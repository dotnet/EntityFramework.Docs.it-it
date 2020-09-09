---
title: Servizi in fase di progettazione-EF Core
description: Informazioni su Entity Framework Core servizi in fase di progettazione
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617851"
---
# <a name="design-time-services"></a><span data-ttu-id="60c68-103">Servizi in fase di progettazione</span><span class="sxs-lookup"><span data-stu-id="60c68-103">Design-time services</span></span>

<span data-ttu-id="60c68-104">Alcuni servizi utilizzati dagli strumenti vengono utilizzati solo in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="60c68-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="60c68-105">Questi servizi vengono gestiti separatamente dai servizi di runtime di EF Core per impedire che vengano distribuiti con l'app.</span><span class="sxs-lookup"><span data-stu-id="60c68-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="60c68-106">Per eseguire l'override di uno di questi servizi, ad esempio il servizio per generare file di migrazione, aggiungere un'implementazione di `IDesignTimeServices` al progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="60c68-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
