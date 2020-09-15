---
title: Porting da EF6 a EF Core-porting di un modello basato su EDMX-EF
description: Informazioni specifiche sul porting di un'applicazione modello basata su EDMX Entity Framework 6 per Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 7bd832f459ae3893e6a90e8483c95a41ca13f9ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070003"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a>Porting di un modello basato su EDMX EF6 per EF Core

EF Core non supporta il formato di file EDMX per i modelli. L'opzione migliore per trasferire questi modelli consiste nel generare un nuovo modello basato sul codice dal database per l'applicazione.

## <a name="install-ef-core-nuget-packages"></a>Installare EF Core pacchetti NuGet

Installare il pacchetto NuGet `Microsoft.EntityFrameworkCore.Tools`.

## <a name="regenerate-the-model"></a>Rigenera il modello

È ora possibile usare la funzionalità di reverse engineering per creare un modello basato sul database esistente.

Eseguire il comando seguente nella console di gestione pacchetti (strumenti-> gestione pacchetti NuGet-> console di gestione pacchetti). Vedere [console di gestione pacchetti (Visual Studio)](xref:core/miscellaneous/cli/powershell) per le opzioni di comando per l'impalcatura di un subset di tabelle e così via.

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

Ad esempio, di seguito è riportato il comando per eseguire l'impalcatura di un modello dal database blogging nell'istanza di SQL Server database locale.

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a>Rimuovi modello EF6

A questo punto è necessario rimuovere il modello EF6 dall'applicazione.

È consigliabile lasciare installato il pacchetto NuGet EF6 (EntityFramework), perché EF Core e EF6 possono essere utilizzati side-by-side nella stessa applicazione. Tuttavia, se non si intende usare EF6 in tutte le aree dell'applicazione, la disinstallazione del pacchetto consente di ottenere errori di compilazione su frammenti di codice che richiedono attenzione.

## <a name="update-your-code"></a>Aggiornare il codice

A questo punto, si tratta di risolvere gli errori di compilazione e di esaminare il codice per verificare se il comportamento cambia tra EF6 e EF Core avrà un effetto.

## <a name="test-the-port"></a>Testare la porta

Solo perché l'applicazione viene compilata, non significa che la porta è stata eseguita correttamente per EF Core. È necessario testare tutte le aree dell'applicazione per assicurarsi che nessuna delle modifiche del comportamento abbia avuto un impatto negativo sull'applicazione.
