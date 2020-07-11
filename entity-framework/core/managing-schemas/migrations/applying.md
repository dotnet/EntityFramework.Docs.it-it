---
title: Applicazione delle migrazioni-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238759"
---
# <a name="applying-migrations"></a><span data-ttu-id="955ef-102">Applicazione di migrazioni</span><span class="sxs-lookup"><span data-stu-id="955ef-102">Applying Migrations</span></span>

<span data-ttu-id="955ef-103">Una volta aggiunte le migrazioni, è necessario distribuirle e applicarle ai database.</span><span class="sxs-lookup"><span data-stu-id="955ef-103">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="955ef-104">Per eseguire questa operazione sono disponibili diverse strategie, con alcune più appropriate per gli ambienti di produzione e altre per il ciclo di vita dello sviluppo.</span><span class="sxs-lookup"><span data-stu-id="955ef-104">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="955ef-105">Indipendentemente dalla strategia di distribuzione, controllare sempre le migrazioni generate e testarle prima di applicare a un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-105">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="955ef-106">Una migrazione può eliminare una colonna quando lo scopo è rinominarla o potrebbe non riuscire per vari motivi quando viene applicata a un database.</span><span class="sxs-lookup"><span data-stu-id="955ef-106">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="955ef-107">Script SQL</span><span class="sxs-lookup"><span data-stu-id="955ef-107">SQL scripts</span></span>

<span data-ttu-id="955ef-108">La modalità consigliata per distribuire le migrazioni in un database di produzione è la generazione di script SQL.</span><span class="sxs-lookup"><span data-stu-id="955ef-108">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="955ef-109">I vantaggi di questa strategia includono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="955ef-109">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="955ef-110">È possibile esaminare gli script SQL per verificarne l'accuratezza; Questo aspetto è importante perché l'applicazione delle modifiche dello schema ai database di produzione è un'operazione potenzialmente pericolosa che può implicare la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="955ef-110">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="955ef-111">In alcuni casi, gli script possono essere ottimizzati per soddisfare le esigenze specifiche di un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-111">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="955ef-112">Gli script SQL possono essere usati insieme a una tecnologia di distribuzione e possono anche essere generati come parte del processo CI.</span><span class="sxs-lookup"><span data-stu-id="955ef-112">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="955ef-113">Gli script SQL possono essere forniti a un amministratore di database e possono essere gestiti e archiviati separatamente.</span><span class="sxs-lookup"><span data-stu-id="955ef-113">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="955ef-114">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="955ef-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="955ef-115">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="955ef-115">Basic Usage</span></span>

<span data-ttu-id="955ef-116">Il codice seguente genera uno script SQL da un database vuoto alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="955ef-116">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="955ef-117">Con from (destinazione implicita)</span><span class="sxs-lookup"><span data-stu-id="955ef-117">With From (to implied)</span></span>

<span data-ttu-id="955ef-118">Il codice seguente genera uno script SQL dalla migrazione specificata alla migrazione più recente.</span><span class="sxs-lookup"><span data-stu-id="955ef-118">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="955ef-119">Con from e to</span><span class="sxs-lookup"><span data-stu-id="955ef-119">With From and To</span></span>

<span data-ttu-id="955ef-120">Il codice seguente genera uno script SQL dalla `from` migrazione specificata alla migrazione specificata `to` .</span><span class="sxs-lookup"><span data-stu-id="955ef-120">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="955ef-121">È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback.</span><span class="sxs-lookup"><span data-stu-id="955ef-121">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="955ef-122">Tenere conto degli scenari con potenziale perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="955ef-122">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="955ef-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ef-123">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="955ef-124">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="955ef-124">Basic Usage</span></span>

<span data-ttu-id="955ef-125">Il codice seguente genera uno script SQL da un database vuoto alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="955ef-125">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="955ef-126">Con from (destinazione implicita)</span><span class="sxs-lookup"><span data-stu-id="955ef-126">With From (to implied)</span></span>

<span data-ttu-id="955ef-127">Il codice seguente genera uno script SQL dalla migrazione specificata alla migrazione più recente.</span><span class="sxs-lookup"><span data-stu-id="955ef-127">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="955ef-128">Con from e to</span><span class="sxs-lookup"><span data-stu-id="955ef-128">With From and To</span></span>

<span data-ttu-id="955ef-129">Il codice seguente genera uno script SQL dalla `from` migrazione specificata alla migrazione specificata `to` .</span><span class="sxs-lookup"><span data-stu-id="955ef-129">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="955ef-130">È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback.</span><span class="sxs-lookup"><span data-stu-id="955ef-130">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="955ef-131">*Tenere conto degli scenari con potenziale perdita di dati.*</span><span class="sxs-lookup"><span data-stu-id="955ef-131">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="955ef-132">La generazione di script accetta i due argomenti seguenti in indica l'intervallo di migrazioni da generare:</span><span class="sxs-lookup"><span data-stu-id="955ef-132">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="955ef-133">La migrazione **di origine** deve essere l'ultima migrazione applicata al database prima dell'esecuzione dello script.</span><span class="sxs-lookup"><span data-stu-id="955ef-133">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="955ef-134">Se non è stata applicata alcuna migrazione, specificare `0` (valore predefinito).</span><span class="sxs-lookup"><span data-stu-id="955ef-134">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="955ef-135">La migrazione **di destinazione** è l'ultima migrazione applicata al database dopo l'esecuzione dello script.</span><span class="sxs-lookup"><span data-stu-id="955ef-135">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="955ef-136">L'impostazione predefinita corrisponde all'ultima migrazione nel progetto.</span><span class="sxs-lookup"><span data-stu-id="955ef-136">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="955ef-137">Script SQL idempotente</span><span class="sxs-lookup"><span data-stu-id="955ef-137">Idempotent SQL scripts</span></span>

<span data-ttu-id="955ef-138">Gli script SQL generati in precedenza possono essere applicati solo per modificare lo schema da una migrazione a un'altra. è responsabilità dell'utente applicare lo script in modo appropriato e solo al database nello stato di migrazione corretto.</span><span class="sxs-lookup"><span data-stu-id="955ef-138">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="955ef-139">EF Core supporta anche la generazione di script **idempotente** , che controllano internamente le migrazioni già applicate (tramite la tabella di cronologia delle migrazioni) e applicano solo quelle mancanti.</span><span class="sxs-lookup"><span data-stu-id="955ef-139">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="955ef-140">Questa operazione è utile se non si conosce esattamente l'ultima migrazione applicata al database o se si esegue la distribuzione in più database che possono essere in una migrazione diversa.</span><span class="sxs-lookup"><span data-stu-id="955ef-140">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="955ef-141">Il codice seguente genera migrazioni idempotente:</span><span class="sxs-lookup"><span data-stu-id="955ef-141">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="955ef-142">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="955ef-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="955ef-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ef-143">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="955ef-144">Strumenti da riga di comando</span><span class="sxs-lookup"><span data-stu-id="955ef-144">Command-line tools</span></span>

<span data-ttu-id="955ef-145">Gli strumenti da riga di comando EF possono essere usati per applicare le migrazioni a un database.</span><span class="sxs-lookup"><span data-stu-id="955ef-145">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="955ef-146">Sebbene sia produttivo per lo sviluppo e il test locali delle migrazioni, questo approccio non è ideale per la gestione dei database di produzione:</span><span class="sxs-lookup"><span data-stu-id="955ef-146">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="955ef-147">I comandi SQL vengono applicati direttamente dallo strumento, senza concedere allo sviluppatore una modifica da ispezionare o modificare.</span><span class="sxs-lookup"><span data-stu-id="955ef-147">The SQL commands are applied directly by the tool, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="955ef-148">Questa situazione può essere pericolosa in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-148">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="955ef-149">.NET SDK e lo strumento EF devono essere installati nei server di produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-149">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="955ef-150">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="955ef-150">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="955ef-151">Il codice seguente consente di aggiornare il database alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="955ef-151">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="955ef-152">Il codice seguente consente di aggiornare il database a una determinata migrazione:</span><span class="sxs-lookup"><span data-stu-id="955ef-152">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="955ef-153">Si noti che questa operazione può essere usata anche per eseguire il rollback a una migrazione precedente.</span><span class="sxs-lookup"><span data-stu-id="955ef-153">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="955ef-154">Tenere conto degli scenari con potenziale perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="955ef-154">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="955ef-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ef-155">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="955ef-156">Il codice seguente consente di aggiornare il database alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="955ef-156">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="955ef-157">Il codice seguente consente di aggiornare il database a una determinata migrazione:</span><span class="sxs-lookup"><span data-stu-id="955ef-157">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="955ef-158">Si noti che questa operazione può essere usata anche per eseguire il rollback a una migrazione precedente.</span><span class="sxs-lookup"><span data-stu-id="955ef-158">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="955ef-159">Tenere conto degli scenari con potenziale perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="955ef-159">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="955ef-160">Per ulteriori informazioni sull'applicazione delle migrazioni tramite gli strumenti da riga di comando, vedere la Guida di [riferimento agli strumenti EF Core](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="955ef-160">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="955ef-161">Applicare migrazioni al runtime</span><span class="sxs-lookup"><span data-stu-id="955ef-161">Apply migrations at runtime</span></span>

<span data-ttu-id="955ef-162">È possibile che l'applicazione stessa applichi le migrazioni a livello di codice, in genere durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="955ef-162">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="955ef-163">Sebbene sia produttivo per lo sviluppo e il test locali delle migrazioni, questo approccio non è appropriato per la gestione dei database di produzione, per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="955ef-163">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="955ef-164">Se sono in esecuzione più istanze dell'applicazione, entrambe le applicazioni potrebbero tentare di applicare la migrazione simultaneamente e non riuscire (o peggiori, causare il danneggiamento dei dati).</span><span class="sxs-lookup"><span data-stu-id="955ef-164">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="955ef-165">Analogamente, se un'applicazione accede al database mentre viene eseguita da un'altra applicazione, ciò può causare gravi problemi.</span><span class="sxs-lookup"><span data-stu-id="955ef-165">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="955ef-166">Per modificare lo schema del database, l'applicazione deve disporre di accesso con privilegi elevati.</span><span class="sxs-lookup"><span data-stu-id="955ef-166">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="955ef-167">È in genere consigliabile limitare le autorizzazioni per il database dell'applicazione in produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-167">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="955ef-168">È importante essere in grado di eseguire il rollback di una migrazione applicata in caso di problemi.</span><span class="sxs-lookup"><span data-stu-id="955ef-168">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="955ef-169">Le altre strategie forniscono questa operazione in modo semplice e rapido.</span><span class="sxs-lookup"><span data-stu-id="955ef-169">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="955ef-170">I comandi SQL vengono applicati direttamente dal programma, senza concedere allo sviluppatore una modifica da ispezionare o modificare.</span><span class="sxs-lookup"><span data-stu-id="955ef-170">The SQL commands are applied directly by the program, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="955ef-171">Questa situazione può essere pericolosa in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-171">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="955ef-172">Per applicare le migrazioni a livello di codice, chiamare `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="955ef-172">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="955ef-173">Ad esempio, un'applicazione ASP.NET tipica può eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="955ef-173">For example, a typical ASP.NET application can do the following:</span></span>

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="955ef-174">Si noti che si `Migrate()` basa sul `IMigrator` servizio, che può essere usato per scenari più avanzati.</span><span class="sxs-lookup"><span data-stu-id="955ef-174">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="955ef-175">Usare `myDbContext.GetInfrastructure().GetService<IMigrator>()` per accedervi.</span><span class="sxs-lookup"><span data-stu-id="955ef-175">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="955ef-176">Valutare attentamente prima di usare questo approccio nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="955ef-176">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="955ef-177">L'esperienza ha dimostrato che la semplicità di questa strategia di distribuzione è stata superata dai problemi che crea.</span><span class="sxs-lookup"><span data-stu-id="955ef-177">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="955ef-178">In alternativa, è consigliabile usare gli script SQL.</span><span class="sxs-lookup"><span data-stu-id="955ef-178">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="955ef-179">Non chiamare `EnsureCreated()` prima di `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="955ef-179">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="955ef-180">`EnsureCreated()` ignora la cartella Migrations per creare lo schema, causando un errore di `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="955ef-180">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
