---
title: Guida di riferimento agli strumenti di EF Core (.NET CLI)-EF Core
description: Guida di riferimento per gli strumenti di interfaccia della riga di comando di .NET Core Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dotnet
ms.openlocfilehash: 83989b8690236dbec3466cda78c204ab67fd10c4
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431583"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Guida di riferimento agli strumenti di Entity Framework Core-interfaccia della riga di comando di .NET Core

Gli strumenti dell'interfaccia della riga di comando (CLI) per Entity Framework Core eseguono attività di sviluppo in fase di progettazione. Ad esempio, creano [migrazioni](/aspnet/core/data/ef-mvc/migrations), applicano migrazioni e generano codice per un modello basato su un database esistente. I comandi sono un'estensione del comando [DotNet](/dotnet/core/tools) multipiattaforma, che fa parte del [.NET Core SDK](https://www.microsoft.com/net/core). Questi strumenti funzionano con i progetti .NET Core.

Quando si usa Visual Studio, è consigliabile usare gli [strumenti della console di gestione pacchetti](xref:core/cli/powershell) invece degli strumenti dell'interfaccia della riga di comando. Strumenti console di gestione pacchetti automaticamente:

* Funziona con il progetto corrente selezionato nella **console di gestione pacchetti** senza che sia necessario cambiare manualmente le directory.
* Apre i file generati da un comando dopo il completamento del comando.
* Fornisce il completamento tramite TAB di comandi, parametri, nomi di progetti, tipi di contesto e nomi di migrazione.

## <a name="installing-the-tools"></a>Installazione degli strumenti

`dotnet ef` può essere installato come strumento globale o locale. La maggior parte degli sviluppatori preferisce installare `dotnet ef` come strumento globale usando il comando seguente:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Per usarlo come strumento locale, ripristinare le dipendenze di un progetto che lo dichiara come dipendenza degli strumenti usando un [file manifesto dello strumento](/dotnet/core/tools/global-tools#install-a-local-tool).

Aggiornare lo strumento usando il comando seguente:

```dotnetcli
dotnet tool update --global dotnet-ef
```

Prima di poter usare gli strumenti in un progetto specifico, è necessario aggiungervi il `Microsoft.EntityFrameworkCore.Design` pacchetto.

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### <a name="verify-installation"></a>Verificare l'installazione

Eseguire i comandi seguenti per verificare che EF Core gli strumenti dell'interfaccia della riga di comando siano installati correttamente:

  ```dotnetcli
  dotnet ef
  ```

L'output del comando identifica la versione degli strumenti in uso:

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="update-the-tools"></a>Aggiornare gli strumenti

Utilizzare `dotnet tool update --global dotnet-ef` per aggiornare gli strumenti globali alla versione più recente disponibile. Se gli strumenti sono installati localmente nel progetto `dotnet tool update dotnet-ef` , usare. Installare una versione specifica aggiungendo `--version <VERSION>` al comando. Per altri dettagli, vedere la sezione relativa all' [aggiornamento](/dotnet/core/tools/dotnet-tool-update) della documentazione dello strumento dotnet.

## <a name="using-the-tools"></a>Uso degli strumenti

Prima di utilizzare gli strumenti, potrebbe essere necessario creare un progetto di avvio o impostare l'ambiente.

### <a name="target-project-and-startup-project"></a>Progetto di destinazione e progetto di avvio

I comandi fanno riferimento a un *progetto e a* un *progetto di avvio*.

* Il *progetto* è anche noto come *progetto di destinazione* perché è il punto in cui i comandi aggiungono o rimuovono i file. Per impostazione predefinita, il progetto nella directory corrente è il progetto di destinazione. È possibile specificare un progetto diverso come progetto di destinazione utilizzando l' <nobr>`--project`</nobr> opzione.

* Il *progetto di avvio* è quello che gli strumenti compilano ed eseguono. Gli strumenti di devono eseguire il codice dell'applicazione in fase di progettazione per ottenere informazioni sul progetto, ad esempio la stringa di connessione al database e la configurazione del modello. Per impostazione predefinita, il progetto nella directory corrente è il progetto di avvio. È possibile specificare un progetto diverso come progetto di avvio usando l' <nobr>`--startup-project`</nobr> opzione.

Il progetto di avvio e il progetto di destinazione sono spesso lo stesso progetto. Uno scenario tipico in cui si trovano progetti distinti è quando:

* Il contesto EF Core e le classi di entità si trovano in una libreria di classi .NET Core.
* Un'app console o Web .NET Core fa riferimento alla libreria di classi.

È anche possibile [inserire il codice delle migrazioni in una libreria di classi separata dal contesto EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Altri Framework di destinazione

Gli strumenti dell'interfaccia della riga di comando funzionano con progetti .NET Core e progetti .NET Framework. Le app che hanno il modello di EF Core in una libreria di classi .NET Standard potrebbero non avere un progetto .NET Core o .NET Framework. Ad esempio, questo vale per le app Novell e piattaforma UWP (Universal Windows Platform). In questi casi, è possibile creare un progetto di app console .NET Core il cui unico scopo è fungere da progetto di avvio per gli strumenti. Il progetto può essere un progetto fittizio senza codice reale &mdash; necessario per fornire una destinazione per gli strumenti.

Perché è necessario un progetto fittizio? Come indicato in precedenza, gli strumenti devono eseguire il codice dell'applicazione in fase di progettazione. A tale scopo, è necessario usare il runtime di .NET Core. Quando il modello di EF Core si trova in un progetto destinato a .NET Core o .NET Framework, gli strumenti di EF Core prendono in prestito il runtime dal progetto. Questa operazione non può essere eseguita se il modello di EF Core si trova in una libreria di classi .NET Standard. Il .NET Standard non è un'implementazione .NET effettiva; si tratta di una specifica di un set di API che le implementazioni di .NET devono supportare. Pertanto .NET Standard non è sufficiente per gli strumenti EF Core per eseguire il codice dell'applicazione. Il progetto fittizio creato da usare come progetto di avvio fornisce una piattaforma di destinazione concreta in cui gli strumenti possono caricare la libreria di classi .NET Standard.

### <a name="aspnet-core-environment"></a>Ambiente ASP.NET Core

Per specificare [l'ambiente](/aspnet/core/fundamentals/environments) per i progetti ASP.NET Core, impostare la variabile di ambiente **ASPNETCORE_ENVIRONMENT** prima di eseguire i comandi.

A partire da EF Core 5,0, gli argomenti aggiuntivi possono essere passati anche in Program. CreateHostBuilder che consente di specificare l'ambiente nella riga di comando:

```dotnetcli
dotnet ef database update -- --environment Production
```

> [!TIP]
> Il `--` token indirizza `dotnet ef` a trattare tutti gli elementi che seguono come argomento e non tenta di analizzarli come opzioni. Qualsiasi argomento aggiuntivo non usato da `dotnet ef` viene inviato all'app.

## <a name="common-options"></a>Opzioni comuni

| Opzione                                         | Short             | Descrizione                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | Mostra l'output JSON.                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | Classe `DbContext` da usare. Solo il nome della classe o completo con gli spazi dei nomi.  Se questa opzione viene omessa, EF Core troverà la classe Context. Se sono presenti più classi di contesto, questa opzione è obbligatoria.                                            |
| `--project <PROJECT>`                          | `-p`              | Percorso relativo della cartella del progetto del progetto di destinazione.  Il valore predefinito è la cartella corrente.                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | Percorso relativo della cartella di progetto del progetto di avvio. Il valore predefinito è la cartella corrente.                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | [Moniker del Framework](/dotnet/standard/frameworks#supported-target-framework-versions) di destinazione per il [Framework di destinazione](/dotnet/standard/frameworks).  Usare quando il file di progetto specifica più framework di destinazione e si vuole selezionarne uno. |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | Configurazione della build, ad esempio: `Debug` o `Release` .                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | Identificatore del runtime di destinazione per cui ripristinare i pacchetti. Per un elenco degli identificatori di runtime (RID, Runtime Identifier), vedere il [catalogo RID](/dotnet/core/rid-catalog).                                                                                                      |
| `--no-build`                                   |                   | Non compilare il progetto. Progettato per essere utilizzato quando la compilazione è aggiornata.                                                                                                                                                                                    |
| `--help`                                       | `-h`              | Visualizzare le informazioni della guida.                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | Mostra output dettagliato.                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | Non colora l'output.                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | Prefisso output con livello.                                                                                                                                                                                                                                     |

A partire da EF Core 5,0, eventuali argomenti aggiuntivi vengono passati all'applicazione.

## <a name="dotnet-ef-database-drop"></a>eliminazione database DotNet EF

Elimina il database.

Opzioni:

| Opzione                   | Short             | Descrizione                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | Non confermare.                                           |
| <nobr>`--dry-run`</nobr> |                   | Indica il database da eliminare, ma non eliminarlo. |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-database-update"></a>aggiornamento database DotNet EF

Aggiorna il database all'ultima migrazione o a una migrazione specificata.

Argomenti:

| Argomento                   | Descrizione                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | Migrazione di destinazione. Le migrazioni possono essere identificate in base al nome o all'ID. Il numero 0 è un caso speciale che indica *prima della prima migrazione* e comporta il ripristino di tutte le migrazioni. Se non viene specificata alcuna migrazione, l'impostazione predefinita del comando è l'ultima migrazione. |

Opzioni:

| Opzione                                    | Descrizione                                                                                                                      |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | Stringa di connessione al database. Il valore predefinito è quello specificato in `AddDbContext` o `OnConfiguring` . Aggiunto in EF Core 5,0. |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

Gli esempi seguenti aggiornano il database a una migrazione specificata. Il primo usa il nome della migrazione e il secondo usa l'ID migrazione e una connessione specificata:

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>informazioni su DotNet EF DbContext

Ottiene informazioni su un `DbContext` tipo.

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-dbcontext-list"></a>elenco DbContext di DotNet EF

Elenca i `DbContext` tipi disponibili.

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-dbcontext-scaffold"></a>impalcatura DbContext DotNet EF

Genera il codice per un `DbContext` tipo di entità e per un database. Affinché questo comando generi un tipo di entità, è necessario che la tabella di database disponga di una chiave primaria.

Argomenti:

| Argomento                    | Descrizione                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | Stringa di connessione al database. Per i progetti ASP.NET Core 2. x, il valore può essere *Name \<name of connection string> =*. In tal caso, il nome deriva dalle origini di configurazione configurate per il progetto. |
| `<PROVIDER>`                | Provider da usare. Si tratta in genere del nome del pacchetto NuGet, ad esempio: `Microsoft.EntityFrameworkCore.SqlServer` .                                                                                           |

Opzioni:

| Opzione                                   | Short             | Descrizione                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | Utilizzare gli attributi per configurare il modello (laddove possibile). Se questa opzione viene omessa, viene usata solo l'API Fluent.                                                                |
| `--context <NAME>`                       | `-c`              | Nome della `DbContext` classe da generare.                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | Directory in cui inserire il `DbContext` file di classe. I percorsi sono relativi alla directory del progetto. Gli spazi dei nomi sono derivati dai nomi delle cartelle.                                 |
| `--context-namespace <NAMESPACE>`        |                   | Spazio dei nomi da utilizzare per la `DbContext` classe generata. Nota: esegue l'override di `--namespace` . Aggiunto in EF Core 5,0.                                                                 |
| `--force`                                | `-f`              | Sovrascrivere i file esistenti.                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | Directory in cui inserire i file della classe di entità. I percorsi sono relativi alla directory del progetto.                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | Spazio dei nomi da utilizzare per tutte le classi generate. Il valore predefinito è generato dallo spazio dei nomi radice e dalla directory di output. Aggiunto in EF Core 5,0.                                  |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | Schemi delle tabelle per cui generare i tipi di entità. Per specificare più schemi, ripetere `--schema` per ognuno di essi. Se questa opzione viene omessa, vengono inclusi tutti gli schemi.          |
| `--table <TABLE_NAME>`...                | `-t`              | Tabelle per cui generare i tipi di entità. Per specificare più tabelle, ripetere `-t` o `--table` per ciascuna di esse. Se questa opzione viene omessa, vengono incluse tutte le tabelle.                |
| `--use-database-names`                   |                   | Utilizzare i nomi di tabella e colonna esattamente come vengono visualizzati nel database. Se questa opzione viene omessa, i nomi di database vengono modificati in modo da essere conformi alle convenzioni di stile del nome C#. |
| `--no-onconfiguring`                     |                   | Evita la generazione del `OnConfiguring` metodo nella `DbContext` classe generata. Aggiunto in EF Core 5,0.                                                                  |
| `--no-pluralize`                         |                   | Non usare pluralizer. Aggiunto in EF Core 5,0                                                                                                                                 |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

Nell'esempio seguente vengono assemblati tutti gli schemi e le tabelle e vengono inseriti i nuovi file nella cartella *Models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

Nell'esempio seguente vengono create le impalcature solo per le tabelle selezionate e il contesto viene creato in una cartella separata con un nome e uno spazio dei nomi specificati:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

Nell'esempio seguente viene letta la stringa di connessione dal set di configurazione del progetto utilizzando lo [strumento di gestione dei segreti](/aspnet/core/security/app-secrets#secret-manager).

```dotnetcli
dotnet user-secrets set ConnectionStrings.Blogging "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Blogging"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Blogging Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="dotnet-ef-dbcontext-script"></a>script DotNet EF DbContext

Genera uno script SQL da DbContext. Ignora le migrazioni. Aggiunto in EF Core 3,0.

Opzioni:

| Opzione                         | Short             | Descrizione                      |
| ------------------------------ | ----------------- | -------------------------------- |
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | File in cui scrivere il risultato. |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-migrations-add"></a>Aggiunta di migrazioni DotNet EF

Aggiunge una nuova migrazione.

Argomenti:

| Argomento              | Descrizione                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | Nome della migrazione. |

Opzioni:

| Opzione                                 | Short             | Descrizione                                                                                                            |
|:---------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | Directory usata per l'output dei file. I percorsi sono relativi alla directory del progetto di destinazione. Il valore predefinito è "Migrations".   |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | Spazio dei nomi da utilizzare per le classi generate. Il valore predefinito è generato dalla directory di output. Aggiunto in EF Core 5,0. |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-migrations-list"></a>elenco migrazioni di DotNet EF

Elenca le migrazioni disponibili.

Opzioni:

| Opzione                                   | Descrizione                                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| <nobr>`--connection <CONNECTION>`</nobr> | Stringa di connessione al database. Il valore predefinito è quello specificato in AddDbContext o Configuring. Aggiunto in EF Core 5,0. |
| `--no-connect`                           | Non connettersi al database. Aggiunto in EF Core 5,0.                                                                         |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-migrations-remove"></a>Rimuovi migrazioni di DotNet EF

Rimuove l'ultima migrazione (esegue il rollback delle modifiche del codice eseguite per la migrazione).

Opzioni:

| Opzione                 | Short             | Descrizione                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | Ripristinare la migrazione, ovvero eseguire il rollback delle modifiche applicate al database. |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

## <a name="dotnet-ef-migrations-script"></a>script migrazioni DotNet EF

Genera uno script SQL dalle migrazioni.

Argomenti:

| Argomento              | Descrizione                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | Migrazione iniziale. Le migrazioni possono essere identificate in base al nome o all'ID. Il numero 0 è un caso speciale che indica *prima della prima migrazione*. Il valore predefinito è 0. |
| `<TO>`                | Migrazione finale. L'impostazione predefinita è l'ultima migrazione.                                                                                                         |

Opzioni:

| Opzione                           | Short             | Descrizione                                                        |
|:---------------------------------|:------------------|:-------------------------------------------------------------------|
| `--output <FILE>`                | <nobr>`-o`</nobr> | File in cui scrivere lo script.                                   |
| `--idempotent`                   | `-i`              | Genera uno script che può essere utilizzato in un database in qualsiasi migrazione. |
| <nobr>`--no-transactions`</nobr> |                   | Non generare istruzioni SQL Transaction. Aggiunto in EF Core 5,0.   |

Le [Opzioni comuni](#common-options) sono elencate in precedenza.

Nell'esempio seguente viene creato uno script per la migrazione di InitialCreate:

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

Nell'esempio seguente viene creato uno script per tutte le migrazioni dopo la migrazione di InitialCreate.

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Risorse aggiuntive

* [Migrazioni](xref:core/managing-schemas/migrations/index)
* [Reverse Engineering](xref:core/managing-schemas/scaffolding)
