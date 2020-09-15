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
# <a name="managing-database-schemas"></a>Gestione di schemi di database

EF Core offre due metodi principali per mantenere sincronizzati il modello di EF Core e lo schema di database. Per scegliere tra i due, decidere se il modello di EF Core o lo schema del database è l'origine di dati reali.

Se si sceglie il modello di EF Core come origine di dati reali, usare le [migrazioni][1]. Quando si apportano modifiche al modello di EF Core questo approccio consente di applicare in modo incrementale le modifiche corrispondenti dello schema al database, in modo che rimanga compatibile con il modello di EF Core.

Usare il [reverse engineering][2] se si vuole usare lo schema del database come origine di dati reali. Questo approccio consente di eseguire lo scaffolding di un elemento DbContext e delle classi del tipo di entità decompilando lo schema del database in un modello di EF Core.

> [!NOTE]
> Anche le [API di creazione ed eliminazione][3] sono in grado di creare lo schema del database dal modello di EF Core. Tuttavia, sono destinate principalmente ai test e ad altri scenari in cui l'eliminazione del database è accettabile.

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
