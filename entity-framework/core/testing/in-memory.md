---
title: Test con il database EF In-Memory-EF Core
description: Uso del database in memoria EF per testare un'applicazione Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128810"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Test con il database EF In-Memory

> [!WARNING]
> Il database in memoria EF si comporta spesso in modo diverso rispetto ai database relazionali.
> Usare il database in memoria EF solo dopo aver compreso completamente i problemi e i compromessi necessari, come descritto in [test del codice che usa EF Core](xref:core/testing/index).

> [!TIP]
> SQLite è un provider relazionale e può anche usare i database in memoria.
> Si consiglia di usare questo metodo per eseguire il test per confrontare con maggiore precisione i comportamenti comuni del database relazionale.
> Questo problema viene trattato nell' [uso di SQLite per testare un'applicazione EF Core](xref:core/testing/sqlite).

Le informazioni contenute in questa pagina sono ora presenti in altre posizioni:

* Vedere [test del codice che usa EF Core](xref:core/testing/index) per informazioni generali sul test con il database in memoria EF.
* Vedere l' [esempio che illustra come testare le applicazioni che usano EF Core](xref:core/testing/testing-sample) per un esempio usando il database in memoria EF.
* Per informazioni generali sul database in memoria EF, vedere [il provider di database in memoria EF](xref:core/providers/in-memory/index) .
