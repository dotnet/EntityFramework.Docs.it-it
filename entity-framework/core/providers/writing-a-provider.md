---
title: Scrittura di un provider di database-EF Core
description: Informazioni sulla scrittura di un nuovo provider di Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: e66c5b94d826e35bb5148d57897a1081de4e9736
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128420"
---
# <a name="writing-a-database-provider"></a>Scrittura di un provider di database

Per informazioni sulla scrittura di un provider di database Entity Framework Core, vedere la pagina relativa alla scrittura di [un provider EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) da [Arthur Vickers](https://github.com/ajcvickers).

> [!NOTE]
> Questi post non sono stati aggiornati a partire da EF Core 1,1 e sono state apportate modifiche significative da quel momento.
Il [problema 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) sta monitorando gli aggiornamenti della documentazione.

Il EF Core codebase è open source e contiene diversi provider di database che possono essere utilizzati come riferimento. Il codice sorgente è reperibile in <https://github.com/dotnet/efcore> . Potrebbe anche essere utile esaminare il codice per i provider di terze parti di uso comune, ad esempio [npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)e [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact). In particolare, questi progetti sono configurati per l'estensione e l'esecuzione di test funzionali pubblicati in NuGet. Questo tipo di installazione è fortemente consigliato.

## <a name="keeping-up-to-date-with-provider-changes"></a>Mantenersi aggiornati sulle modifiche del provider

A partire da lavoro dopo la versione 2,1, è stato creato un [log di modifiche](xref:core/providers/provider-log) che potrebbero richiedere modifiche corrispondenti nel codice del provider. Questa operazione è utile quando si aggiorna un provider esistente per l'utilizzo con una nuova versione di EF Core.

Nelle versioni precedenti alla 2,1 sono state usate le [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) etichette e nei problemi di GitHub e le richieste pull per uno scopo simile. Si continiue di usare queste etichette nei problemi per indicare quali elementi di lavoro in una determinata versione potrebbero richiedere il lavoro da eseguire nei provider. Un' `providers-beware` etichetta indica in genere che l'implementazione di un elemento di lavoro potrebbe interrompere i provider, mentre un' `providers-fyi` etichetta in genere significa che i provider non verranno interrotti, ma potrebbe essere necessario modificare il codice comunque, ad esempio, per abilitare la nuova funzionalità.

## <a name="suggested-naming-of-third-party-providers"></a>Denominazione consigliata per i provider di terze parti

È consigliabile usare i nomi seguenti per i pacchetti NuGet. Questo è coerente con i nomi dei pacchetti forniti dal team EF Core.

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

Ad esempio:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
