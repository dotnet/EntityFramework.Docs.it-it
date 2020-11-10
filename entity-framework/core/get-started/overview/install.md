---
title: Installazione di Entity Framework Core - EF Core
description: Istruzioni di installazione per Entity Framework Core
author: bricelam
ms.date: 08/06/2017
uid: core/get-started/overview/install
ms.openlocfilehash: 039e5339a6dba57f6a8f33acba6e467f8d6439c9
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431556"
---
# <a name="installing-entity-framework-core"></a>Installazione di Entity Framework Core

## <a name="prerequisites"></a>Prerequisiti

* EF Core è una libreria [.NET Standard 2.0](/dotnet/standard/net-standard). Pertanto, EF Core richiede un'implementazione di .NET che supporta .NET Standard 2.0 per l'esecuzione. EF Core può anche essere indicato come riferimento da altre librerie .NET Standard 2.0.

* Ad esempio, è possibile usare EF Core per sviluppare app la cui destinazione è .NET Core. La compilazione di app .NET Core richiede [.NET Core SDK](https://dotnet.microsoft.com/download). Facoltativamente, è anche possibile usare un ambiente di sviluppo come [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac), o [Visual Studio Code](https://code.visualstudio.com). Per altre informazioni, vedere [Introduzione a .NET Core](/dotnet/core/get-started).

* È possibile usare EF Core per lo sviluppo di applicazioni in Windows con Visual Studio. È consigliata l'ultima versione di [Visual Studio](https://visualstudio.microsoft.com/vs).

* EF Core può essere eseguito in altre implementazioni di .NET come [Xamarin](https://dotnet.microsoft.com/apps/xamarin) e .NET Native, tuttavia in pratica tali implementazioni presentano delle limitazioni di runtime che potrebbero incidere sull'efficacia di EF Core sulla propria app. Per altre informazioni, vedere [.Implementazioni di .NET supportate da EF Core](xref:core/miscellaneous/platforms).

* Infine, diversi provider di database potrebbero richiedere versioni del motore di database, implementazioni di .NET o sistemi operativi specifici. Assicurarsi che sia disponibile un [provider di database EF Core](xref:core/providers/index) che supporta l'ambiente corretto per l'applicazione.

## <a name="get-the-entity-framework-core-runtime"></a>Ottenere il runtime Entity Framework Core

Per aggiungere EF Core a un'applicazione, installare il pacchetto NuGet del provider di database da usare.

Se si sta creando un'applicazione ASP.NET Core, non è necessario installare i provider interni alla memoria e quelli di SQL Server. Tali provider sono inclusi nelle versioni correnti di ASP.NET Core, insieme al runtime di EF Core.

Per installare o aggiornare i pacchetti NuGet, è possibile usare l'interfaccia della riga di comando di .NET Core, la finestra di dialogo Gestione pacchetti di Visual Studio o la console di Gestione pacchetti di Visual Studio.

### <a name="net-core-cli"></a>Interfaccia della riga di comando di .NET Core

* Usare il comando di interfaccia della riga di comando di .NET Core seguente dalla riga di comando del sistema operativo per installare o aggiornare il provider di EF Core SQL Server:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* È possibile indicare una versione specifica nel comando `dotnet add package` mediante il modificatore `-v`. Ad esempio, per installare i pacchetti di EF Core 2.2.0, aggiungere `-v 2.2.0` al comando.

Per altre informazioni, vedere [Strumenti dell'interfaccia della riga di comando (CLI) di .NET ](/dotnet/core/tools/).

### <a name="visual-studio-nuget-package-manager-dialog"></a>Finestra di dialogo Gestione pacchetti NuGet in Visual Studio

* Nel menu Visual Studio, selezionare **Progetto > Gestisci pacchetti NuGet**

* Fare clic sulla scheda **Sfoglia** o sulla scheda **Aggiornamenti**

* Per installare o aggiornare il provider SQL Server selezionare il pacchetto `Microsoft.EntityFrameworkCore.SqlServer` e confermare.

Per altre informazioni, vedere [Finestra di dialogo Gestione pacchetti NuGet](/nuget/tools/package-manager-ui).

### <a name="visual-studio-nuget-package-manager-console"></a>Console di Gestione pacchetti NuGet di Visual Studio

* Nel menu Visual Studio, selezionare **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**

* Per installare il provider SQL Server eseguire il comando seguente nella console di Gestione pacchetti:

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* Per aggiornare il provider, usare il comando `Update-Package`.

* Per indicare una versione specifica usare il modificatore `-Version`. Ad esempio, per installare i pacchetti di EF Core 2.2.0 aggiungere `-Version 2.2.0` ai comandi

Per altre informazioni, vedere [Console di Gestione pacchetti](/nuget/tools/package-manager-console).

## <a name="get-the-entity-framework-core-tools"></a>Ottenere gli strumenti di Entity Framework Core

È possibile installare gli strumenti per eseguire attività associate a EF Core nel progetto, come creare e applicare le migrazioni del database o creare un modello di EF Core basato su un database esistente.

Sono disponibili due set di strumenti:

* Gli [strumenti dell'interfaccia della riga di comando (CLI) di .NET Core](xref:core/cli/dotnet) possono essere usati in Windows, Linux o macOS. Questi comandi iniziano con `dotnet ef`.

* Gli [strumenti della console di Gestione pacchetti](xref:core/cli/powershell) vengono eseguiti in Visual Studio su Windows. Questi comandi iniziano con un verbo, ad esempio `Add-Migration`, `Update-Database`.

Sebbene sia possibile usare anche i comandi `dotnet ef` della console di Gestione pacchetti, quando si usa Visual Studio è consigliabile usare gli strumenti della console di Gestione pacchetti:

* i comandi funzionano automaticamente con il progetto corrente selezionato nella Console di Gestione pacchetti in Visual Studio, senza richiedere il passaggio manuale da una directory all'altra.

* Dopo il completamento del comando, i file generati vengono aperti automaticamente in Visual Studio.

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a>Ottenere gli strumenti dell'interfaccia della riga di comando di .NET Core

Gli strumenti CLI di .NET core richiedono .NET Core SDK, indicato in precedenza nei [Prerequisiti](#prerequisites).

* `dotnet ef` deve essere installato come strumento globale o locale. La maggior parte degli sviluppatori preferisce installare `dotnet ef` come strumento globale usando il comando seguente:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  `dotnet ef` può essere usato anche come strumento locale. Per usarlo come strumento locale, ripristinare le dipendenze di un progetto che lo dichiara come dipendenza degli strumenti usando un [file manifesto dello strumento](/dotnet/core/tools/global-tools#install-a-local-tool).

* Per aggiornare gli strumenti, utilizzare il `dotnet tool update` comando.

* Installare il pacchetto più recente `Microsoft.EntityFrameworkCore.Design` .

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

> [!IMPORTANT]
> Usare sempre le versioni dei pacchetti degli strumenti corrispondenti alla versione principale dei pacchetti di runtime.

### <a name="get-the-package-manager-console-tools"></a>Ottenere gli strumenti della console di Gestione pacchetti

Per ottenere gli strumenti della console di Gestione pacchetti per EF Core, installare il pacchetto `Microsoft.EntityFrameworkCore.Tools`. Ad esempio, in Visual Studio:

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Per le app ASP.NET Core, questo pacchetto è incluso automaticamente.

## <a name="upgrading-to-the-latest-ef-core"></a>Aggiornamento alla versione più recente di EF Core

* Ogni volta che viene rilasciata una nuova versione di EF Core, viene rilasciata anche una nuova versione dei provider che fanno parte del progetto EF Core, come Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite e Microsoft.EntityFrameworkCore.InMemory. È possibile eseguire soltanto l'aggiornamento alla nuova versione del provider per ottenere tutti i miglioramenti.

* EF Core, insieme a SQL Server e ai provider interni alla memoria sono inclusi nelle versioni correnti di ASP.NET Core. Per aggiornare un'applicazione ASP.NET Core esistente a una versione più recente di EF Core, eseguire sempre l'aggiornamento della versione di ASP.NET Core.

* Se si deve aggiornare un'applicazione che usa un provider di database di terze parti, verificare sempre se è disponibile un aggiornamento del provider compatibile con la versione di Entity Framework Core che si vuole usare. I provider di database per la versione 1,0, ad esempio, non sono compatibili con la versione 2,0 del runtime EF Core.

* I provider di terze parti per EF Core in genere non rilasciano versioni di patch insieme al runtime di EF Core. Per aggiornare un'applicazione che usa un provider di terze parti a una versione patch di EF Core, potrebbe essere necessario aggiungere un riferimento diretto ai singoli componenti di runtime di EF Core, quali Microsoft.EntityFrameworkCore, e Microsoft.EntityFrameworkCore.Relational.
