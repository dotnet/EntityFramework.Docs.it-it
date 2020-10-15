---
title: Panoramica della registrazione e dell'intercettazione-EF Core
description: Panoramica di registrazione, eventi, intercettori e diagnostica per EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066614"
---
# <a name="overview-of-logging-and-interception"></a>Panoramica della registrazione e dell'intercettazione

Entity Framework Core (EF Core) contiene diversi meccanismi per la generazione di log, la risposta agli eventi e l'ottenimento della diagnostica. Ognuno di questi è adattato a situazioni diverse ed è importante selezionare il meccanismo migliore per l'attività a disposizione, anche quando possono funzionare più meccanismi. Ad esempio, un intercettore di database può essere usato per registrare SQL, ma è meglio gestito da uno dei meccanismi specifici per la registrazione. In questa pagina viene presentata una panoramica di ognuno di questi meccanismi e viene descritto quando è necessario utilizzare ciascuna di esse.

## <a name="simple-logging"></a>Registrazione semplice

> [!NOTE]
> Questa funzionalità è stata aggiunta in EF Core 5,0.

È possibile accedere ai log di EF Core da qualsiasi tipo di applicazione tramite l'uso di [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> Quando si [configura un'istanza di DbContext](xref:core/miscellaneous/configuring-dbcontext). Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Ad esempio:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Questo concetto è simile a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.

Per ulteriori informazioni, vedere [registrazione semplice](xref:core/miscellaneous/events/simple-logging) .
