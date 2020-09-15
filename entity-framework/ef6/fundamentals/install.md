---
title: Ottenere Entity Framework-EF6
description: Ottenere Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/install
ms.openlocfilehash: 9254f835f5007a213c49557165f6830c30711f1f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072785"
---
# <a name="get-entity-framework"></a>Ottenere Entity Framework
Entity Framework è costituito da EF Tools per Visual Studio e dal runtime di EF.

## <a name="ef-tools-for-visual-studio"></a>EF Tools per Visual Studio

I Entity Framework Tools per Visual Studio includono EF designer e la procedura guidata modello EF e sono necessari per i flussi di lavoro First e Model First del database. Gli strumenti EF sono inclusi in tutte le versioni recenti di Visual Studio. Se si esegue un'installazione personalizzata di Visual Studio, è necessario assicurarsi che sia selezionato l'elemento "Entity Framework 6 Tools" scegliendo un carico di lavoro che lo includa o selezionandolo come singolo componente.

Per alcune versioni precedenti di Visual Studio, gli strumenti EF aggiornati sono disponibili come download. Vedere [versioni di Visual Studio](xref:ef6/what-is-new/visual-studio) per istruzioni su come ottenere la versione più recente degli strumenti EF per la versione di Visual Studio.

## <a name="ef-runtime"></a>Runtime EF

La versione più recente di Entity Framework è disponibile come [pacchetto NuGet EntityFramework](https://nuget.org/packages/EntityFramework/). Se non si ha familiarità con gestione pacchetti NuGet, si consiglia di leggere la [Panoramica di NuGet](/nuget/consume-packages/overview-and-workflow).

### <a name="installing-the-ef-nuget-package"></a>Installazione del pacchetto NuGet EF

È possibile installare il pacchetto EntityFramework facendo clic con il pulsante destro del mouse sulla cartella **riferimenti** del progetto e scegliendo **Gestisci pacchetti NuGet...**

![Manage NuGet Packages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>Installazione dalla console di gestione pacchetti

In alternativa, è possibile installare EntityFramework eseguendo il comando seguente nella console di [Gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>Installazione di una versione specifica di EF

Da EF 4,1 in poi, le nuove versioni di EF Runtime sono state rilasciate come [pacchetto NuGet EntityFramework](https://www.nuget.org/packages/EntityFramework/). È possibile aggiungere una qualsiasi di queste versioni a un progetto basato su .NET Framework eseguendo il comando seguente nella [console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)di Visual Studio:

``` powershell
Install-Package EntityFramework -Version <number>
```

Si noti che `<number>` rappresenta la versione specifica di EF da installare. Ad esempio, 6.2.0 è la versione di numero per EF 6,2.   

I runtime EF prima del 4,1 facevano parte di .NET Framework e non possono essere installati separatamente.

### <a name="installing-the-latest-preview"></a>Installazione dell'anteprima più recente

I metodi precedenti forniranno la versione più recente di Entity Framework supportata. Spesso sono disponibili versioni non definitive di Entity Framework che ci piacerebbero provare e inviare commenti e suggerimenti su.

Per installare l'anteprima più recente di EntityFramework, è possibile selezionare **Includi versione preliminare** nella finestra Gestisci pacchetti NuGet. Se non sono disponibili versioni non definitive, verrà automaticamente scaricata la versione di Entity Framework più recente supportata.

![Includi versione preliminare](~/ef6/media/includeprerelease.png)

In alternativa, è possibile eseguire il comando seguente nella [console di gestione pacchetti](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).

``` powershell
Install-Package EntityFramework -Pre
```
