---
title: Salvataggio dei dati - EF Core
description: Panoramica del salvataggio dei dati con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: 13e7c470e71f9cf9a1112aad18a8902e2235cef1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616308"
---
# <a name="saving-data"></a>Salvataggio di dati

Ogni istanza del contesto include un elemento `ChangeTracker` che è responsabile del rilevamento delle modifiche da scrivere nel database. Quando si apportano modifiche alle istanze delle classi di entità, queste modifiche vengono registrate in `ChangeTracker` e quindi scritte nel database quando si chiama `SaveChanges`. Il provider di database è responsabile della conversione delle modifiche in operazioni specifiche del database (ad esempio, i comandi `INSERT`, `UPDATE` e `DELETE` per un database relazionale).
