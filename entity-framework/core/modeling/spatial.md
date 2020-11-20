---
title: Dati spaziali-EF Core
description: Utilizzo di dati spaziali in un modello di Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: a38e75e150b3d3404f2ed7619d05ba478c411117
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003445"
---
# <a name="spatial-data"></a>Dati spaziali

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 2,2.

I dati spaziali rappresentano la posizione fisica e la forma degli oggetti. Molti database forniscono supporto per questo tipo di dati, in modo che possa essere indicizzato e sottoposto a query insieme ad altri dati. Gli scenari comuni includono l'esecuzione di query per gli oggetti all'interno di una distanza specificata da una posizione o la selezione dell'oggetto il cui bordo contiene una determinata posizione. EF Core supporta il mapping ai tipi di dati spaziali mediante la libreria spaziale NetTopologySuite.

## <a name="installing"></a>Installazione

Per usare i dati spaziali con EF Core, è necessario installare il pacchetto NuGet di supporto appropriato. Il pacchetto che è necessario installare dipende dal provider in uso.

Provider di EF Core                        | Pacchetto NuGet spaziale
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
Pomelo.EntityFrameworkCore.MySql        | [Pomelo. EntityFrameworkCore. MySql. NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
Devart.Data.MySql.EFCore                | [Devart. Data. MySql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
Devart.Data.PostgreSql.EFCore           | [Devart. Data. PostgreSql. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
Devart.Data.SQLite.EFCore               | [Devart. Data. SQLite. EFCore. NetTopologySuite](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
Teradata.EntityFrameworkCore            | [Teradata. EntityFrameworkCore. NetTopologySuite](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a>NetTopologySuite

[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) è una libreria spaziale per .NET. EF Core Abilita il mapping ai tipi di dati spaziali nel database usando i tipi NTS nel modello.

Per abilitare il mapping ai tipi spaziali tramite NTS, chiamare il metodo UseNetTopologySuite nel generatore di opzioni DbContext del provider. Ad esempio, con SQL Server è possibile chiamarlo come questo.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

Esistono diversi tipi di dati spaziali. Il tipo da utilizzare dipende dai tipi di forme che si desidera consentire. Di seguito è illustrata la gerarchia di tipi NTS che è possibile usare per le proprietà nel modello. Si trovano nello `NetTopologySuite.Geometries` spazio dei nomi.

* Geometria
  * Point
  * LineString
  * Polygon
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString, CompoundCurve e CurePolygon non sono supportati da NTS.

L'utilizzo del tipo Geometry di base consente di specificare qualsiasi tipo di forma dalla proprietà.

## <a name="longitude-and-latitude"></a>Longitudine e latitudine

Le coordinate in NTS sono espresse in termini di valori X e Y. Per rappresentare la longitudine e la latitudine, usare X per longitudine e Y per la latitudine. Si noti che questa operazione è all' **indietro** rispetto al `latitude, longitude` formato in cui vengono in genere visualizzati questi valori.

## <a name="querying-data"></a>Esecuzione di query su dati

Per eseguire il mapping alle tabelle nel [database di esempio Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630), è possibile usare le classi di entità seguenti.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

In LINQ, i metodi e le proprietà NTS disponibili come funzioni di database verranno convertiti in SQL. Ad esempio, i metodi Distance e Contains vengono convertiti nelle query seguenti. Per informazioni sui metodi supportati, vedere la documentazione del provider.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a>Reverse Engineering

I pacchetti NuGet spaziali abilitano anche [Reverse Engineering](xref:core/managing-schemas/scaffolding) modelli con proprietà spaziali, ma è necessario installare il pacchetto **_prima_* di _ che esegue `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` . In caso contrario, si riceveranno avvisi relativi alla mancata individuazione dei mapping dei tipi per le colonne e le colonne verranno ignorate.

## <a name="srid-ignored-during-client-operations"></a>SRID ignorato durante le operazioni client

NTS ignora i valori SRID durante le operazioni. Si presuppone un sistema di coordinate planare. Ciò significa che se si specificano le coordinate in termini di longitudine e latitudine, alcuni valori valutati dal client, ad esempio distanza, lunghezza e area, saranno in gradi, non in metri. Per valori più significativi, è necessario innanzitutto proiettare le coordinate in un altro sistema di coordinate usando una libreria come [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) prima di calcolare questi valori.

Se un'operazione viene valutata dal server EF Core tramite SQL, l'unità risultante sarà determinata dal database.

Di seguito è riportato un esempio di utilizzo di ProjNet4GeoAPI per calcolare la distanza tra due città.

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> 4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.

## <a name="additional-resources"></a>Risorse aggiuntive

### <a name="database-specific-information"></a>Informazioni specifiche del database

Assicurarsi di leggere la documentazione del provider per informazioni aggiuntive sull'utilizzo dei dati spaziali.

_ [Dati spaziali nel provider di SQL Server](xref:core/providers/sql-server/spatial)
* [Dati spaziali nel provider SQLite](xref:core/providers/sqlite/spatial)
* [Dati spaziali nel provider npgsql](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a>Altre risorse

* [Documentazione di NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
* [EF Core sessione standup della community](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15), con particolare attenzione ai dati spaziali e a NetTopologySuite.
