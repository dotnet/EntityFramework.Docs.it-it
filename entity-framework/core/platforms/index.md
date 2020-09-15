---
title: Implementazioni di .NET supportate - EF Core
description: Informazioni sulle piattaforme supportate nelle versioni Entity Framework Core
author: bricelam
ms.date: 06/26/2020
uid: core/platforms/index
ms.openlocfilehash: f57002af869c16eeedf931085f5e5ff9e72e2984
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616652"
---
# <a name="net-implementations-supported-by-ef-core"></a>Implementazioni di .NET supportate da Entity Framework Core

Microsoft vuole che EF Core sia disponibile per gli sviluppatori in tutte le implementazioni di .NET moderne e sta ancora lavorando per questo obiettivo. Mentre il supporto di EF Core in .NET Core è accompagnato da test automatizzati ed è noto che molte applicazioni lo usano correttamente, esistono alcuni problemi per Mono, Xamarin e UWP.

## <a name="overview"></a>Panoramica

La tabella seguente offre indicazioni per ogni implementazione .NET:

| EF Core                       | 2.1 e 3.1 | 5,0             |
|:------------------------------|:------------|:----------------|
| .NET Standard                 | 2.0         | 2.1             |
| .NET Core                     | 2.0         | 3,0             |
| .NET Framework<sup>(1)</sup>  | 4.7.2       | (non supportato) |
| Mono                          | 5.4         | 6.4             |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       | 12.16           |
| Xamarin.Mac<sup>(2)</sup>     | 3.8         | 5.16            |
| Xamarin.Android<sup>(2)</sup> | 8.0         | 10,0            |
| UWP<sup>(3)</sup>             | 10.0.16299  | TBD             |
| Unity<sup>(4)</sup>           | 2018.1      | TBD             |

<sup>(1)</sup> Vedere la sezione [.NET Framework](#net-framework) di seguito.

<sup>(2)</sup> Esistono alcuni problemi e limitazioni noti per Xamarin, che possono impedire il corretto funzionamento di alcune applicazioni sviluppate usando EF Core. Controllare l'elenco dei [problemi attivi](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) per informazioni sulle soluzioni alternative.

<sup>3 </sup> Si consiglia EF Core 2.0.1 e versioni più recenti. Installare il [pacchetto .NET Core UWP 6.x](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/). Vedere la sezione [Piattaforma UWP (Universal Windows Platform)](#universal-windows-platform) di questo articolo.

<sup>(4)</sup> Esistono problemi e limitazioni noti con Unity. Controllare l'elenco dei [problemi attivi](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).

## <a name="net-framework"></a>.NET Framework

Le applicazioni che hanno .NET Framework come destinazione potrebbero richiedere modifiche per funzionare con le librerie .NET Standard:

Modificare il file di progetto e assicurarsi che la voce seguente sia visualizzata nel gruppo di proprietà iniziale:

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

Per i progetti di test, assicurarsi anche che sia presente la voce seguente:

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

Se si vuole usare una versione precedente di Visual Studio, assicurarsi di [aggiornare il client NuGet alla versione 3.6.0](https://www.nuget.org/downloads) per lavorare con le librerie .NET Standard 2.0.

Si consiglia anche di eseguire la migrazione da packages.config NuGet a PackageReference, se possibile. Aggiungere la proprietà seguente al file di progetto:

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Le versioni precedenti di EF Core e .NET UWP includono numerosi problemi di compatibilità, in particolare con le applicazioni compilate con la toolchain .NET Native. La nuova versione di .NET UWP aggiunge il supporto per .NET 2.0 Standard e include .NET 2.0 Native, che consente di risolvere la maggior parte dei problemi di compatibilità segnalati in precedenza. EF Core 2.0.1 è stato testato in modo più approfondito con la piattaforma UWP ma i test non sono automatizzati.

Quando si usa EF Core nella piattaforma UWP:

* Per ottimizzare le prestazioni delle query, evitare i tipi anonimi nelle query LINQ. Per distribuire un'applicazione UWP nell'App Store, un'applicazione deve essere compilata con .NET Native. Le query con tipi anonimi hanno prestazioni peggiori in .NET Native.

* Per ottimizzare le prestazioni di `SaveChanges()`, usare [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) e implementare [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging ](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) e [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) nei tipi di entità.

## <a name="report-issues"></a>Segnalare i problemi

Per qualsiasi combinazione che non funziona come previsto, si consiglia di segnalare nuovi problemi nello [strumento di gestione dei problemi di EF Core](https://github.com/aspnet/entityframeworkcore/issues/new). Per problemi specifici di Xamarin, usare lo strumento di gestione dei problemi per [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) o [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).
