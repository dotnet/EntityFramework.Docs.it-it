---
title: Introduzione a Entity Framework 6 - EF6
description: Introduzione a Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 66ce9113-81d2-480f-8c16-d00ec405b2f7
uid: ef6/get-started
ms.openlocfilehash: a03fa403bedc260def3f8110a028e972824cb756
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618200"
---
# <a name="get-started-with-entity-framework-6"></a>Introduzione a Entity Framework 6

Questa guida contiene una raccolta di collegamenti ad articoli della documentazione, procedure dettagliate e video selezionati per iniziare a usare rapidamente il prodotto.

## <a name="fundamentals"></a>Fundamentals

* [Ottenere Entity Framework](xref:ef6/fundamentals/install)

  Viene descritto come aggiungere Entity Framework alle applicazioni e, se si vuole usare EF Designer, assicurarsi che venga installato in Visual Studio.

* [Creazione di un modello: flussi di lavoro di Code First, EF Designer ed EF](xref:ef6/modeling/index)

  Si preferisce specificare il modello EF scrivendo codice o tracciando caselle e linee?
Si userà EF per mappare gli oggetti in un database esistente o si vuole che EF crei un database specifico per gli oggetti?
Di seguito vengono illustrati due diversi approcci per l'uso di EF6: EF designer e Code First.
Seguire la discussione e guardare il video che illustra le differenze.

* [Utilizzo di DbContext](xref:ef6/fundamentals/working-with-dbcontext)

  DbContext è il principale tipo di Entity Framework che è necessario imparare a usare. Svolge la funzione di launchpad delle query del database e tiene traccia delle modifiche apportate agli oggetti in modo che possano essere salvate in modo permanente nel database.

* [Invia una domanda](xref:ef6/resources/get-help)

  È possibile ricevere assistenza dagli esperti e contribuire con le proprie risposte alla community.

* [Contribuire](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 usa un modello di sviluppo aperto. Nel repository GitHub sono disponibili informazioni su come migliorare ulteriormente Entity Framework.

## <a name="code-first-resources"></a>Risorse per Code First

  - [Code First nel flusso di lavoro di un database esistente](xref:ef6/modeling/code-first/workflows/existing-database)
  - [Code First nel flusso di lavoro di un nuovo database](xref:ef6/modeling/code-first/workflows/new-database)
  - [Mapping delle enumerazioni usando Code First](xref:ef6/modeling/code-first/data-types/enums)
  - [Mapping dei tipi spaziali usando Code First](xref:ef6/modeling/code-first/data-types/spatial)
  - [Creazione di convenzioni Code First personalizzate](xref:ef6/modeling/code-first/conventions/custom)
  - [Uso della configurazione Fluent di Code First con Visual Basic](xref:ef6/modeling/code-first/fluent/vb)
  - [Migrazioni Code First](xref:ef6/modeling/code-first/migrations/index)
  - [Migrazioni Code First in ambienti team](xref:ef6/modeling/code-first/migrations/teams)
  - [Migrazioni Code First automatiche](xref:ef6/modeling/code-first/migrations/automatic) (non consigliato)

## <a name="ef-designer-resources"></a>Risorse per EF Designer
  - [Flusso di lavoro di Database First](xref:ef6/modeling/designer/workflows/database-first)
  - [Flusso di lavoro di Model First](xref:ef6/modeling/designer/workflows/model-first)
  - [Mapping delle enumerazioni](xref:ef6/modeling/designer/data-types/enums)
  - [Mapping dei tipi spaziali](xref:ef6/modeling/designer/data-types/spatial)
  - [Mapping dell'ereditarietà della tabella per gerarchia](xref:ef6/modeling/designer/inheritance/tph)
  - [Mapping dell'ereditarietà della tabella per tipo](xref:ef6/modeling/designer/inheritance/tpt)
  - [Mapping di stored procedure per gli aggiornamenti](xref:ef6/modeling/designer/stored-procedures/cud)
  - [Mapping di stored procedure per le query](xref:ef6/modeling/designer/stored-procedures/query)
  - [Suddivisione di entità](xref:ef6/modeling/designer/entity-splitting)
  - [Suddivisione di tabelle](xref:ef6/modeling/designer/table-splitting)
  - [Query di definizione](xref:ef6/modeling/designer/advanced/defining-query) (informazioni avanzate)
  - [Funzioni con valori di tabella](xref:ef6/modeling/designer/advanced/tvfs) (informazioni avanzate)

## <a name="other-resources"></a>Altre risorse
  - [Query e salvataggio asincroni](xref:ef6/fundamentals/async)
  - [Data binding con WinForms](xref:ef6/fundamentals/databinding/winforms)
  - [Data binding con WPF](xref:ef6/fundamentals/databinding/wpf)
  - [Scenari disconnessi con entità con rilevamento automatico](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (non consigliato)
