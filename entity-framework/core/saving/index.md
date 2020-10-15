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
# <a name="saving-data"></a><span data-ttu-id="3ee1b-103">Salvataggio di dati</span><span class="sxs-lookup"><span data-stu-id="3ee1b-103">Saving Data</span></span>

<span data-ttu-id="3ee1b-104">Ogni istanza del contesto include un elemento `ChangeTracker` che è responsabile del rilevamento delle modifiche da scrivere nel database.</span><span class="sxs-lookup"><span data-stu-id="3ee1b-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="3ee1b-105">Quando si apportano modifiche alle istanze delle classi di entità, queste modifiche vengono registrate in `ChangeTracker` e quindi scritte nel database quando si chiama `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="3ee1b-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="3ee1b-106">Il provider di database è responsabile della conversione delle modifiche in operazioni specifiche del database (ad esempio, i comandi `INSERT`, `UPDATE` e `DELETE` per un database relazionale).</span><span class="sxs-lookup"><span data-stu-id="3ee1b-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
