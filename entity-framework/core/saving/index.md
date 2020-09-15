---
title: Salvataggio dei dati - EF Core
description: Panoramica del salvataggio dei dati con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 98a23fd4956a8646e9760f9ecb0b48166ad6caa1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072473"
---
# <a name="saving-data"></a><span data-ttu-id="ac369-103">Salvataggio di dati</span><span class="sxs-lookup"><span data-stu-id="ac369-103">Saving Data</span></span>

<span data-ttu-id="ac369-104">Ogni istanza del contesto include un elemento `ChangeTracker` che è responsabile del rilevamento delle modifiche da scrivere nel database.</span><span class="sxs-lookup"><span data-stu-id="ac369-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="ac369-105">Quando si apportano modifiche alle istanze delle classi di entità, queste modifiche vengono registrate in `ChangeTracker` e quindi scritte nel database quando si chiama `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="ac369-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="ac369-106">Il provider di database è responsabile della conversione delle modifiche in operazioni specifiche del database (ad esempio, i comandi `INSERT`, `UPDATE` e `DELETE` per un database relazionale).</span><span class="sxs-lookup"><span data-stu-id="ac369-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
