---
title: Panoramica della registrazione e dell'intercettazione-EF Core
description: Panoramica di registrazione, eventi, intercettori e diagnostica per EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: d85a506167661523bf70b62d3a075a6248180d11
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635679"
---
# <a name="overview-of-logging-and-interception"></a>Panoramica della registrazione e dell'intercettazione

Entity Framework Core (EF Core) contiene diversi meccanismi per la generazione di log, la risposta agli eventi e l'ottenimento della diagnostica. Ognuno di questi è adattato a situazioni diverse ed è importante selezionare il meccanismo migliore per l'attività a disposizione, anche quando più meccanismi potrebbero funzionare. Ad esempio, un intercettore di database può essere usato per registrare SQL, ma è meglio gestito da uno dei meccanismi personalizzati per la registrazione. In questa pagina viene presentata una panoramica di ognuno di questi meccanismi e viene descritto quando è necessario utilizzare ciascuna di esse.

## <a name="quick-reference"></a>Riferimento rapido

La tabella seguente fornisce un riferimento rapido per le differenze tra i meccanismi descritti qui.

| Mechanism |  Async | Ambito | Registrato | Uso previsto
|:----------|--------|-------|------------|-------------
| Registrazione semplice | No | Per contesto | Configurazione del contesto | Registrazione in fase di sviluppo
| Microsoft.Extensions.Logging | No | Per contesto * | D.I. o configurazione del contesto | Registrazione di produzione
| Eventi | No | Per contesto | In qualsiasi momento | Reazione a eventi EF
| Intercettori | Sì | Per contesto | Configurazione del contesto | Manipolazione di operazioni EF
| Listener di diagnostica | No | Process | A livello globale | Diagnostica applicazioni

* Viene in genere `Microsoft.Extensions.Logging` configurato per applicazione tramite l'inserimento di dipendenze, tuttavia, a livello di EF, ogni contesto _può_ essere configurato con un logger diverso, se necessario.

## <a name="simple-logging"></a>Registrazione semplice

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

È possibile accedere ai log di EF Core da qualsiasi tipo di applicazione tramite l'utilizzo di <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> quando si [configura un'istanza di DbContext](xref:core/dbcontext-configuration/index). Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Questo concetto è simile a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.

Per ulteriori informazioni, vedere [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) .

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) è un meccanismo di registrazione estendibile con provider plug-in per molti sistemi di registrazione comuni. EF Core si integra completamente con `Microsoft.Extensions.Logging` e questa forma di registrazione viene utilizzata per impostazione predefinita per le applicazioni ASP.NET Core.

Per ulteriori informazioni, vedere [utilizzo di Microsoft. Extensions. Logging in EF Core](xref:core/logging-events-diagnostics/extensions-logging) .

## <a name="events"></a>Eventi

> [!NOTE]
> Altri eventi sono stati introdotti nella EF Core 5,0.

EF Core espone [gli eventi .NET](/dotnet/standard/events/) in modo che fungano da callback quando si verificano determinate operazioni nel codice di EF core. Gli eventi sono più semplici degli intercettori e consentono una registrazione più flessibile. Tuttavia, sono solo sincronizzati e pertanto non possono eseguire operazioni di I/O asincrone non bloccanti.

Gli eventi vengono registrati per ogni istanza di DbContext e questa registrazione può essere eseguita in qualsiasi momento. Usare un [listener di diagnostica](xref:core/logging-events-diagnostics/diagnostic-listeners) per ottenere le stesse informazioni, ma per tutte le istanze di DbContext nel processo.

Per ulteriori informazioni, vedere [eventi .NET in EF Core](xref:core/logging-events-diagnostics/events) .

## <a name="interception"></a>Interception

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 3,0. Gli intercettori aggiuntivi sono stati introdotti nella EF Core 5,0.

Gli intercettori di EF Core consentono l'intercettazione, la modifica e/o l'eliminazione di operazioni EF Core. Sono incluse operazioni di database di basso livello, ad esempio l'esecuzione di un comando, nonché operazioni di livello superiore, ad esempio chiamate a SaveChanges.

Gli intercettori sono diversi dalla registrazione e dalla diagnostica in quanto consentono la modifica o l'eliminazione dell'operazione intercettata. La registrazione [semplice](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) è una scelta migliore per la registrazione.

Gli intercettori vengono registrati per ogni istanza di DbContext al momento della configurazione del contesto. Usare un [listener di diagnostica](xref:core/logging-events-diagnostics/diagnostic-listeners) per ottenere le stesse informazioni, ma per tutte le istanze di DbContext nel processo.

Per ulteriori informazioni, vedere [intercettazione](xref:core/logging-events-diagnostics/interceptors) .

## <a name="diagnostic-listeners"></a>Listener diagnostici

I listener di diagnostica consentono l'ascolto di qualsiasi evento EF Core che si verifica nel processo .NET corrente.

I listener di diagnostica non sono adatti per ottenere gli eventi da una singola istanza di DbContext. EF Core gli intercettori forniscono l'accesso agli stessi eventi con registrazione per contesto.

I listener di diagnostica non sono progettati per la registrazione. La registrazione [semplice](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) è una scelta migliore per la registrazione.

Per ulteriori informazioni, vedere [utilizzo dei listener di diagnostica in EF Core](xref:core/logging-events-diagnostics/diagnostic-listeners) .
