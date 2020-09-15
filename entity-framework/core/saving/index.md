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
# <a name="saving-data"></a><span data-ttu-id="9ea42-103">Salvataggio di dati</span><span class="sxs-lookup"><span data-stu-id="9ea42-103">Saving Data</span></span>

<span data-ttu-id="9ea42-104">Ogni istanza del contesto include un elemento `ChangeTracker` che è responsabile del rilevamento delle modifiche da scrivere nel database.</span><span class="sxs-lookup"><span data-stu-id="9ea42-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="9ea42-105">Quando si apportano modifiche alle istanze delle classi di entità, queste modifiche vengono registrate in `ChangeTracker` e quindi scritte nel database quando si chiama `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="9ea42-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="9ea42-106">Il provider di database è responsabile della conversione delle modifiche in operazioni specifiche del database (ad esempio, i comandi `INSERT`, `UPDATE` e `DELETE` per un database relazionale).</span><span class="sxs-lookup"><span data-stu-id="9ea42-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
