---
title: Panoramica di Entity Framework 6 - EF6
description: Panoramica di Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/index
ms.openlocfilehash: aff7aee51c4e12b278f7cab8b5122ee0edbbac0f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072122"
---
# <a name="entity-framework-6"></a>Entity Framework 6
Entity Framework 6 (EF6) è un mapper relazionale a oggetti comprovato e testato per .NET le cui funzionalità vengono sviluppate e stabilizzate da anni.

In quanto mapper relazionale a oggetti, EF6 riduce la mancata corrispondenza dell'impedenza tra i mondi relazionale e orientato agli oggetti, consentendo agli sviluppatori di scrivere applicazioni che interagiscono con i dati archiviati in database relazionali mediante oggetti .NET fortemente tipizzati che rappresentano il dominio dell'applicazione ed eliminando la necessità di gran parte del codice di accesso ai dati complesso che di solito devono scrivere.

EF6 implementa diverse funzionalità del mapping relazionale a oggetti molto comuni:
- Mapping delle classi di entità [POCO](xref:ef6/resources/glossary#poco) che non dipendono da alcun tipo di Entity Framework
- Rilevamento automatico delle modifiche
- Risoluzione di identità e unità di lavoro
- Caricamento eager, lazy ed esplicito
- Conversione di query fortemente tipizzate tramite [LINQ (Language Integrated Query)](https://aka.ms/AA6hsvu)
- Funzionalità di mapping complete che includono il supporto per:
  - Relazioni uno-a-uno, uno-a-molti e molti-a-molti
  - Ereditarietà (tabella per gerarchia, tabella per tipo e tabella per classe concreta)
  - Tipi complessi
  - Stored procedure
- Una finestra di progettazione per creare modelli di entità.
- Un'esperienza Code First per creare modelli di entità mediante la scrittura di codice.
- I modelli possono essere generati da database esistenti e poi modificati manualmente, oppure possono essere creati da zero e usati per generare nuovi database.
- Integrazione con i modelli di applicazione .NET Framework, tra cui ASP.NET e, tramite associazione dati, con WPF e WinForms.
- Connettività del database basata su ADO.NET e numerosi [provider](xref:ef6/fundamentals/providers/index) disponibili per la connessione a SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2 e così via.

## <a name="should-i-use-ef6-or-ef-core"></a>È meglio usare EF6 o EF Core?

EF Core è una versione più moderna, leggera ed estendibile di Entity Framework che offre molte funzionalità e vantaggi simili a quelli di EF6.
EF Core è una versione completamente riscritta e contiene molte nuove funzionalità non disponibili in EF6, anche se non include ancora alcune delle funzionalità di mapping più avanzate di EF6.
È consigliabile usare EF Core nelle nuove applicazioni, se il set di funzionalità soddisfa i propri requisiti.
[Confronto tra EF Core ed EF6](xref:efcore-and-ef6/index) esamina questa scelta in modo più dettagliato.

## <a name="get-started"></a>[Introduzione](xref:ef6/get-started)

Aggiungere al progetto il pacchetto NuGet EntityFramework o installare [Entity Framework Tools per Visual Studio](https://aka.ms/AA6i8c5). In seguito guardare i video e leggere le esercitazioni e la documentazione avanzata per sfruttare al meglio EF6.

## <a name="past-entity-framework-versions"></a>Versioni precedenti di Entity Framework

Questa è la documentazione per la versione più recente di Entity Framework 6, anche se la maggior parte di essa si applica anche alle versioni precedenti.
Vedere [Novità](xref:ef6/what-is-new/index) e [Versioni precedenti](xref:ef6/what-is-new/past-releases) per un elenco completo delle versioni di Entity Framework e delle funzionalità introdotte.
