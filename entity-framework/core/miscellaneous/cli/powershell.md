---
title: Guida di riferimento agli strumenti di EF Core (console di gestione pacchetti)-EF Core
description: Guida di riferimento per la console di gestione pacchetti Entity Framework Core Visual Studio
author: bricelam
ms.date: 10/13/2020
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 3a9599288d74013bf4da910c64bc858539c0c32c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062386"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Guida di riferimento agli strumenti di Entity Framework Core-Console di gestione pacchetti in Visual Studio

Gli strumenti della console di gestione pacchetti (PMC) per Entity Framework Core eseguono attività di sviluppo in fase di progettazione. Ad esempio, creano [migrazioni](/aspnet/core/data/ef-mvc/migrations), applicano migrazioni e generano codice per un modello basato su un database esistente. I comandi vengono eseguiti all'interno di Visual Studio tramite la [console di gestione pacchetti](/nuget/tools/package-manager-console). Questi strumenti funzionano sia con i progetti .NET Framework che con i progetti .NET Core.

Se non si usa Visual Studio, è consigliabile usare gli [strumenti da riga di comando EF Core](xref:core/miscellaneous/cli/dotnet) . Gli strumenti interfaccia della riga di comando di .NET Core sono multipiattaforma ed eseguiti all'interno di un prompt dei comandi.

## <a name="installing-the-tools"></a>Installazione degli strumenti

Installare gli strumenti della console di gestione pacchetti eseguendo il comando seguente nella **console di gestione pacchetti**:

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Aggiornare gli strumenti eseguendo il comando seguente nella **console di gestione pacchetti**.

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>Verificare l'installazione

Verificare che gli strumenti siano installati eseguendo questo comando:

```powershell
Get-Help about_EntityFrameworkCore
```

L'output ha un aspetto simile al seguente (non indica la versione degli strumenti in uso):

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>Uso degli strumenti

Prima di usare gli strumenti:

* Comprendere la differenza tra il progetto di destinazione e quello di avvio.
* Informazioni su come usare gli strumenti con le librerie di classi .NET Standard.
* Per ASP.NET Core progetti, impostare l'ambiente.

### <a name="target-and-startup-project"></a>Progetto di destinazione e di avvio

I comandi fanno riferimento a un *progetto e a* un *progetto di avvio*.

* Il *progetto* è anche noto come *progetto di destinazione* perché è il punto in cui i comandi aggiungono o rimuovono i file. Per impostazione predefinita, il progetto **predefinito** selezionato nella **console di gestione pacchetti** è il progetto di destinazione. È possibile specificare un progetto diverso come progetto di destinazione utilizzando l' <nobr>`--project`</nobr> opzione.

* Il *progetto di avvio* è quello che gli strumenti compilano ed eseguono. Gli strumenti di devono eseguire il codice dell'applicazione in fase di progettazione per ottenere informazioni sul progetto, ad esempio la stringa di connessione al database e la configurazione del modello. Per impostazione predefinita, il **progetto di avvio** in **Esplora soluzioni** è il progetto di avvio. È possibile specificare un progetto diverso come progetto di avvio usando l' <nobr>`--startup-project`</nobr> opzione.

Il progetto di avvio e il progetto di destinazione sono spesso lo stesso progetto. Uno scenario tipico in cui si trovano progetti distinti è quando:

* Il contesto EF Core e le classi di entità si trovano in una libreria di classi .NET Core.
* Un'app console o Web .NET Core fa riferimento alla libreria di classi.

È anche possibile [inserire il codice delle migrazioni in una libreria di classi separata dal contesto EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Altri Framework di destinazione

Gli strumenti della console di gestione pacchetti funzionano con i progetti .NET Core o .NET Framework. Le app che hanno il modello di EF Core in una libreria di classi .NET Standard potrebbero non avere un progetto .NET Core o .NET Framework. Ad esempio, questo vale per le app Novell e piattaforma UWP (Universal Windows Platform). In questi casi, è possibile creare un progetto di app console .NET Core o .NET Framework il cui unico scopo è fungere da progetto di avvio per gli strumenti di. Il progetto può essere un progetto fittizio senza codice reale &mdash; necessario per fornire una destinazione per gli strumenti.

Perché è necessario un progetto fittizio? Come indicato in precedenza, gli strumenti devono eseguire il codice dell'applicazione in fase di progettazione. A tale scopo, è necessario usare .NET Core o .NET Framework Runtime. Quando il modello di EF Core si trova in un progetto destinato a .NET Core o .NET Framework, gli strumenti di EF Core prendono in prestito il runtime dal progetto. Questa operazione non può essere eseguita se il modello di EF Core si trova in una libreria di classi .NET Standard. Il .NET Standard non è un'implementazione .NET effettiva; si tratta di una specifica di un set di API che le implementazioni di .NET devono supportare. Pertanto .NET Standard non è sufficiente per gli strumenti EF Core per eseguire il codice dell'applicazione. Il progetto fittizio creato da usare come progetto di avvio fornisce una piattaforma di destinazione concreta in cui gli strumenti possono caricare la libreria di classi .NET Standard.

### <a name="aspnet-core-environment"></a>Ambiente ASP.NET Core

Per specificare l'ambiente per i progetti ASP.NET Core, impostare **env: ASPNETCORE_ENVIRONMENT** prima di eseguire i comandi.

## <a name="common-parameters"></a>Parametri comuni

La tabella seguente illustra i parametri comuni a tutti i comandi EF Core:

| Parametro                 | Description                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Contesto \<String>        | Classe `DbContext` da usare. Solo il nome della classe o completo con gli spazi dei nomi.  Se questo parametro viene omesso, EF Core trova la classe del contesto. Se sono presenti più classi di contesto, questo parametro è obbligatorio. |
| -Progetto \<String>        | Progetto di destinazione. Se questo parametro viene omesso, come progetto di destinazione viene utilizzato il **progetto predefinito** per la **console di gestione pacchetti** .                                                                             |
| <nobr>-Progetto</nobr>\<String> | Progetto di avvio. Se questo parametro viene omesso, il **progetto di avvio** nelle **proprietà della soluzione** viene utilizzato come progetto di destinazione.                                                                                 |
| -Argomenti \<String>           | Argomenti passati all'applicazione. Aggiunto in EF Core 5,0.                                                                                                                                                           |
| -Verbose                  | Mostra output dettagliato.                                                                                                                                                                                                 |

Per visualizzare le informazioni della guida relative a un comando, usare il comando di PowerShell `Get-Help` .

> [!TIP]
> Il contesto, il progetto e i parametri progetto supportano la scheda-espansione.

## <a name="add-migration"></a>Add-Migration

Aggiunge una nuova migrazione.

Parametri

| Parametro                         | Description                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<String><nobr>       | Nome della migrazione. Si tratta di un parametro posizionale ed è obbligatorio.                                              |
| <nobr>-OutputDir \<String></nobr> | Directory usata per l'output dei file. I percorsi sono relativi alla directory del progetto di destinazione. Il valore predefinito è "Migrations". |
| <nobr>-Spazio dei nomi \<String></nobr> | Spazio dei nomi da utilizzare per le classi generate. Il valore predefinito è generato dalla directory di output. Aggiunto in EF Core 5,0.  |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

## <a name="drop-database"></a>Drop-Database

Elimina il database.

Parametri

| Parametro | Description                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | Indica il database da eliminare, ma non eliminarlo. |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

## <a name="get-dbcontext"></a>Get-DbContext

Elenca e ottiene informazioni sui `DbContext` tipi disponibili.

I [parametri comuni](#common-parameters) sono elencati in precedenza.

## <a name="get-migration"></a>Get-Migration

Elenca le migrazioni disponibili. Aggiunto in EF Core 5,0.

Parametri

| Parametro                          | Description                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <nobr>-Connessione \<String></nobr> | Stringa di connessione al database. Il valore predefinito è quello specificato in AddDbContext o Configuring. |
| -Noconnect                         | Non connettersi al database.                                                                         |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

## <a name="remove-migration"></a>Remove-Migration

Rimuove l'ultima migrazione (esegue il rollback delle modifiche del codice eseguite per la migrazione).

Parametri

| Parametro | Description                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | Ripristinare la migrazione, ovvero eseguire il rollback delle modifiche applicate al database. |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

## <a name="scaffold-dbcontext"></a>Scaffold-DbContext

Genera il codice per un `DbContext` tipo di entità e per un database. Per `Scaffold-DbContext` poter generare un tipo di entità, è necessario che la tabella di database disponga di una chiave primaria.

Parametri

| Parametro                          | Description                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Connessione \<String></nobr> | Stringa di connessione al database. Per i progetti ASP.NET Core 2. x, il valore può essere *Name \<name of connection string> =*. In tal caso, il nome deriva dalle origini di configurazione configurate per il progetto. Si tratta di un parametro posizionale ed è obbligatorio. |
| <nobr>-Provider \<String></nobr>   | Provider da usare. Si tratta in genere del nome del pacchetto NuGet, ad esempio: `Microsoft.EntityFrameworkCore.SqlServer` . Si tratta di un parametro posizionale ed è obbligatorio.                                                                                           |
| -OutputDir \<String>               | Directory in cui inserire i file. I percorsi sono relativi alla directory del progetto.                                                                                                                                                                                             |
| -ContextDir \<String>              | Directory in cui inserire il `DbContext` file. I percorsi sono relativi alla directory del progetto.                                                                                                                                                               |
| -Spazio dei nomi \<String>               | Spazio dei nomi da utilizzare per tutte le classi generate. Il valore predefinito è generato dallo spazio dei nomi radice e dalla directory di output. Aggiunto in EF Core 5,0.                                                                                                                           |
| -ContextNamespace \<String>        | Spazio dei nomi da utilizzare per la `DbContext` classe generata. Nota: esegue l'override di `-Namespace` . Aggiunto in EF Core 5,0.                                                                                                                                                           |
| -Contesto \<String>                 | Nome della `DbContext` classe da generare.                                                                                                                                                                                                                          |
| -Schemi \<String[]>               | Schemi delle tabelle per cui generare i tipi di entità. Se questo parametro viene omesso, vengono inclusi tutti gli schemi.                                                                                                                                                             |
| -Tabelle \<String[]>                | Tabelle per cui generare i tipi di entità. Se questo parametro viene omesso, vengono incluse tutte le tabelle.                                                                                                                                                                         |
| -DataAnnotations                   | Utilizzare gli attributi per configurare il modello (laddove possibile). Se questo parametro viene omesso, viene usata solo l'API Fluent.                                                                                                                                                      |
| -UseDatabaseNames                  | Utilizzare i nomi di tabella e colonna esattamente come vengono visualizzati nel database. Se questo parametro viene omesso, i nomi di database vengono modificati in modo da essere conformi alle convenzioni di stile del nome C#.                                                                                       |
| -Force                             | Sovrascrivere i file esistenti.                                                                                                                                                                                                                                               |
| -NoOnConfiguring                   | Non generare `DbContext.OnConfiguring` . Aggiunto in EF Core 5,0.                                                                                                                                                                                                         |
| -NoPluralize                       | Non usare pluralizer. Aggiunto in EF Core 5,0.                                                                                                                                                                                                                         |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

Esempio:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

Esempio che associa solo le tabelle selezionate e crea il contesto in una cartella separata con il nome e lo spazio dei nomi specificati:

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-dbcontext"></a>Script-DbContext

Genera uno script SQL da DbContext. Ignora le migrazioni. Aggiunto in EF Core 3,0.

Parametri

| Parametro                      | Description                      |
| ------------------------------ | -------------------------------- |
| <nobr>-Output \<String></nobr> | File in cui scrivere il risultato. |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

## <a name="script-migration"></a>Script-Migration

Genera uno script SQL che applica tutte le modifiche a una migrazione selezionata a un'altra.

Parametri

| Parametro                    | Description                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-Da*\<String>            | Migrazione iniziale. Le migrazioni possono essere identificate in base al nome o all'ID. Il numero 0 è un caso speciale che indica *prima della prima migrazione*. Il valore predefinito è 0.                                                              |
| *Da a*\<String>              | Migrazione finale. L'impostazione predefinita è l'ultima migrazione.                                                                                                                                                                      |
| -Idempotente                  | Genera uno script che può essere utilizzato in un database in qualsiasi migrazione.                                                                                                                                                         |
| <nobr>-Transactions</nobr> | Non generare istruzioni SQL Transaction. Aggiunto in EF Core 5,0.                                                                                                                                                           |
| -Output \<String>            | File in cui scrivere il risultato. Se questo parametro viene omesso, il file viene creato con un nome generato nella stessa cartella in cui vengono creati i file di runtime dell'applicazione, ad esempio: */obj/debug/netcoreapp2.1/ghbkztfz.SQL/*. |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

> [!TIP]
> I parametri to, from e output supportano la scheda-espansione.

Nell'esempio seguente viene creato uno script per la migrazione di InitialCreate, usando il nome della migrazione.

```powershell
Script-Migration -To InitialCreate
```

Nell'esempio seguente viene creato uno script per tutte le migrazioni dopo la migrazione di InitialCreate, usando l'ID di migrazione.

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>Update-Database

Aggiorna il database all'ultima migrazione o a una migrazione specificata.

| Parametro                           | Description                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-Migrazione*\<String></nobr> | Migrazione di destinazione. Le migrazioni possono essere identificate in base al nome o all'ID. Il numero 0 è un caso speciale che indica *prima della prima migrazione* e comporta il ripristino di tutte le migrazioni. Se non viene specificata alcuna migrazione, l'impostazione predefinita del comando è l'ultima migrazione. |
| <nobr>-Connessione \<String></nobr>  | Stringa di connessione al database. Il valore predefinito è quello specificato in `AddDbContext` o `OnConfiguring` . Aggiunto in EF Core 5,0.                                                                                                                                |

I [parametri comuni](#common-parameters) sono elencati in precedenza.

> [!TIP]
> Il parametro Migration supporta l'espansione tramite tabulazione.

Nell'esempio seguente vengono ripristinate tutte le migrazioni.

```powershell
Update-Database -Migration 0
```

Gli esempi seguenti aggiornano il database a una migrazione specificata. Il primo usa il nome della migrazione e il secondo usa l'ID migrazione e una connessione specificata:

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Migrazioni](xref:core/managing-schemas/migrations/index)
* [Reverse Engineering](xref:core/managing-schemas/scaffolding)
