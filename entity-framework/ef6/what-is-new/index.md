---
title: Novità - EF6
description: Novità di Entity Framework 6
author: divega
ms.date: 09/12/2019
uid: ef6/what-is-new/index
ms.openlocfilehash: 3663276174b7f17701756a82b592ebb971516719
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073656"
---
# <a name="whats-new-in-ef6"></a>Novità di EF6

Per ottenere le funzionalità più recenti e il livello di stabilità più elevato è consigliabile usare l'ultima versione rilasciata di Entity Framework.
È possibile, tuttavia, usare una versione precedente o provare i miglioramenti resi disponibili nella versione provvisoria più recente.
Per installare versioni specifiche di Entity Framework, vedere [Get Entity Framework](xref:ef6/fundamentals/install) (Ottenere Entity Framework).

## <a name="ef-640"></a>EF 6.4.0

Il runtime di EF 6.4.0 è stato rilasciato in NuGet nel mese di dicembre 2019. L'obiettivo principale di EF 6.4 è quello di rifinire le funzionalità e gli scenari forniti in EF 6.3. Vedere l'[elenco delle correzioni importanti](https://github.com/dotnet/ef6/milestone/14?closed=1) su GitHub.

## <a name="ef-630"></a>EF 6.3.0

Il runtime di EF 6.3.0 è stato rilasciato in NuGet nel mese di settembre 2019. L'obiettivo principale di questa versione è quello di semplificare la migrazione delle applicazioni esistenti che usano EF 6 a .NET Core 3.0. La community ha anche contribuito a diverse correzioni di bug e miglioramenti. Per informazioni dettagliate, vedere i problemi chiusi in ogni [fase cardine](https://github.com/aspnet/EntityFramework6/milestones?state=closed) della versione 6.3.0. Ecco alcuni dei più importanti:

- Supporto per .NET Core 3.0
  - Il pacchetto EntityFramework ora ha come destinazione .NET Standard 2.1 oltre a .NET Framework 4.x.
  - Questo significa che EF 6.3 è multipiattaforma e supportato in altri sistemi operativi oltre a Windows, come Linux e macOS.
  - I comandi per le migrazioni sono stati riscritti per l'esecuzione out-of-process e supportano i progetti in stile SDK.
- Supporto di HierarchyId di SQL Server.
- Compatibilità migliorata con Roslyn e NuGet PackageReference.
- Aggiunta dell'utilità `ef6.exe` per l'abilitazione, l'aggiunta, l'esecuzione di script e l'applicazione di migrazioni da assembly. Sostituisce `migrate.exe`.

### <a name="ef-designer-support"></a>Supporto di EF Designer

Attualmente non è disponibile alcun supporto per l'uso di EF Designer direttamente nei progetti .NET Core o .NET Standard oppure nei progetti .NET Framework in stile SDK. 

Per ovviare a questa limitazione, è possibile aggiungere il file EDMX e le classi generate per le entità e DbContext come file collegati a un progetto .NET Core 3.0 o .NET Standard 2.1 nella stessa soluzione.

Nel file di progetto i file collegati appariranno come segue:

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

Si noti che il file EDMX è collegato all'azione di compilazione EntityDeploy. Si tratta di un'attività MSBuild speciale (ora inclusa nel pacchetto EF 6.3) che gestisce l'aggiunta del modello EF nell'assembly di destinazione come risorse incorporate (o la copia come file nella cartella di output, a seconda dell'impostazione di elaborazione degli artefatti dei metadati in EDMX). Per altri dettagli su come ottenere questa configurazione, vedere l'[esempio EDMX .NET Core](https://aka.ms/EdmxDotNetCoreSample).

Avviso: assicurarsi che il progetto .NET Framework vecchio stile (ovvero non in stile SDK), che definisce il file con estensione edmx "reale" venga _prima_ del progetto che definisce il collegamento all'interno del file con estensione sln. In caso contrario, quando si apre il file con estensione edmx nella finestra di progettazione, viene visualizzato il messaggio di errore "Entity Framework non è disponibile nel framework di destinazione attualmente specificato per il progetto. È possibile modificare il framework di destinazione del progetto oppure modificare il modello in XmlEditor".

## <a name="past-releases"></a>Versioni precedenti

La pagina [Versioni precedenti](xref:ef6/what-is-new/past-releases) contiene un archivio di tutte le versioni precedenti di Entity Framework e delle funzionalità principali introdotte in ogni versione.
