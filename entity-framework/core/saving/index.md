---
title: Salvataggio dei dati - EF Core
description: Panoramica del salvataggio dei dati con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 7a31f92aff20f4026b984065e53a2dcc77ed46e7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063543"
---
# <a name="saving-data"></a>Salvataggio di dati

Ogni istanza del contesto include un elemento `ChangeTracker` che è responsabile del rilevamento delle modifiche da scrivere nel database. Quando si apportano modifiche alle istanze delle classi di entità, queste modifiche vengono registrate in `ChangeTracker` e quindi scritte nel database quando si chiama `SaveChanges`. Il provider di database è responsabile della conversione delle modifiche in operazioni specifiche del database (ad esempio, i comandi `INSERT`, `UPDATE` e `DELETE` per un database relazionale).
