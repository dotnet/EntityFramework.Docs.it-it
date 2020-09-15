---
title: Reverse Engineering-EF Core
description: Reverse Engineering di un modello da un database esistente usando Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 86aa6d22ebe8e5c1d654c83d4c292a1ed5842ddd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071914"
---
# <a name="reverse-engineering"></a> Reverse Engineering

Il Reverse Engineering è il processo di impalcature di classi di tipi di entità e una classe DbContext basata su uno schema di database. Può essere eseguita usando il `Scaffold-DbContext` comando degli strumenti della console di gestione pacchetti di EF core o del `dotnet ef dbcontext scaffold` comando degli strumenti dell'interfaccia della riga di comando di .NET (CLI).

## <a name="installing"></a>Installazione

Prima di reverse engineering, è necessario installare gli strumenti di [PMC](xref:core/miscellaneous/cli/powershell) (solo Visual Studio) o gli [strumenti dell'interfaccia](xref:core/miscellaneous/cli/dotnet)della riga di comando. Per informazioni dettagliate, vedere i collegamenti.

Sarà inoltre necessario installare un [provider di database](xref:core/providers/index) appropriato per lo schema del database che si desidera decodificare.

## <a name="connection-string"></a>Stringa di connessione

Il primo argomento del comando è una stringa di connessione al database. Questa stringa di connessione verrà utilizzata dagli strumenti per leggere lo schema del database.

La modalità di citazione e di escape della stringa di connessione dipende dalla shell utilizzata per eseguire il comando. Per informazioni dettagliate, vedere la documentazione della shell. PowerShell, ad esempio, richiede l'escape del `$` carattere, ma non `\` .

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a>Configurazione e segreti utente

Se si dispone di un progetto di ASP.NET Core, è possibile utilizzare la `Name=<connection-string>` sintassi per leggere la stringa di connessione dalla configurazione.

Questo funziona bene con lo [strumento di gestione dei segreti](/aspnet/core/security/app-secrets#secret-manager) per evitare che la password del database sia separata dalla codebase.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>Nome provider

Il secondo argomento è il nome del provider. Il nome del provider è in genere uguale al nome del pacchetto NuGet del provider.

## <a name="specifying-tables"></a>Specifica di tabelle

Per impostazione predefinita, tutte le tabelle nello schema del database sono decodificate in tipi di entità. È possibile limitare le tabelle decodificate specificando schemi e tabelle.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

L' `--schema` opzione può essere utilizzata per includere ogni tabella all'interno di uno schema, mentre `--table` può essere utilizzata per includere tabelle specifiche.

Per includere più tabelle, specificare l'opzione più volte:

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

L' `-Schemas` opzione può essere utilizzata per includere ogni tabella all'interno di uno schema, mentre `-Tables` può essere utilizzata per includere tabelle specifiche.

Per includere più tabelle, usare una matrice:

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a>Conservazione dei nomi

Per impostazione predefinita, i nomi delle tabelle e delle colonne sono corretti per una migliore corrispondenza con le convenzioni di denominazione .NET per i tipi e le proprietà. `-UseDatabaseNames`Se si specifica l'opzione in PMC o l' `--use-database-names` opzione nel interfaccia della riga di comando di .NET Core, questo comportamento verrà disabilitato nel modo più possibile mantenendo i nomi originali del database. Gli identificatori .NET non validi verranno comunque corretti e i nomi sintetizzati, come le proprietà di navigazione, saranno comunque conformi alle convenzioni di denominazione .NET.

## <a name="fluent-api-or-data-annotations"></a>API Fluent o annotazioni dei dati

Per impostazione predefinita, i tipi di entità vengono configurati tramite l'API Fluent. Specificare `-DataAnnotations` (PMC) o `--data-annotations` (interfaccia della riga di comando di .NET Core) per usare invece le annotazioni dei dati, quando possibile.

Se ad esempio si usa l'API Fluent, l'impalcatura è la seguente:

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

Quando si usano le annotazioni dei dati, questo è l'impalcatura:

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>Nome DbContext

Il nome della classe DbContext con impalcature sarà il nome del database con suffisso *per impostazione predefinita* . Per specificarne uno diverso, usare `-Context` in PMC e `--context` nel interfaccia della riga di comando di .NET Core.

## <a name="directories-and-namespaces"></a>Directory e spazi dei nomi

Le classi di entità e una classe DbContext sono impalcature nella directory radice del progetto e usano lo spazio dei nomi predefinito del progetto.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

È possibile specificare la directory in cui le classi sono con impalcature usando `--output-dir` e `--context-dir` possono essere usate per eseguire il patibolo della classe DbContext in una directory separata dalle classi del tipo di entità:

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

Per impostazione predefinita, lo spazio dei nomi sarà lo spazio dei nomi radice più i nomi di tutte le sottodirectory nella directory radice del progetto. Tuttavia, a partire da EFCore 5,0, è possibile eseguire l'override dello spazio dei nomi per tutte le classi di output usando `--namespace` . È anche possibile eseguire l'override dello spazio dei nomi solo per la classe DbContext usando `--context-namespace` :

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

È possibile specificare la directory in cui le classi sono con impalcature usando `-OutputDir` e `-ContextDir` possono essere usate per eseguire il patibolo della classe DbContext in una directory separata dalle classi del tipo di entità:

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

Per impostazione predefinita, lo spazio dei nomi sarà lo spazio dei nomi radice più i nomi di tutte le sottodirectory nella directory radice del progetto. Tuttavia, a partire da EFCore 5,0, è possibile eseguire l'override dello spazio dei nomi per tutte le classi di output usando `-Namespace` . È anche possibile eseguire l'override dello spazio dei nomi solo per la classe DbContext usando `-ContextNamespace` .

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a>Come funziona

Il reverse engineering inizia con la lettura dello schema del database. Legge le informazioni su tabelle, colonne, vincoli e indici.

USA quindi le informazioni sullo schema per creare un modello di EF Core. Le tabelle vengono utilizzate per creare i tipi di entità. le colonne vengono utilizzate per creare le proprietà; e le chiavi esterne vengono utilizzate per creare relazioni.

Infine, il modello viene utilizzato per generare il codice. Le classi del tipo di entità, l'API Fluent e le annotazioni dei dati corrispondenti sono basate su impalcature per ricreare lo stesso modello dall'app.

## <a name="limitations"></a>Limitazioni

* Non tutti gli elementi di un modello possono essere rappresentati utilizzando uno schema di database. Ad esempio, le informazioni sulle [**gerarchie di ereditarietà**](xref:core/modeling/inheritance), i [**tipi di proprietà**](xref:core/modeling/owned-entities)e la suddivisione delle [**tabelle**](xref:core/modeling/table-splitting) non sono presenti nello schema del database. Per questo motivo, questi costrutti non verranno mai decodificati.
* Inoltre, **alcuni tipi di colonna** potrebbero non essere supportati dal provider EF core. Queste colonne non verranno incluse nel modello.
* È possibile definire i [**token di concorrenza**](xref:core/modeling/concurrency)in un modello di EF core per impedire a due utenti di aggiornare la stessa entità nello stesso momento. Alcuni database hanno un tipo speciale per rappresentare questo tipo di colonna (ad esempio, rowversion in SQL Server), nel qual caso è possibile decompilare queste informazioni; Tuttavia, altri token di concorrenza non verranno decodificati.
* [La funzionalità del tipo di riferimento Nullable di C# 8](/dotnet/csharp/tutorials/nullable-reference-types) non è attualmente supportata in reverse engineering: EF core genera sempre il codice c# che presuppone che la funzionalità sia disabilitata. Ad esempio, le colonne di testo Nullable verranno sottoposto a impalcatura come proprietà con tipo `string` , non `string?` con l'API Fluent o le annotazioni dei dati utilizzate per configurare se una proprietà è obbligatoria o meno. È possibile modificare il codice con impalcature e sostituirle con annotazioni di supporto per i valori null C#. Il supporto dell'impalcatura per i tipi di riferimento nullable viene rilevato da Issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).

## <a name="customizing-the-model"></a>Personalizzazione del modello

Il codice generato da EF Core è il codice. È possibile modificarlo. Verrà rigenerato solo se si esegue nuovamente la decompilazione dello stesso modello. Il codice con impalcatura rappresenta *un* modello che può essere utilizzato per accedere al database, ma non è certo l' *unico* modello che può essere utilizzato.

Personalizzare le classi del tipo di entità e la classe DbContext in base alle esigenze. È ad esempio possibile scegliere di rinominare tipi e proprietà, introdurre gerarchie di ereditarietà o suddividere una tabella in più entità. È inoltre possibile rimuovere indici non univoci, sequenze inutilizzate e proprietà di navigazione, proprietà scalari facoltative e nomi di vincoli dal modello.

È anche possibile aggiungere ulteriori costruttori, metodi, proprietà e così via. uso di un'altra classe parziale in un file separato. Questo approccio funziona anche quando si intende decodificare nuovamente il modello.

## <a name="updating-the-model"></a>Aggiornamento del modello

Dopo aver apportato modifiche al database, potrebbe essere necessario aggiornare il modello di EF Core per riflettere tali modifiche. Se le modifiche apportate al database sono semplici, potrebbe essere più semplice apportare manualmente le modifiche al modello di EF Core. Ad esempio, la ridenominazione di una tabella o di una colonna, la rimozione di una colonna o l'aggiornamento del tipo di una colonna sono semplici modifiche da apportare nel codice.

Le modifiche più significative, tuttavia, non sono semplici da creare manualmente. Un flusso di lavoro comune è quello di decodificare il modello dal database usando `-Force` (PMC) o `--force` (CLI) per sovrascrivere il modello esistente con uno aggiornato.

Un'altra funzionalità comunemente richiesta è la possibilità di aggiornare il modello dal database mantenendo la personalizzazione, ad esempio le rinominazioni, le gerarchie dei tipi e così via. Utilizzare Issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) per tenere traccia dello stato di avanzamento di questa funzionalità.

> [!WARNING]
> Se si esegue di nuovo la decompilazione del modello dal database, tutte le modifiche apportate ai file andranno perse.
