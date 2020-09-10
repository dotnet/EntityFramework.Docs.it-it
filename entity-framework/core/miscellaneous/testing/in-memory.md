---
title: Test con il database in memoria EF-EF Core
description: Uso del database in memoria EF per testare un'applicazione Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619390"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Test con il database in memoria EF

> [!WARNING]
> Il database in memoria EF si comporta spesso in modo diverso rispetto ai database relazionali.
> Usare il database in memoria EF solo dopo aver compreso completamente i problemi e i compromessi necessari, come descritto in [test del codice che usa EF Core](xref:core/miscellaneous/testing/index).  

> [!TIP]
> SQLite è un provider relazionale e può anche usare i database in memoria.
> Si consiglia di usare questo metodo per eseguire il test per confrontare con maggiore precisione i comportamenti comuni del database relazionale.
> Questo problema viene trattato nell' [uso di SQLite per testare un'applicazione EF Core](xref:core/miscellaneous/testing/sqlite).   

Le informazioni contenute in questa pagina sono ora presenti in altre posizioni:
* Vedere [test del codice che usa EF Core](xref:core/miscellaneous/testing/index) per informazioni generali sul test con il database in memoria EF.
* Vedere l' [esempio che illustra come testare le applicazioni che usano EF Core](xref:core/miscellaneous/testing/testing-sample) per un esempio usando il database in memoria EF.
* Per informazioni generali sul database in memoria EF, vedere [il provider di database in memoria EF](xref:core/providers/in-memory/index) .
