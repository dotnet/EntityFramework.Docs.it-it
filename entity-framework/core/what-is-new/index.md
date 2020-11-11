---
title: Versioni e pianificazione di EF Core
description: Versioni correnti di EF Core e dettagli sulla pianificazione per le versioni future
author: ajcvickers
ms.date: 10/11/2020
uid: core/what-is-new/index
ms.openlocfilehash: 983a8b21d353481df4f1db232d00761e070d893b
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503141"
---
# <a name="ef-core-releases-and-planning"></a>Versioni e pianificazione di EF Core

## <a name="stable-releases"></a>Versioni stabili

| Versione | Framework di destinazione | Ultima versione supportata | Collegamenti
|:--------|------------------|-----------------|------
| [EF Core 5.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.1 | Metà febbraio, 2022 | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-ef-core-5-0/) / [Modifiche che causano un'interruzione](xref:core/what-is-new/ef-core-5.0/breaking-changes)
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.10) | .NET Standard 2.0 | 3 dicembre 2022 (LTS) | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | Scaduto il 3 marzo 2020 | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [Modifiche che causano un'interruzione](xref:core/what-is-new/ef-core-3.x/breaking-changes)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | Scaduto il 23 dicembre 2019 | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 21 agosto 2021 (LTS) | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | Scaduto il 1° ottobre 2018 | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | Scaduto il 27 giugno 2019 | [Annuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | Scaduto il 27 giugno 2019 | [Annuncio](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

Per informazioni sulle specifiche piattaforme supportate da ogni versione di EF Core, vedere [Piattaforme supportate](xref:core/miscellaneous/platforms).

Per informazioni sulla scadenza del supporto e sulle versioni con supporto a lungo termine (LTS), vedere [Criteri di supporto per .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="guidance-on-updating-to-new-releases"></a>Linee guida per l'aggiornamento alle nuove versioni

* Le versioni supportate includono patch per la sicurezza e altri bug critici. Usare sempre la patch più recente di una determinata versione. Ad esempio, per EF Core 2,1, utilizzare 2.1. x per l'oggetto ' x ' più elevato disponibile.
* Gli aggiornamenti delle versioni principali (ad esempio, da EF Core 2 a EF Core 3) spesso presentano modifiche che causano un'interruzione. Quando si effettua l'aggiornamento tra versioni principali, è consigliabile eseguire test approfonditi. Usare i collegamenti precedenti relativi alle modifiche che causano un'interruzione per informazioni aggiuntive sulla gestione di tali modifiche.
* Gli aggiornamenti delle versioni secondarie in genere non contengono modifiche che causano un'interruzione. È comunque consigliabile eseguire test approfonditi poiché le nuove funzionalità possono introdurre regressioni.

## <a name="release-planning-and-schedules"></a>Pianificazione delle versioni

Le versioni EF Core sono allineate con la [pianificazione della distribuzione di .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md).

Le versioni patch vengono in genere distribuite mensilmente, ma hanno un lead time lungo.
Stiamo lavorando per migliorare questo aspetto.

Per altre informazioni su come viene deciso quali elementi rilasciare in ogni versione, vedere il [processo di pianificazione delle versioni](xref:core/what-is-new/release-planning).
In genere non viene eseguita una pianificazione dettagliata a lungo termine, oltre la versione principale o secondaria successiva.

## <a name="ef-core-60"></a>EF Core 6,0

La versione stabile successiva pianificata è **EF Core 6,0** , pianificata per il **2021 novembre**.

Attualmente si sta lavorando a un piano di alto livello basato sul [processo di pianificazione della versione](xref:core/what-is-new/release-planning).

I commenti e i suggerimenti dei clienti sulla pianificazione sono importanti.
Il modo migliore per indicare l'importanza di un problema consiste nel votare (pollice in su 👍) per tale problema in GitHub.
Questi dati verranno inclusi nel processo di pianificazione per la versione successiva.

### <a name="get-it-now"></a>Scarica adesso

I pacchetti EF Core 6,0 sono **ora disponibili** come

* [Build giornaliere](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md)
  * Tutte le funzionalità e le correzioni di bug più recenti. Generalmente molto stabile. Più di 57000 test eseguiti su ogni build.

Inoltre, quando si procede, le anteprime frequenti verranno inviate a NuGet. Si noti che le anteprime sono in ritardo rispetto alle compilazioni quotidiane, ma sono testate per funzionare con le anteprime ASP.NET Core e .NET Core corrispondenti.

L'uso delle anteprime o delle build giornaliere è un ottimo modo per individuare i problemi e fornire commenti e suggerimenti il prima possibile.
Prima viene ricevuto tale feedback, maggiori saranno le probabilità di poter intervenire prima della successiva versione ufficiale.
