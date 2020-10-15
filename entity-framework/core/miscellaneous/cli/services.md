---
title: Servizi in fase di progettazione-EF Core
description: Informazioni su Entity Framework Core servizi in fase di progettazione
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061944"
---
# <a name="design-time-services"></a>Servizi in fase di progettazione

Alcuni servizi utilizzati dagli strumenti vengono utilizzati solo in fase di progettazione. Questi servizi vengono gestiti separatamente dai servizi di runtime di EF Core per impedire che vengano distribuiti con l'app. Per eseguire l'override di uno di questi servizi, ad esempio il servizio per generare file di migrazione, aggiungere un'implementazione di `IDesignTimeServices` al progetto di avvio.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
