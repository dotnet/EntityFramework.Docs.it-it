---
title: Gestione di schemi di database - EF Core
description: Panoramica delle strategie di gestione degli schemi di database con Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619499"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="79da1-103">Gestione di schemi di database</span><span class="sxs-lookup"><span data-stu-id="79da1-103">Managing Database Schemas</span></span>

<span data-ttu-id="79da1-104">EF Core offre due metodi principali per mantenere sincronizzati il modello di EF Core e lo schema di database. Per scegliere tra i due, decidere se il modello di EF Core o lo schema del database è l'origine di dati reali.</span><span class="sxs-lookup"><span data-stu-id="79da1-104">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="79da1-105">Se si sceglie il modello di EF Core come origine di dati reali, usare le [migrazioni][1].</span><span class="sxs-lookup"><span data-stu-id="79da1-105">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="79da1-106">Quando si apportano modifiche al modello di EF Core questo approccio consente di applicare in modo incrementale le modifiche corrispondenti dello schema al database, in modo che rimanga compatibile con il modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="79da1-106">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="79da1-107">Usare il [reverse engineering][2] se si vuole usare lo schema del database come origine di dati reali.</span><span class="sxs-lookup"><span data-stu-id="79da1-107">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="79da1-108">Questo approccio consente di eseguire lo scaffolding di un elemento DbContext e delle classi del tipo di entità decompilando lo schema del database in un modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="79da1-108">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="79da1-109">Anche le [API di creazione ed eliminazione][3] sono in grado di creare lo schema del database dal modello di EF Core.</span><span class="sxs-lookup"><span data-stu-id="79da1-109">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="79da1-110">Tuttavia, sono destinate principalmente ai test e ad altri scenari in cui l'eliminazione del database è accettabile.</span><span class="sxs-lookup"><span data-stu-id="79da1-110">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
