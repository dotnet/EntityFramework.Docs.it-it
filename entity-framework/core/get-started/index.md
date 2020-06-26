---
title: Introduzione a EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7181df6ee73b09f196940178ffed38d96b075258
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370318"
---
# <a name="getting-started-with-ef-core"></a>Introduzione a EF Core

In questa esercitazione viene creata un'app console .NET Core che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.

È possibile eseguire l'esercitazione usando Visual Studio in Windows oppure usando l'interfaccia della riga di comando di .NET Core in Windows, macOS o Linux.

[Visualizzare l'esempio di questo articolo su GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).

## <a name="prerequisites"></a>Prerequisiti

Installare il software seguente:

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

* [.NET Core SDK](https://www.microsoft.com/net/download/core).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 versione 16.3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:
  * **Sviluppo multipiattaforma .NET Core** (in **Altri set di strumenti**)

---

## <a name="create-a-new-project"></a>Creare un nuovo progetto

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aprire Visual Studio
* Fare clic su **Crea un nuovo progetto**
* Selezionare **App console (.NET Core)** con il tag **C#** e fare clic su **Avanti**
* Immettere **EFGetStarted** come nome e fare clic su **Crea**

---

## <a name="install-entity-framework-core"></a>Installare Entity Framework Core

Per installare EF Core, installare il pacchetto per i provider di database di EF Core che si vuole usare come destinazione. Questa esercitazione usa SQLite in quanto viene eseguita su tutte le piattaforme supportate da .NET Core. Per un elenco dei provider disponibili, vedere [Provider di database](../providers/index.md).

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Strumenti > Gestione pacchetti NuGet > Console di Gestione pacchetti**
* Eseguire i comandi seguenti:

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

Suggerimento: È anche possibile installare i pacchetti facendo clic con il pulsante destro del mouse sul progetto e scegliendo **Gestisci pacchetti NuGet**

---

## <a name="create-the-model"></a>Creare il modello

Definire una classe di contesto e le classi di entità che costituiscono il modello.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

* Nella directory del progetto creare un file **Model.cs** con il codice seguente

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Aggiungi > Classe**
* Immettere **Model.cs** come nome e fare clic su **Aggiungi**
* Sostituire tutti i contenuti del file con il codice seguente

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core supporta anche il [reverse engineering](../managing-schemas/scaffolding.md) di un modello da un database esistente.

Suggerimento: questa applicazione mantiene intenzionalmente le cose semplici per maggiore chiarezza. Le [stringhe di connessione](../miscellaneous/connection-strings.md) non devono essere archiviate nel codice per le applicazioni di produzione. Potrebbe anche essere necessario suddividere ogni classe C# nel relativo file.

## <a name="create-the-database"></a>Creare il database

La procedura seguente usa le [migrazioni](xref:core/managing-schemas/migrations/index) per creare un database.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

* Eseguire i comandi seguenti:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  Vengono installati [dotnet ef](../miscellaneous/cli/dotnet.md) e il pacchetto di progettazione necessario per eseguire il comando in un progetto. Il comando `migrations` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello. Il comando `database update` crea il database e ne applica la nuova migrazione.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Eseguire i comandi seguenti nella **console di gestione pacchetti (PMC)**

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  Verranno installati gli [strumenti di PMC per EF Core](../miscellaneous/cli/powershell.md). Il comando `Add-Migration` esegue lo scaffolding di una migrazione per creare il set iniziale di tabelle per il modello. Il comando `Update-Database` crea il database e ne applica la nuova migrazione.

---

## <a name="create-read-update--delete"></a>Creazione, lettura, aggiornamento ed eliminazione

* Aprire *Program.cs* e sostituire il contenuto con il codice seguente:

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>Eseguire l'app

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio usa una directory di lavoro incoerente quando si eseguono app console .NET Core. (vedere [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) Questa operazione causa la generazione di un'eccezione: *Nessuna tabella di questo tipo: Blogs*. Per aggiornare la directory di lavoro:

* Fare clic con il pulsante destro del mouse sul progetto e scegliere **Modifica file di progetto**
* Subito dopo la proprietà *TargetFramework* aggiungere quanto segue:

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* Salvare il file

A questo punto è possibile eseguire l'app:

* **Debug > Avvia senza eseguire debug**

---

## <a name="next-steps"></a>Passaggi successivi

* Seguire l'[esercitazione di ASP.NET Core](/aspnet/core/data/ef-rp/intro) per usare EF Core in un'app Web
* Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)
* Usare le [migrazioni](xref:core/managing-schemas/migrations/index) per aggiornare lo schema del database dopo aver modificato il modello
