---
title: Reverse Engineering-EF Core
description: Reverse Engineering di un modello da un database esistente usando Entity Framework Core
author: bricelam
ms.date: 11/13/2018
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 4cad43f4b1300e1ef3cb31e60d3e9890826d974d
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983573"
---
# <a name="reverse-engineering"></a><span data-ttu-id="25c5b-103"> Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="25c5b-103">Reverse Engineering</span></span>

<span data-ttu-id="25c5b-104">Il Reverse Engineering è il processo di impalcature di classi di tipi di entità e una classe DbContext basata su uno schema di database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-104">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="25c5b-105">Può essere eseguita usando il `Scaffold-DbContext` comando degli strumenti della console di gestione pacchetti di EF core o del `dotnet ef dbcontext scaffold` comando degli strumenti dell'interfaccia della riga di comando di .NET (CLI).</span><span class="sxs-lookup"><span data-stu-id="25c5b-105">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="25c5b-106">Installazione</span><span class="sxs-lookup"><span data-stu-id="25c5b-106">Installing</span></span>

<span data-ttu-id="25c5b-107">Prima di reverse engineering, è necessario installare gli strumenti di [PMC](xref:core/cli/powershell) (solo Visual Studio) o gli [strumenti dell'interfaccia](xref:core/cli/dotnet)della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="25c5b-107">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/cli/dotnet).</span></span> <span data-ttu-id="25c5b-108">Per informazioni dettagliate, vedere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="25c5b-108">See links for details.</span></span>

<span data-ttu-id="25c5b-109">Sarà inoltre necessario installare un [provider di database](xref:core/providers/index) appropriato per lo schema del database che si desidera decodificare.</span><span class="sxs-lookup"><span data-stu-id="25c5b-109">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="25c5b-110">Stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="25c5b-110">Connection string</span></span>

<span data-ttu-id="25c5b-111">Il primo argomento del comando è una stringa di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-111">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="25c5b-112">Questa stringa di connessione verrà utilizzata dagli strumenti per leggere lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-112">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="25c5b-113">La modalità di citazione e di escape della stringa di connessione dipende dalla shell utilizzata per eseguire il comando.</span><span class="sxs-lookup"><span data-stu-id="25c5b-113">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="25c5b-114">Per informazioni dettagliate, vedere la documentazione della shell.</span><span class="sxs-lookup"><span data-stu-id="25c5b-114">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="25c5b-115">PowerShell, ad esempio, richiede l'escape del `$` carattere, ma non `\` .</span><span class="sxs-lookup"><span data-stu-id="25c5b-115">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="25c5b-116">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="25c5b-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="25c5b-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25c5b-117">Visual Studio</span></span>](#tab/vs)

```powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="25c5b-118">Configurazione e segreti utente</span><span class="sxs-lookup"><span data-stu-id="25c5b-118">Configuration and User Secrets</span></span>

<span data-ttu-id="25c5b-119">Se si dispone di un progetto di ASP.NET Core, è possibile utilizzare la `Name=<connection-string>` sintassi per leggere la stringa di connessione dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="25c5b-119">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="25c5b-120">Questo funziona bene con lo [strumento di gestione dei segreti](/aspnet/core/security/app-secrets#secret-manager) per evitare che la password del database sia separata dalla codebase.</span><span class="sxs-lookup"><span data-stu-id="25c5b-120">This works well with the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings:Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings:Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="25c5b-121">Nome provider</span><span class="sxs-lookup"><span data-stu-id="25c5b-121">Provider name</span></span>

<span data-ttu-id="25c5b-122">Il secondo argomento è il nome del provider.</span><span class="sxs-lookup"><span data-stu-id="25c5b-122">The second argument is the provider name.</span></span> <span data-ttu-id="25c5b-123">Il nome del provider è in genere uguale al nome del pacchetto NuGet del provider.</span><span class="sxs-lookup"><span data-stu-id="25c5b-123">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="25c5b-124">Specifica di tabelle</span><span class="sxs-lookup"><span data-stu-id="25c5b-124">Specifying tables</span></span>

<span data-ttu-id="25c5b-125">Per impostazione predefinita, tutte le tabelle nello schema del database sono decodificate in tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="25c5b-125">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="25c5b-126">È possibile limitare le tabelle decodificate specificando schemi e tabelle.</span><span class="sxs-lookup"><span data-stu-id="25c5b-126">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="25c5b-127">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="25c5b-127">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="25c5b-128">L' `--schema` opzione può essere utilizzata per includere ogni tabella all'interno di uno schema, mentre `--table` può essere utilizzata per includere tabelle specifiche.</span><span class="sxs-lookup"><span data-stu-id="25c5b-128">The `--schema` option can be used to include every table within a schema, while `--table` can be used to include specific tables.</span></span>

<span data-ttu-id="25c5b-129">Per includere più tabelle, specificare l'opzione più volte:</span><span class="sxs-lookup"><span data-stu-id="25c5b-129">To include multiple tables, specify the option multiple times:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[<span data-ttu-id="25c5b-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25c5b-130">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="25c5b-131">L' `-Schemas` opzione può essere utilizzata per includere ogni tabella all'interno di uno schema, mentre `-Tables` può essere utilizzata per includere tabelle specifiche.</span><span class="sxs-lookup"><span data-stu-id="25c5b-131">The `-Schemas` option can be used to include every table within a schema, while `-Tables` can be used to include specific tables.</span></span>

<span data-ttu-id="25c5b-132">Per includere più tabelle, usare una matrice:</span><span class="sxs-lookup"><span data-stu-id="25c5b-132">To include multiple tables, use an array:</span></span>

```powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="25c5b-133">\*\*_</span><span class="sxs-lookup"><span data-stu-id="25c5b-133">\*\*_</span></span>

## <a name="preserving-names"></a><span data-ttu-id="25c5b-134">Conservazione dei nomi</span><span class="sxs-lookup"><span data-stu-id="25c5b-134">Preserving names</span></span>

<span data-ttu-id="25c5b-135">Per impostazione predefinita, i nomi delle tabelle e delle colonne sono corretti per una migliore corrispondenza con le convenzioni di denominazione .NET per i tipi e le proprietà.</span><span class="sxs-lookup"><span data-stu-id="25c5b-135">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="25c5b-136">`-UseDatabaseNames`Se si specifica l'opzione in PMC o l' `--use-database-names` opzione nel interfaccia della riga di comando di .NET Core, questo comportamento verrà disabilitato nel modo più possibile mantenendo i nomi originali del database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-136">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the .NET Core CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="25c5b-137">Gli identificatori .NET non validi verranno comunque corretti e i nomi sintetizzati, come le proprietà di navigazione, saranno comunque conformi alle convenzioni di denominazione .NET.</span><span class="sxs-lookup"><span data-stu-id="25c5b-137">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="25c5b-138">API Fluent o annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="25c5b-138">Fluent API or Data Annotations</span></span>

<span data-ttu-id="25c5b-139">Per impostazione predefinita, i tipi di entità vengono configurati tramite l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="25c5b-139">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="25c5b-140">Specificare `-DataAnnotations` (PMC) o `--data-annotations` (interfaccia della riga di comando di .NET Core) per usare invece le annotazioni dei dati, quando possibile.</span><span class="sxs-lookup"><span data-stu-id="25c5b-140">Specify `-DataAnnotations` (PMC) or `--data-annotations` (.NET Core CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="25c5b-141">Se ad esempio si usa l'API Fluent, l'impalcatura è la seguente:</span><span class="sxs-lookup"><span data-stu-id="25c5b-141">For example, using the Fluent API will scaffold this:</span></span>

```csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="25c5b-142">Quando si usano le annotazioni dei dati, questo è l'impalcatura:</span><span class="sxs-lookup"><span data-stu-id="25c5b-142">While using Data Annotations will scaffold this:</span></span>

```csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="25c5b-143">Nome DbContext</span><span class="sxs-lookup"><span data-stu-id="25c5b-143">DbContext name</span></span>

<span data-ttu-id="25c5b-144">Il nome della classe DbContext con impalcature sarà il nome del database con suffisso _Context \* per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="25c5b-144">The scaffolded DbContext class name will be the name of the database suffixed with _Context\* by default.</span></span> <span data-ttu-id="25c5b-145">Per specificarne uno diverso, usare `-Context` in PMC e `--context` nel interfaccia della riga di comando di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="25c5b-145">To specify a different one, use `-Context` in PMC and `--context` in the .NET Core CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="25c5b-146">Directory e spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="25c5b-146">Directories and namespaces</span></span>

<span data-ttu-id="25c5b-147">Le classi di entità e una classe DbContext sono impalcature nella directory radice del progetto e usano lo spazio dei nomi predefinito del progetto.</span><span class="sxs-lookup"><span data-stu-id="25c5b-147">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="25c5b-148">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="25c5b-148">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="25c5b-149">È possibile specificare la directory in cui le classi sono con impalcature usando `--output-dir` e `--context-dir` possono essere usate per eseguire il patibolo della classe DbContext in una directory separata dalle classi del tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="25c5b-149">You can specify the directory where classes are scaffolded using `--output-dir`, and `--context-dir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

<span data-ttu-id="25c5b-150">Per impostazione predefinita, lo spazio dei nomi sarà lo spazio dei nomi radice più i nomi di tutte le sottodirectory nella directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="25c5b-150">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="25c5b-151">Tuttavia, a partire da EFCore 5,0, è possibile eseguire l'override dello spazio dei nomi per tutte le classi di output usando `--namespace` .</span><span class="sxs-lookup"><span data-stu-id="25c5b-151">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `--namespace`.</span></span> <span data-ttu-id="25c5b-152">È anche possibile eseguire l'override dello spazio dei nomi solo per la classe DbContext usando `--context-namespace` :</span><span class="sxs-lookup"><span data-stu-id="25c5b-152">You can also override the namespace for just the DbContext class using `--context-namespace`:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="25c5b-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25c5b-153">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="25c5b-154">È possibile specificare la directory in cui le classi sono con impalcature usando `-OutputDir` e `-ContextDir` possono essere usate per eseguire il patibolo della classe DbContext in una directory separata dalle classi del tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="25c5b-154">You can specify the directory where classes are scaffolded using `-OutputDir`, and `-ContextDir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

<span data-ttu-id="25c5b-155">Per impostazione predefinita, lo spazio dei nomi sarà lo spazio dei nomi radice più i nomi di tutte le sottodirectory nella directory radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="25c5b-155">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="25c5b-156">Tuttavia, a partire da EFCore 5,0, è possibile eseguire l'override dello spazio dei nomi per tutte le classi di output usando `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="25c5b-156">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `-Namespace`.</span></span> <span data-ttu-id="25c5b-157">È anche possibile eseguire l'override dello spazio dei nomi solo per la classe DbContext usando `-ContextNamespace` .</span><span class="sxs-lookup"><span data-stu-id="25c5b-157">You can also override the namespace for just the DbContext class using `-ContextNamespace`.</span></span>

```powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a><span data-ttu-id="25c5b-158">Funzionamento</span><span class="sxs-lookup"><span data-stu-id="25c5b-158">How it works</span></span>

<span data-ttu-id="25c5b-159">Il reverse engineering inizia con la lettura dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-159">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="25c5b-160">Legge le informazioni su tabelle, colonne, vincoli e indici.</span><span class="sxs-lookup"><span data-stu-id="25c5b-160">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="25c5b-161">USA quindi le informazioni sullo schema per creare un modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="25c5b-161">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="25c5b-162">Le tabelle vengono utilizzate per creare i tipi di entità. le colonne vengono utilizzate per creare le proprietà; e le chiavi esterne vengono utilizzate per creare relazioni.</span><span class="sxs-lookup"><span data-stu-id="25c5b-162">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="25c5b-163">Infine, il modello viene utilizzato per generare il codice.</span><span class="sxs-lookup"><span data-stu-id="25c5b-163">Finally, the model is used to generate code.</span></span> <span data-ttu-id="25c5b-164">Le classi del tipo di entità, l'API Fluent e le annotazioni dei dati corrispondenti sono basate su impalcature per ricreare lo stesso modello dall'app.</span><span class="sxs-lookup"><span data-stu-id="25c5b-164">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="25c5b-165">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="25c5b-165">Limitations</span></span>

* <span data-ttu-id="25c5b-166">Non tutti gli elementi di un modello possono essere rappresentati utilizzando uno schema di database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-166">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="25c5b-167">Ad esempio, le informazioni sulle [**gerarchie di ereditarietà**](xref:core/modeling/inheritance), i [**tipi di proprietà**](xref:core/modeling/owned-entities)e la suddivisione delle [**tabelle**](xref:core/modeling/table-splitting) non sono presenti nello schema del database.</span><span class="sxs-lookup"><span data-stu-id="25c5b-167">For example, information about [**inheritance hierarchies**](xref:core/modeling/inheritance), [**owned types**](xref:core/modeling/owned-entities), and [**table splitting**](xref:core/modeling/table-splitting) are not present in the database schema.</span></span> <span data-ttu-id="25c5b-168">Per questo motivo, questi costrutti non verranno mai decodificati.</span><span class="sxs-lookup"><span data-stu-id="25c5b-168">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="25c5b-169">Inoltre, **alcuni tipi di colonna** potrebbero non essere supportati dal provider EF core.</span><span class="sxs-lookup"><span data-stu-id="25c5b-169">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="25c5b-170">Queste colonne non verranno incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="25c5b-170">These columns won't be included in the model.</span></span>
* <span data-ttu-id="25c5b-171">È possibile definire i [**token di concorrenza**](xref:core/modeling/concurrency)in un modello di EF core per impedire a due utenti di aggiornare la stessa entità nello stesso momento.</span><span class="sxs-lookup"><span data-stu-id="25c5b-171">You can define [**concurrency tokens**](xref:core/modeling/concurrency), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="25c5b-172">Alcuni database hanno un tipo speciale per rappresentare questo tipo di colonna (ad esempio, rowversion in SQL Server), nel qual caso è possibile decompilare queste informazioni; Tuttavia, altri token di concorrenza non verranno decodificati.</span><span class="sxs-lookup"><span data-stu-id="25c5b-172">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="25c5b-173">[La funzionalità del tipo di riferimento Nullable di C# 8](/dotnet/csharp/tutorials/nullable-reference-types) non è attualmente supportata in reverse engineering: EF core genera sempre il codice c# che presuppone che la funzionalità sia disabilitata.</span><span class="sxs-lookup"><span data-stu-id="25c5b-173">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="25c5b-174">Ad esempio, le colonne di testo Nullable verranno sottoposto a impalcatura come proprietà con tipo `string` , non `string?` con l'API Fluent o le annotazioni dei dati utilizzate per configurare se una proprietà è obbligatoria o meno.</span><span class="sxs-lookup"><span data-stu-id="25c5b-174">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="25c5b-175">È possibile modificare il codice con impalcature e sostituirle con annotazioni di supporto per i valori null C#.</span><span class="sxs-lookup"><span data-stu-id="25c5b-175">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="25c5b-176">Il supporto dell'impalcatura per i tipi di riferimento nullable viene rilevato da Issue [#15520](https://github.com/dotnet/efcore/issues/15520).</span><span class="sxs-lookup"><span data-stu-id="25c5b-176">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/dotnet/efcore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="25c5b-177">Personalizzazione del modello</span><span class="sxs-lookup"><span data-stu-id="25c5b-177">Customizing the model</span></span>

<span data-ttu-id="25c5b-178">Il codice generato da EF Core è il codice.</span><span class="sxs-lookup"><span data-stu-id="25c5b-178">The code generated by EF Core is your code.</span></span> <span data-ttu-id="25c5b-179">È possibile modificarlo.</span><span class="sxs-lookup"><span data-stu-id="25c5b-179">Feel free to change it.</span></span> <span data-ttu-id="25c5b-180">Verrà rigenerato solo se si esegue nuovamente la decompilazione dello stesso modello.</span><span class="sxs-lookup"><span data-stu-id="25c5b-180">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="25c5b-181">Il codice con impalcatura rappresenta *un* modello che può essere utilizzato per accedere al database, ma non è certo l' *unico* modello che può essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="25c5b-181">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="25c5b-182">Personalizzare le classi del tipo di entità e la classe DbContext in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="25c5b-182">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="25c5b-183">È ad esempio possibile scegliere di rinominare tipi e proprietà, introdurre gerarchie di ereditarietà o suddividere una tabella in più entità.</span><span class="sxs-lookup"><span data-stu-id="25c5b-183">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="25c5b-184">È inoltre possibile rimuovere indici non univoci, sequenze inutilizzate e proprietà di navigazione, proprietà scalari facoltative e nomi di vincoli dal modello.</span><span class="sxs-lookup"><span data-stu-id="25c5b-184">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="25c5b-185">È anche possibile aggiungere ulteriori costruttori, metodi, proprietà e così via.</span><span class="sxs-lookup"><span data-stu-id="25c5b-185">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="25c5b-186">uso di un'altra classe parziale in un file separato.</span><span class="sxs-lookup"><span data-stu-id="25c5b-186">using another partial class in a separate file.</span></span> <span data-ttu-id="25c5b-187">Questo approccio funziona anche quando si intende decodificare nuovamente il modello.</span><span class="sxs-lookup"><span data-stu-id="25c5b-187">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="25c5b-188">Aggiornamento del modello</span><span class="sxs-lookup"><span data-stu-id="25c5b-188">Updating the model</span></span>

<span data-ttu-id="25c5b-189">Dopo aver apportato modifiche al database, potrebbe essere necessario aggiornare il modello di EF Core per riflettere tali modifiche.</span><span class="sxs-lookup"><span data-stu-id="25c5b-189">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="25c5b-190">Se le modifiche apportate al database sono semplici, potrebbe essere più semplice apportare manualmente le modifiche al modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="25c5b-190">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="25c5b-191">Ad esempio, la ridenominazione di una tabella o di una colonna, la rimozione di una colonna o l'aggiornamento del tipo di una colonna sono semplici modifiche da apportare nel codice.</span><span class="sxs-lookup"><span data-stu-id="25c5b-191">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="25c5b-192">Le modifiche più significative, tuttavia, non sono semplici da creare manualmente.</span><span class="sxs-lookup"><span data-stu-id="25c5b-192">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="25c5b-193">Un flusso di lavoro comune è quello di decodificare il modello dal database usando `-Force` (PMC) o `--force` (CLI) per sovrascrivere il modello esistente con uno aggiornato.</span><span class="sxs-lookup"><span data-stu-id="25c5b-193">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="25c5b-194">Un'altra funzionalità comunemente richiesta è la possibilità di aggiornare il modello dal database mantenendo la personalizzazione, ad esempio le rinominazioni, le gerarchie dei tipi e così via. Utilizzare Issue [#831](https://github.com/dotnet/efcore/issues/831) per tenere traccia dello stato di avanzamento di questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="25c5b-194">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/dotnet/efcore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="25c5b-195">Se si esegue di nuovo la decompilazione del modello dal database, tutte le modifiche apportate ai file andranno perse.</span><span class="sxs-lookup"><span data-stu-id="25c5b-195">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
