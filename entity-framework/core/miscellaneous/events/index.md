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
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="2d701-103">Panoramica della registrazione e dell'intercettazione</span><span class="sxs-lookup"><span data-stu-id="2d701-103">Overview of logging and interception</span></span>

<span data-ttu-id="2d701-104">Entity Framework Core (EF Core) contiene diversi meccanismi per la generazione di log, la risposta agli eventi e l'ottenimento della diagnostica.</span><span class="sxs-lookup"><span data-stu-id="2d701-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="2d701-105">Ognuno di questi è adattato a situazioni diverse ed è importante selezionare il meccanismo migliore per l'attività a disposizione, anche quando possono funzionare più meccanismi.</span><span class="sxs-lookup"><span data-stu-id="2d701-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="2d701-106">Ad esempio, un intercettore di database può essere usato per registrare SQL, ma è meglio gestito da uno dei meccanismi specifici per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="2d701-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="2d701-107">In questa pagina viene presentata una panoramica di ognuno di questi meccanismi e viene descritto quando è necessario utilizzare ciascuna di esse.</span><span class="sxs-lookup"><span data-stu-id="2d701-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="2d701-108">Registrazione semplice</span><span class="sxs-lookup"><span data-stu-id="2d701-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="2d701-109">Questa funzionalità è stata aggiunta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="2d701-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="2d701-110">È possibile accedere ai log di EF Core da qualsiasi tipo di applicazione tramite l'uso di [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="2d701-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="2d701-111">Quando si [configura un'istanza di DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2d701-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="2d701-112">Questa configurazione viene in genere eseguita in un override di <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="2d701-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="2d701-113">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="2d701-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="2d701-114">Questo concetto è simile a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span><span class="sxs-lookup"><span data-stu-id="2d701-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="2d701-115">Per ulteriori informazioni, vedere [registrazione semplice](xref:core/miscellaneous/events/simple-logging) .</span><span class="sxs-lookup"><span data-stu-id="2d701-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>
