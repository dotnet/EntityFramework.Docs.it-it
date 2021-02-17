---
title: EF Core pacchetti NuGet
description: Panoramica dei diversi pacchetti NuGet di Entity Framework Core
author: ajcvickers
ms.date: 01/21/2021
uid: core/what-is-new/nuget-packages
ms.openlocfilehash: 4b6e210f2324ea97e006d681d399bfdd6918d1b4
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544599"
---
# <a name="ef-core-nuget-packages"></a>EF Core pacchetti NuGet

Entity Framework Core (EF Core) viene fornito come pacchetto [NuGet](https://www.nuget.org/) . I pacchetti necessari per un'applicazione dipendono da:

- Tipo di sistema di database in uso (SQL Server, SQLite e così via)
- Funzionalità di EF Core necessarie

Il normale processo di installazione dei pacchetti è:

- Decidere un provider di database e installare il pacchetto appropriato ([vedere di seguito](#database-providers))
- Installare anche [Microsoft. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)e [Microsoft. EntityFrameworkCore. relazionale](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/) se si usa un provider relazionale. In questo modo si garantisce che vengano usate versioni coerenti e che NuGet informerà le informazioni quando vengono spedite nuove versioni del pacchetto.
- Facoltativamente, decidere quale tipo di strumenti è necessario e installare i pacchetti appropriati (vedere di seguito)

Per informazioni introduttive sui EF Core, vedere Esercitazione introduttiva [per Entity Framework Core](xref:core/get-started/overview/first-app) .

## <a name="package-versions"></a>Versioni del pacchetto

Assicurarsi di installare la stessa versione di tutti i pacchetti EF Core forniti da Microsoft. Se, ad esempio, è installata la versione 5.0.3 di Microsoft. EntityFrameworkCore. SqlServer, tutti gli altri pacchetti Microsoft. EntityFrameworkCore. * devono trovarsi anche in 5.0.3.

Assicurarsi inoltre che tutti i pacchetti esterni siano compatibili con la versione di EF Core in uso. In particolare, verificare che il provider di database esterno supporti la versione di EF Core in uso. Le nuove versioni principali di EF Core in genere richiedono un provider di database aggiornato.

> [!WARNING]
> NuGet non impone versioni coerenti del pacchetto. Controllare sempre con attenzione le versioni a cui si fa riferimento nel `csproj` file o equivalenti.

## <a name="database-providers"></a>Provider di database

EF Core supporta diversi sistemi di database tramite l'utilizzo di "provider di database". Ogni sistema dispone di un proprio provider di database, che viene fornito come pacchetto NuGet. Le applicazioni devono installare uno o più pacchetti del provider.

I provider di database comuni sono elencati nella tabella seguente. Per un elenco più completo dei provider disponibili, vedere [provider di database](xref:core/providers/index) .

| Sistema di database                   | Pacchetto
|:----------------------------------|----------------------
| SQL Server e SQL Azure          | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)
| SQLite                            | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)
| Azure Cosmos DB                   | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)
| PostgreSQL                        | [Npgsql. EntityFrameworkCore. PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)*
| MySQL                             | [Pomelo. EntityFrameworkCore. MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)*
| EF Core database in memoria * *      | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)

* Si tratta di provider open source diffusi e di alta qualità sviluppati e distribuiti dalla community. Gli altri provider elencati vengono forniti da Microsoft.

* * Valutare con attenzione se utilizzare il provider in memoria. Non è progettato per l'uso in ambiente di produzione e potrebbe non essere [la soluzione migliore per i test](xref:core/testing/index).

## <a name="tools"></a>Strumenti

L'uso di strumenti per [EF Core migrazioni](xref:core/managing-schemas/migrations/index) e [Reverse Engineering (impalcature) da un database esistente](xref:core/managing-schemas/scaffolding) richiede l'installazione del pacchetto di strumenti appropriato:

- [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) per gli strumenti di PowerShell che funziona nella console di [Gestione pacchetti](/nuget/consume-packages/install-use-packages-powershell) di Visual Studio
- [DotNet-EF](https://www.nuget.org/packages/dotnet-ef/) e [Microsoft. EntityFrameworkCore. Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design/) per gli strumenti da riga di comando multipiattaforma

Per ulteriori informazioni sull'utilizzo degli strumenti di EF Core, vedere informazioni di [riferimento sugli strumenti di Entity Framework Core](xref:core/cli/index) , tra cui come installare correttamente lo `dotnet-ef` strumento in un progetto o a livello globale.

> [!TIP]
> Per impostazione predefinita, il pacchetto Microsoft. EntityFrameworkCore. Design viene installato in modo tale che non verrà distribuito con l'applicazione. Ciò significa anche che i relativi tipi non possono essere usati in modo transitivo in altri progetti. Usare una normale `PackageReference` nel `.csproj` file o equivalente se è necessario accedere ai tipi nel pacchetto. Per ulteriori informazioni, vedere [Servizi in fase di progettazione](xref:core/cli/services) .

## <a name="extension-packages"></a>Pacchetti di estensione

Sono disponibili molte [estensioni per EF Core](xref:core/extensions/index) pubblicate sia da Microsoft che da terze parti come pacchetti NuGet. I pacchetti usati comunemente includono:

| Funzionalità                                | Pacchetto | Dipendenze aggiuntive
|:---------------------------------------------|---------|------------------------
| Proxy per il caricamento lazy e il rilevamento delle modifiche | [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) | [Castle. Core](https://www.nuget.org/packages/Castle.Core/)
| Supporto spaziale per SQL Server               | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) e [NetTopologySuite. io. SqlServerBytes](https://www.nuget.org/packages/NetTopologySuite.IO.SqlServerBytes/)
| Supporto spaziale per SQLite                   | [Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) e [NetTopologySuite. io. SpatiaLite](https://www.nuget.org/packages/NetTopologySuite.IO.SpatiaLite/)
| Supporto spaziale per PostgreSQL               | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) e [NetTopologySuite. io. PostGIS](https://www.nuget.org/packages/NetTopologySuite.IO.PostGIS/) (tramite [npgsql. NetTopologySuite](https://www.nuget.org/packages/Npgsql.NetTopologySuite/))
| Supporto spaziale per MySQL                    | [Pomelo. EntityFrameworkCore. MySql. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/)

## <a name="other-packages"></a>Altri pacchetti

Gli altri pacchetti EF Core vengono estratti come dipendenze del pacchetto del provider di database. Tuttavia, potrebbe essere necessario aggiungere riferimenti espliciti ai pacchetti per questi pacchetti in modo che NuGet fornisca notifiche quando vengono rilasciate nuove versioni.

| Funzionalità                                              | Pacchetto
|:-----------------------------------------------------------|--------
| EF Core funzionalità di base                                | [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)
| Funzionalità del database relazionale comune                   | [Microsoft. EntityFrameworkCore. relazionale](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/)
| Pacchetto leggero per gli attributi di EF Core e così via.           | [Microsoft. EntityFrameworkCore. abstracts](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)
| Analizzatori di codice Roslyn per l'utilizzo EF Core                    | [Microsoft. EntityFrameworkCore. Analyzers](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Analyzers/)
| EF Core provider SQLite senza una dipendenza SQLite nativa | [Microsoft. EntityFrameworkCore. sqlite. Core](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.Core/)

## <a name="packages-for-database-provider-testing"></a>Pacchetti per i test del provider di database

I pacchetti seguenti vengono usati per testare i provider di database incorporati in repository GitHub esterni. Per esempi, vedere [efcore.PG](https://github.com/npgsql/efcore.pg) e [pomelo. EntityFrameworkCore. MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) . Le applicazioni non devono installare questi pacchetti.

| Funzionalità                                              | Pacchetto
|:-----------------------------------------------------------|--------
| Test per qualsiasi provider di database                            | [Microsoft. EntityFrameworkCore. Specification. tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Specification.Tests/)
| Test per provider di database relazionali                    | [Microsoft. EntityFrameworkCore. relazional. Specification. tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational.Specification.Tests/)

## <a name="obsolete-packages"></a>Pacchetti obsoleti

**Non** installare i pacchetti obsoleti seguenti e rimuoverli se sono attualmente installati nei progetti:

- Microsoft. EntityFrameworkCore. relazional. Design
- Microsoft. EntityFrameworkCore. Tools. DotNet
- Microsoft. EntityFrameworkCore. SqlServer. Design
- Microsoft. EntityFrameworkCore. sqlite. Design
- Microsoft. EntityFrameworkCore. relazional. Design. Specification. tests
