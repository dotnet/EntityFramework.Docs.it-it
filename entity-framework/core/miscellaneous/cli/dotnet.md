---
title: Guida di riferimento agli strumenti di EF Core (.NET CLI)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 07/11/2019
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: fe378fc962c0d491703a3e77dca4415ad510d673
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370628"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Guida di riferimento agli strumenti di Entity Framework Core-interfaccia della riga di comando di .NET Core

Gli strumenti dell'interfaccia della riga di comando (CLI) per Entity Framework Core eseguono attività di sviluppo in fase di progettazione. Ad esempio, creano [migrazioni](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), applicano migrazioni e generano codice per un modello basato su un database esistente. I comandi sono un'estensione del comando [DotNet](/dotnet/core/tools) multipiattaforma, che fa parte del [.NET Core SDK](https://www.microsoft.com/net/core). Questi strumenti funzionano con i progetti .NET Core.

Se si usa Visual Studio, è consigliabile usare invece gli [strumenti della console di gestione pacchetti](powershell.md) :

* Il progetto funziona automaticamente con il progetto corrente selezionato nella **console di gestione pacchetti** senza che sia necessario cambiare manualmente le directory.
* Aprono automaticamente i file generati da un comando dopo il completamento del comando.

## <a name="installing-the-tools"></a>Installazione degli strumenti

La procedura di installazione dipende dal tipo di progetto e dalla versione:

* EF Core 3. x
* ASP.NET Core versione 2,1 e successive
* EF Core 2. x
* EF Core 1. x

### <a name="ef-core-3x"></a>EF Core 3. x

* `dotnet ef`deve essere installato come strumento globale o locale. La maggior parte degli sviluppatori verrà installata `dotnet ef` come strumento globale con il comando seguente:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  È anche possibile usare `dotnet ef` come strumento locale. Per usarlo come strumento locale, ripristinare le dipendenze di un progetto che lo dichiara come dipendenza degli strumenti usando un [file manifesto dello strumento](https://github.com/dotnet/cli/issues/10288).

* Installare [.NET Core SDK](https://www.microsoft.com/net/download/core).

* Installare il pacchetto più recente `Microsoft.EntityFrameworkCore.Design` .

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="aspnet-core-21"></a>ASP.NET Core 2.1 +

* Installare la [.NET Core SDK](https://www.microsoft.com/net/download/core)corrente. Il SDK deve essere installato anche se è presente la versione più recente di Visual Studio 2017.

  Questo è tutto ciò che è necessario per ASP.NET Core 2.1 + perché il `Microsoft.EntityFrameworkCore.Design` pacchetto è incluso nel [metapacchetto Microsoft. AspNetCore. app](/aspnet/core/fundamentals/metapackage-app).

### <a name="ef-core-2x-not-aspnet-core"></a>EF Core 2. x (non ASP.NET Core)

I `dotnet ef` comandi sono inclusi nel .NET Core SDK, ma per abilitare i comandi è necessario installare il `Microsoft.EntityFrameworkCore.Design` pacchetto.

* Installare la [.NET Core SDK](https://www.microsoft.com/net/download/core)corrente. L'SDK deve essere installato anche se si dispone della versione più recente di Visual Studio.

* Installare il pacchetto stabile più recente `Microsoft.EntityFrameworkCore.Design` .

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

### <a name="ef-core-1x"></a>EF Core 1. x

* Installare la versione di .NET Core SDK 2.1.200. Le versioni successive non sono compatibili con gli strumenti dell'interfaccia della riga di comando per EF Core 1,0 e 1,1.

* Configurare l'applicazione in modo che usi la versione di 2.1.200 SDK modificando il relativo [global.jssu](/dotnet/core/tools/global-json) file. Questo file è in genere incluso nella directory della soluzione (uno sopra il progetto).

* Modificare il file di progetto e aggiungerlo `Microsoft.EntityFrameworkCore.Tools.DotNet` come `DotNetCliToolReference` elemento. Specificare la versione 1. x più recente, ad esempio: 1.1.6. Vedere l'esempio di file di progetto alla fine di questa sezione.

* Installare la versione 1. x più recente del `Microsoft.EntityFrameworkCore.Design` pacchetto, ad esempio:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 1.1.6
  ```

  Con l'aggiunta di entrambi i riferimenti al pacchetto, il file di progetto ha un aspetto simile al seguente:

  ``` xml
  <Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
      <OutputType>Exe</OutputType>
      <TargetFramework>netcoreapp1.1</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
      <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                        Version="1.1.6"
                         PrivateAssets="All" />
    </ItemGroup>
    <ItemGroup>
       <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                              Version="1.1.6" />
    </ItemGroup>
  </Project>
  ```

  Un riferimento al pacchetto con `PrivateAssets="All"` non viene esposto ai progetti che fanno riferimento a questo progetto. Questa restrizione è particolarmente utile per i pacchetti che in genere vengono usati solo durante lo sviluppo.

### <a name="verify-installation"></a>Verificare l'installazione

Eseguire i comandi seguenti per verificare che EF Core gli strumenti dell'interfaccia della riga di comando siano installati correttamente:

  ```dotnetcli
  dotnet restore
  dotnet ef
  ```

L'output del comando identifica la versione degli strumenti in uso:

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```


## <a name="updating-the-tools"></a>Aggiornamento degli strumenti

Usare `dotnet tool update --global dotnet-ef` per aggiornare gli strumenti globali alla versione disponibile più recente, se gli strumenti sono installati localmente nel progetto `dotnet tool update dotnet-ef` . Installare una versione specifica aggiungendo `--version <VERSION>` al comando. Per altri dettagli, vedere la sezione relativa all' [aggiornamento](/dotnet/core/tools/dotnet-tool-update) della documentazione dello strumento dotnet.


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

Per specificare l'ambiente per i progetti ASP.NET Core, impostare la variabile di ambiente **ASPNETCORE_ENVIRONMENT** prima di eseguire i comandi.

## <a name="common-options"></a>Opzioni comuni

|                   | Opzione                            | Descrizione                                                                                                                                                                                                                                                   |
|:------------------|:----------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                   | `--json`                          | Mostra l'output JSON.                                                                                                                                                                                                                                             |
| <nobr>`-c`</nobr> | `--context <DBCONTEXT>`           | Classe `DbContext` da usare. Solo il nome della classe o completo con gli spazi dei nomi.  Se questa opzione viene omessa, EF Core troverà la classe Context. Se sono presenti più classi di contesto, questa opzione è obbligatoria.                                            |
| `-p`              | `--project <PROJECT>`             | Percorso relativo della cartella del progetto del progetto di destinazione.  Il valore predefinito è la cartella corrente.                                                                                                                                                              |
| `-s`              | `--startup-project <PROJECT>`     | Percorso relativo della cartella di progetto del progetto di avvio. Il valore predefinito è la cartella corrente.                                                                                                                                                              |
|                   | `--framework <FRAMEWORK>`         | [Moniker del Framework](/dotnet/standard/frameworks#supported-target-framework-versions) di destinazione per il [Framework di destinazione](/dotnet/standard/frameworks).  Usare quando il file di progetto specifica più framework di destinazione e si vuole selezionarne uno. |
|                   | `--configuration <CONFIGURATION>` | Configurazione della build, ad esempio: `Debug` o `Release` .                                                                                                                                                                                                   |
|                   | `--runtime <IDENTIFIER>`          | Identificatore del runtime di destinazione per cui ripristinare i pacchetti. Per un elenco degli identificatori di runtime (RID, Runtime Identifier), vedere il [catalogo RID](/dotnet/core/rid-catalog).                                                                                                      |
| `-h`              | `--help`                          | Visualizzare le informazioni della guida.                                                                                                                                                                                                                                        |
| `-v`              | `--verbose`                       | Mostra output dettagliato.                                                                                                                                                                                                                                          |
|                   | `--no-color`                      | Non colora l'output.                                                                                                                                                                                                                                        |
|                   | `--prefix-output`                 | Prefisso output con livello.                                                                                                                                                                                                                                     |

## <a name="dotnet-ef-database-drop"></a>eliminazione database DotNet EF

Elimina il database.

Opzioni:

|                   | Opzione                   | Descrizione                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>`-f`</nobr> | <nobr>`--force`</nobr>   | Non confermare.                                           |
|                   | <nobr>`--dry-run`</nobr> | Indica il database da eliminare, ma non eliminarlo. |

## <a name="dotnet-ef-database-update"></a>aggiornamento database DotNet EF

Aggiorna il database all'ultima migrazione o a una migrazione specificata.

Argomenti:

| Argomento      | Descrizione                                                                                                                                                                                                                                                     |
|:--------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<MIGRATION>` | Migrazione di destinazione. Le migrazioni possono essere identificate in base al nome o all'ID. Il numero 0 è un caso speciale che indica *prima della prima migrazione* e comporta il ripristino di tutte le migrazioni. Se non viene specificata alcuna migrazione, l'impostazione predefinita del comando è l'ultima migrazione. |

Opzioni:

|                   | Opzione                   | Descrizione                                              |
|:------------------|:-------------------------|:---------------------------------------------------------|
| <nobr>    </nobr> |  `--connection <CONNECTION>`        | Stringa di connessione al database. Il valore predefinito è quello specificato in `AddDbContext` o `OnConfiguring` . |


Gli esempi seguenti aggiornano il database a una migrazione specificata. Il primo usa il nome della migrazione e il secondo usa l'ID migrazione e una connessione specificata:

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>informazioni su DotNet EF DbContext

Ottiene informazioni su un `DbContext` tipo.

## <a name="dotnet-ef-dbcontext-list"></a>elenco DbContext di DotNet EF

Elenca i `DbContext` tipi disponibili.

## <a name="dotnet-ef-dbcontext-scaffold"></a>impalcatura DbContext DotNet EF

Genera il codice per un `DbContext` tipo di entità e per un database. Affinché questo comando generi un tipo di entità, è necessario che la tabella di database disponga di una chiave primaria.

Argomenti:

| Argomento       | Descrizione                                                                                                                                                                                                             |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<CONNECTION>` | Stringa di connessione al database. Per i progetti ASP.NET Core 2. x, il valore può essere *Name \<name of connection string> =*. In tal caso, il nome deriva dalle origini di configurazione configurate per il progetto. |
| `<PROVIDER>`   | Provider da usare. Si tratta in genere del nome del pacchetto NuGet, ad esempio: `Microsoft.EntityFrameworkCore.SqlServer` .                                                                                           |

Opzioni:

|                 | Opzione                                   | Descrizione                                                                                                                                                                    |
|:----------------|:-----------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`-d`</nobr> | `--data-annotations`                   | Utilizzare gli attributi per configurare il modello (laddove possibile). Se questa opzione viene omessa, viene usata solo l'API Fluent.                                                                |
| `-c`            | `--context <NAME>`                       | Nome della `DbContext` classe da generare.                                                                                                                                 |
|                 | `--context-dir <PATH>`                   | Directory in cui inserire il `DbContext` file di classe. I percorsi sono relativi alla directory del progetto. Gli spazi dei nomi sono derivati dai nomi delle cartelle.                                 |
|                 | `--context-namespace <NAMESPACE>`        | Spazio dei nomi da utilizzare per la `DbContext` classe generata. Nota: esegue l'override di `--namespace` . (Disponibile da EFCore 5.0.0 in poi).        |
| `-f`            | `--force`                                | Sovrascrivere i file esistenti.                                                                                                                                                      |
| `-o`            | `--output-dir <PATH>`                    | Directory in cui inserire i file della classe di entità. I percorsi sono relativi alla directory del progetto.                                                                                       |
| `-n`            | `--namespace <NAMESPACE>`                | Spazio dei nomi da utilizzare per tutte le classi generate. Il valore predefinito è generato dallo spazio dei nomi radice e dalla directory di output. (Disponibile da EFCore 5.0.0 in poi).        |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | Schemi delle tabelle per cui generare i tipi di entità. Per specificare più schemi, ripetere `--schema` per ognuno di essi. Se questa opzione viene omessa, vengono inclusi tutti gli schemi.          |
| `-t`            | `--table <TABLE_NAME>`...                | Tabelle per cui generare i tipi di entità. Per specificare più tabelle, ripetere `-t` o `--table` per ciascuna di esse. Se questa opzione viene omessa, vengono incluse tutte le tabelle.                |
|                 | `--use-database-names`                   | Utilizzare i nomi di tabella e colonna esattamente come vengono visualizzati nel database. Se questa opzione viene omessa, i nomi di database vengono modificati in modo da essere conformi alle convenzioni di stile del nome C#. |
|                 | `--no-onconfiguring`                     | Evita la generazione del `OnConfiguring` metodo nella `DbContext` classe generata. (Disponibile da EFCore 5.0.0 in poi).        |

Nell'esempio seguente vengono assemblati tutti gli schemi e le tabelle e vengono inseriti i nuovi file nella cartella *Models* .

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

Nell'esempio seguente vengono create le impalcature solo per le tabelle selezionate e il contesto viene creato in una cartella separata con un nome e uno spazio dei nomi specificati:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

## <a name="dotnet-ef-migrations-add"></a>Aggiunta di migrazioni DotNet EF

Aggiunge una nuova migrazione.

Argomenti:

| Argomento | Descrizione                |
|:---------|:---------------------------|
| `<NAME>` | Nome della migrazione. |

Opzioni:

|                   | Opzione                             | Descrizione                                                                                                      |
|:------------------|:-----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <nobr>`-o`</nobr> | <nobr>`--output-dir <PATH>`</nobr> | Directory usata per l'output dei file. I percorsi sono relativi alla directory del progetto di destinazione. Il valore predefinito è "Migrations". |
| <nobr>`-n`</nobr> | <nobr>`--namespace <NAMESPACE>`</nobr> | Spazio dei nomi da utilizzare per le classi generate. Il valore predefinito è generato dalla directory di output. (Disponibile da EFCore 5.0.0 in poi). |

## <a name="dotnet-ef-migrations-list"></a>elenco migrazioni di DotNet EF

Elenca le migrazioni disponibili.

## <a name="dotnet-ef-migrations-remove"></a>Rimuovi migrazioni di DotNet EF

Rimuove l'ultima migrazione (esegue il rollback delle modifiche del codice eseguite per la migrazione).

Opzioni:

|                   | Opzione    | Descrizione                                                                     |
|:------------------|:----------|:--------------------------------------------------------------------------------|
| <nobr>`-f`</nobr> | `--force` | Ripristinare la migrazione, ovvero eseguire il rollback delle modifiche applicate al database. |

## <a name="dotnet-ef-migrations-script"></a>script migrazioni DotNet EF

Genera uno script SQL dalle migrazioni.

Argomenti:

| Argomento | Descrizione                                                                                                                                                   |
|:---------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `<FROM>` | Migrazione iniziale. Le migrazioni possono essere identificate in base al nome o all'ID. Il numero 0 è un caso speciale che indica *prima della prima migrazione*. Il valore predefinito è 0. |
| `<TO>`   | Migrazione finale. L'impostazione predefinita è l'ultima migrazione.                                                                                                         |

Opzioni:

|                   | Opzione            | Descrizione                                                        |
|:------------------|:------------------|:-------------------------------------------------------------------|
| <nobr>`-o`</nobr> | `--output <FILE>` | File in cui scrivere lo script.                                   |
| `-i`              | `--idempotent`    | Genera uno script che può essere utilizzato in un database in qualsiasi migrazione. |

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
