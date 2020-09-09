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
# <a name="design-time-services"></a>Servizi in fase di progettazione

Alcuni servizi utilizzati dagli strumenti vengono utilizzati solo in fase di progettazione. Questi servizi vengono gestiti separatamente dai servizi di runtime di EF Core per impedire che vengano distribuiti con l'app. Per eseguire l'override di uno di questi servizi, ad esempio il servizio per generare file di migrazione, aggiungere un'implementazione di `IDesignTimeServices` al progetto di avvio.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
