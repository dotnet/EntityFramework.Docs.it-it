---
title: Applicazione delle migrazioni-EF Core
description: Strategie per l'applicazione delle migrazioni dello schema ai database di produzione e di sviluppo con Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 68d482a34e5f5c7acf968acdfd8825e1d21ecb13
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062347"
---
# <a name="applying-migrations"></a><span data-ttu-id="b05b9-103">Applicazione di migrazioni</span><span class="sxs-lookup"><span data-stu-id="b05b9-103">Applying Migrations</span></span>

<span data-ttu-id="b05b9-104">Una volta aggiunte le migrazioni, è necessario distribuirle e applicarle ai database.</span><span class="sxs-lookup"><span data-stu-id="b05b9-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="b05b9-105">Per eseguire questa operazione sono disponibili diverse strategie, con alcune più appropriate per gli ambienti di produzione e altre per il ciclo di vita dello sviluppo.</span><span class="sxs-lookup"><span data-stu-id="b05b9-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="b05b9-106">Indipendentemente dalla strategia di distribuzione, controllare sempre le migrazioni generate e testarle prima di applicare a un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="b05b9-107">Una migrazione può eliminare una colonna quando lo scopo è rinominarla o potrebbe non riuscire per vari motivi quando viene applicata a un database.</span><span class="sxs-lookup"><span data-stu-id="b05b9-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="b05b9-108">Script SQL</span><span class="sxs-lookup"><span data-stu-id="b05b9-108">SQL scripts</span></span>

<span data-ttu-id="b05b9-109">La modalità consigliata per distribuire le migrazioni in un database di produzione è la generazione di script SQL.</span><span class="sxs-lookup"><span data-stu-id="b05b9-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="b05b9-110">I vantaggi di questa strategia includono i seguenti:</span><span class="sxs-lookup"><span data-stu-id="b05b9-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="b05b9-111">È possibile esaminare gli script SQL per verificarne l'accuratezza; Questo aspetto è importante perché l'applicazione delle modifiche dello schema ai database di produzione è un'operazione potenzialmente pericolosa che può implicare la perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="b05b9-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="b05b9-112">In alcuni casi, gli script possono essere ottimizzati per soddisfare le esigenze specifiche di un database di produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="b05b9-113">Gli script SQL possono essere usati insieme a una tecnologia di distribuzione e possono anche essere generati come parte del processo CI.</span><span class="sxs-lookup"><span data-stu-id="b05b9-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="b05b9-114">Gli script SQL possono essere forniti a un amministratore di database e possono essere gestiti e archiviati separatamente.</span><span class="sxs-lookup"><span data-stu-id="b05b9-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b05b9-115">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="b05b9-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="b05b9-116">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="b05b9-116">Basic Usage</span></span>

<span data-ttu-id="b05b9-117">Il codice seguente genera uno script SQL da un database vuoto alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="b05b9-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="b05b9-118">Con from (destinazione implicita)</span><span class="sxs-lookup"><span data-stu-id="b05b9-118">With From (to implied)</span></span>

<span data-ttu-id="b05b9-119">Il codice seguente genera uno script SQL dalla migrazione specificata alla migrazione più recente.</span><span class="sxs-lookup"><span data-stu-id="b05b9-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="b05b9-120">Con from e to</span><span class="sxs-lookup"><span data-stu-id="b05b9-120">With From and To</span></span>

<span data-ttu-id="b05b9-121">Il codice seguente genera uno script SQL dalla `from` migrazione specificata alla migrazione specificata `to` .</span><span class="sxs-lookup"><span data-stu-id="b05b9-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="b05b9-122">È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback.</span><span class="sxs-lookup"><span data-stu-id="b05b9-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="b05b9-123">Tenere conto degli scenari con potenziale perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="b05b9-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="b05b9-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b05b9-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="b05b9-125">Utilizzo di base</span><span class="sxs-lookup"><span data-stu-id="b05b9-125">Basic Usage</span></span>

<span data-ttu-id="b05b9-126">Il codice seguente genera uno script SQL da un database vuoto alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="b05b9-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="b05b9-127">Con from (destinazione implicita)</span><span class="sxs-lookup"><span data-stu-id="b05b9-127">With From (to implied)</span></span>

<span data-ttu-id="b05b9-128">Il codice seguente genera uno script SQL dalla migrazione specificata alla migrazione più recente.</span><span class="sxs-lookup"><span data-stu-id="b05b9-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="b05b9-129">Con from e to</span><span class="sxs-lookup"><span data-stu-id="b05b9-129">With From and To</span></span>

<span data-ttu-id="b05b9-130">Il codice seguente genera uno script SQL dalla `from` migrazione specificata alla migrazione specificata `to` .</span><span class="sxs-lookup"><span data-stu-id="b05b9-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="b05b9-131">È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback.</span><span class="sxs-lookup"><span data-stu-id="b05b9-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="b05b9-132">*Tenere conto degli scenari con potenziale perdita di dati.*</span><span class="sxs-lookup"><span data-stu-id="b05b9-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="b05b9-133">La generazione di script accetta i due argomenti seguenti in indica l'intervallo di migrazioni da generare:</span><span class="sxs-lookup"><span data-stu-id="b05b9-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="b05b9-134">La migrazione **di origine** deve essere l'ultima migrazione applicata al database prima dell'esecuzione dello script.</span><span class="sxs-lookup"><span data-stu-id="b05b9-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="b05b9-135">Se non è stata applicata alcuna migrazione, specificare `0` (valore predefinito).</span><span class="sxs-lookup"><span data-stu-id="b05b9-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="b05b9-136">La migrazione **di destinazione** è l'ultima migrazione applicata al database dopo l'esecuzione dello script.</span><span class="sxs-lookup"><span data-stu-id="b05b9-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="b05b9-137">L'impostazione predefinita corrisponde all'ultima migrazione nel progetto.</span><span class="sxs-lookup"><span data-stu-id="b05b9-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="b05b9-138">Script SQL idempotente</span><span class="sxs-lookup"><span data-stu-id="b05b9-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="b05b9-139">Gli script SQL generati in precedenza possono essere applicati solo per modificare lo schema da una migrazione a un'altra. è responsabilità dell'utente applicare lo script in modo appropriato e solo al database nello stato di migrazione corretto.</span><span class="sxs-lookup"><span data-stu-id="b05b9-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="b05b9-140">EF Core supporta anche la generazione di script **idempotente** , che controllano internamente le migrazioni già applicate (tramite la tabella di cronologia delle migrazioni) e applicano solo quelle mancanti.</span><span class="sxs-lookup"><span data-stu-id="b05b9-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="b05b9-141">Questa operazione è utile se non si conosce esattamente l'ultima migrazione applicata al database o se si esegue la distribuzione in più database che possono essere in una migrazione diversa.</span><span class="sxs-lookup"><span data-stu-id="b05b9-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="b05b9-142">Il codice seguente genera migrazioni idempotente:</span><span class="sxs-lookup"><span data-stu-id="b05b9-142">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="b05b9-143">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="b05b9-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="b05b9-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b05b9-144">Visual Studio</span></span>](#tab/vs)

```powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="b05b9-145">Strumenti da riga di comando</span><span class="sxs-lookup"><span data-stu-id="b05b9-145">Command-line tools</span></span>

<span data-ttu-id="b05b9-146">Gli strumenti da riga di comando EF possono essere usati per applicare le migrazioni a un database.</span><span class="sxs-lookup"><span data-stu-id="b05b9-146">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="b05b9-147">Sebbene sia produttivo per lo sviluppo e il test locali delle migrazioni, questo approccio non è ideale per la gestione dei database di produzione:</span><span class="sxs-lookup"><span data-stu-id="b05b9-147">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="b05b9-148">I comandi SQL vengono applicati direttamente dallo strumento, senza concedere allo sviluppatore la possibilità di ispezionarli o modificarli.</span><span class="sxs-lookup"><span data-stu-id="b05b9-148">The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="b05b9-149">Questa situazione può essere pericolosa in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-149">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="b05b9-150">.NET SDK e lo strumento EF devono essere installati nei server di produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-150">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b05b9-151">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="b05b9-151">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="b05b9-152">Il codice seguente consente di aggiornare il database alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="b05b9-152">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="b05b9-153">Il codice seguente consente di aggiornare il database a una determinata migrazione:</span><span class="sxs-lookup"><span data-stu-id="b05b9-153">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="b05b9-154">Si noti che questa operazione può essere usata anche per eseguire il rollback a una migrazione precedente.</span><span class="sxs-lookup"><span data-stu-id="b05b9-154">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="b05b9-155">Tenere conto degli scenari con potenziale perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="b05b9-155">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="b05b9-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b05b9-156">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="b05b9-157">Il codice seguente consente di aggiornare il database alla migrazione più recente:</span><span class="sxs-lookup"><span data-stu-id="b05b9-157">The following updates your database to the latest migration:</span></span>

```powershell
Update-Database
```

<span data-ttu-id="b05b9-158">Il codice seguente consente di aggiornare il database a una determinata migrazione:</span><span class="sxs-lookup"><span data-stu-id="b05b9-158">The following updates your database to a given migration:</span></span>

```powershell
Update-Database AddNewTables
```

<span data-ttu-id="b05b9-159">Si noti che questa operazione può essere usata anche per eseguire il rollback a una migrazione precedente.</span><span class="sxs-lookup"><span data-stu-id="b05b9-159">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="b05b9-160">Tenere conto degli scenari con potenziale perdita di dati.</span><span class="sxs-lookup"><span data-stu-id="b05b9-160">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="b05b9-161">Per ulteriori informazioni sull'applicazione delle migrazioni tramite gli strumenti da riga di comando, vedere la Guida di [riferimento agli strumenti EF Core](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="b05b9-161">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="b05b9-162">Applicare migrazioni al runtime</span><span class="sxs-lookup"><span data-stu-id="b05b9-162">Apply migrations at runtime</span></span>

<span data-ttu-id="b05b9-163">È possibile che l'applicazione stessa applichi le migrazioni a livello di codice, in genere durante l'avvio.</span><span class="sxs-lookup"><span data-stu-id="b05b9-163">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="b05b9-164">Sebbene sia produttivo per lo sviluppo e il test locali delle migrazioni, questo approccio non è appropriato per la gestione dei database di produzione, per i motivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="b05b9-164">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="b05b9-165">Se sono in esecuzione più istanze dell'applicazione, entrambe le applicazioni potrebbero tentare di applicare la migrazione simultaneamente e non riuscire (o peggiori, causare il danneggiamento dei dati).</span><span class="sxs-lookup"><span data-stu-id="b05b9-165">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="b05b9-166">Analogamente, se un'applicazione accede al database mentre viene eseguita da un'altra applicazione, ciò può causare gravi problemi.</span><span class="sxs-lookup"><span data-stu-id="b05b9-166">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="b05b9-167">Per modificare lo schema del database, l'applicazione deve disporre di accesso con privilegi elevati.</span><span class="sxs-lookup"><span data-stu-id="b05b9-167">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="b05b9-168">È in genere consigliabile limitare le autorizzazioni per il database dell'applicazione in produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-168">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="b05b9-169">È importante essere in grado di eseguire il rollback di una migrazione applicata in caso di problemi.</span><span class="sxs-lookup"><span data-stu-id="b05b9-169">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="b05b9-170">Le altre strategie forniscono questa operazione in modo semplice e rapido.</span><span class="sxs-lookup"><span data-stu-id="b05b9-170">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="b05b9-171">I comandi SQL vengono applicati direttamente dal programma, senza concedere allo sviluppatore la possibilità di ispezionarli o modificarli.</span><span class="sxs-lookup"><span data-stu-id="b05b9-171">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="b05b9-172">Questa situazione può essere pericolosa in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-172">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="b05b9-173">Per applicare le migrazioni a livello di codice, chiamare `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="b05b9-173">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="b05b9-174">Ad esempio, un'applicazione ASP.NET tipica può eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b05b9-174">For example, a typical ASP.NET application can do the following:</span></span>

```csharp
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

<span data-ttu-id="b05b9-175">Si noti che si `Migrate()` basa sul `IMigrator` servizio, che può essere usato per scenari più avanzati.</span><span class="sxs-lookup"><span data-stu-id="b05b9-175">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="b05b9-176">Usare `myDbContext.GetInfrastructure().GetService<IMigrator>()` per accedervi.</span><span class="sxs-lookup"><span data-stu-id="b05b9-176">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="b05b9-177">Valutare attentamente prima di usare questo approccio nell'ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="b05b9-177">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="b05b9-178">L'esperienza ha dimostrato che la semplicità di questa strategia di distribuzione è stata superata dai problemi che crea.</span><span class="sxs-lookup"><span data-stu-id="b05b9-178">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="b05b9-179">Si consiglia di generare script SQL dalle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="b05b9-179">Consider generating SQL scripts from migrations instead.</span></span>
> * <span data-ttu-id="b05b9-180">Non chiamare `EnsureCreated()` prima di `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="b05b9-180">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="b05b9-181">`EnsureCreated()` ignora la cartella Migrations per creare lo schema, causando un errore di `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="b05b9-181">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
