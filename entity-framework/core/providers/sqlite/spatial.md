---
title: Provider di database SQLite-dati spaziali-EF Core
description: Uso dei dati spaziali con il provider di database Entity Framework Core SQLite
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066652"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>Dati spaziali nel provider di EF Core SQLite

Questa pagina include informazioni aggiuntive sull'utilizzo dei dati spaziali con il provider di database SQLite. Per informazioni generali sull'utilizzo dei dati spaziali in EF Core, vedere la documentazione principale sui [dati spaziali](xref:core/modeling/spatial) .

## <a name="installing-spatialite"></a>Installazione di SpatiaLite

In Windows la libreria mod_spatialite nativa viene distribuita come dipendenza del pacchetto NuGet. Altre piattaforme devono essere installate separatamente. Questa operazione viene in genere eseguita utilizzando una gestione pacchetti software. Ad esempio, è possibile utilizzare APT in Debian e Ubuntu; e homebrew in MacOS.

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Sfortunatamente, le versioni più recenti di PROJ (una dipendenza da SpatiaLite) non sono compatibili con il [bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predefinito di EF. È possibile ovviare a questo problema usando la libreria SQLite di sistema.

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

In **MacOS**sarà anche necessario impostare una variabile di ambiente prima di eseguire l'app in modo che usi la versione Homebrew di SQLite. In Visual Studio per Mac, è possibile impostare le opzioni di progetto **> progetto > esegui > configurazioni > impostazione predefinita**

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>Configurazione di SRID

In SpatiaLite le colonne devono specificare un identificatore SRID per colonna. L'identificatore SRID predefinito è `0` . Specificare un identificatore SRID diverso usando il metodo ForSqliteHasSrid.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> 4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.

## <a name="dimension"></a>Dimensione

La dimensione o le ordinate predefinite di una colonna sono X e Y. Per abilitare ordinamenti aggiuntivi come Z o M, configurare il tipo di colonna.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>Mapping di funzioni spaziali

Questa tabella mostra i membri [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) che vengono tradotti in funzioni SQL.

.NET                                        | SQL
------------------------------------------- | ---
Geometry. Area                               | Area ( @geometry )
Geometry. AsBinary ()                         | AsBinary ( @geometry )
Geometry. AsText ()                           | AsText ( @geometry )
Geometry. Confine                           | Limite ( @geometry )
Geometry. Buffer (distanza)                   | Buffer ( @geometry , @distance )
Geometry. Buffer (distance, quadrantSegments) | Buffer ( @geometry , @distance , @quadrantSegments )
Geometry. Centro                           | Baricentro ( @geometry )
Geometry. Contains (g)                        | Contains ( @geometry , @g )
Geometry. ConvexHull()                       | ConvexHull ( @geometry )
Geometry. CoveredBy (g)                       | CoveredBy ( @geometry , @g )
Geometry. Coperture (g)                          | Copre ( @geometry , @g )
Geometry. Crosses (g)                         | Crosses ( @geometry , @g )
Geometry. Differenza (altro)                  | Differenza ( @geometry , @other )
Geometry. Dimensione                          | Dimension ( @geometry )
Geometry. Non contiguo (g)                        | Non contiguo ( @geometry , @g )
Geometry. Distanza (g)                        | Distance ( @geometry , @g )
Geometry. Busta                           | Busta ( @geometry )
Geometry. EqualsTopologically (g)             | Uguale a ( @geometry , @g )
Geometry. GeometryType                       | GeometryType ( @geometry )
Geometry. Getgeometryn (n)                    | Geometryn ( @geometry , @n + 1)
Geometry. InteriorPoint                      | PointOnSurface ( @geometry )
Geometry. Intersezione (altro)                | Intersezione ( @geometry , @other )
Geometry. Interseca (g)                      | Interseca ( @geometry , @g )
Geometry. IsEmpty                            | IsEmpty ( @geometry )
Geometry. IsSimple                           | Semplice ( @geometry )
Geometry. IsValid                            | IsValid ( @geometry )
Geometry. IsWithinDistance (Geom, distance)   | Distance ( @geometry , @geom ) <= @distance
Geometry. Lunghezza                             | GLength ( @geometry )
Geometry. NumGeometries                      | NumGeometries ( @geometry )
Geometry. NumPoints                          | NumPoints ( @geometry )
Geometry. OgcGeometryType                    | CASE GeometryType ( @geometry ) quando ' Point ' then 1... FINE
Geometry. Sovrapposizioni (g)                        | Sovrapposizioni ( @geometry , @g )
Geometry. PointOnSurface                     | PointOnSurface ( @geometry )
Geometry. Correlazione (g, intersectionPattern)     | Correlazione ( @geometry , @g , @intersectionPattern )
Geometry. Inverti ()                          | ST_Reverse ( @geometry )
Geometry. SRID                               | SRID ( @geometry )
Geometry. SymmetricDifference (altro)         | SymDifference ( @geometry , @other )
Geometry. ToBinary ()                         | AsBinary ( @geometry )
Geometry. ToText ()                           | AsText ( @geometry )
Geometry. Tocchi (g)                         | Tocchi ( @geometry , @g )
Geometry. Union ()                            | UnaryUnion ( @geometry )
Geometry. Union (altro)                       | GUnion ( @geometry , @other )
Geometry. All'interno di (g)                          | All'interno @geometry di (, @g )
GeometryCollection [i]                       | Geometryn ( @geometryCollection , @i + 1)
GeometryCollection. Count                    | NumGeometries ( @geometryCollection )
lineString. Count                            | NumPoints ( @lineString )
lineString. EndPoint                         | EndPoint ( @lineString )
lineString. getpointn (n)                     | Pointn ( @lineString , @n + 1)
lineString. di chiusura                         | Chiuso ( @lineString )
lineString. Ring                           | Anello ( @lineString )
lineString. StartPoint                       | StartPoint ( @lineString )
MultiLineString. noclosed                    | Chiuso ( @multiLineString )
punto. M                                     | M ( @point )
punto. X                                     | X ( @point )
punto. Y                                     | Y ( @point )
punto. Z                                     | Z ( @point )
poligono. ExteriorRing                        | ExteriorRing ( @polygon )
poligono. GetInteriorRingN (n)                 | InteriorRingN ( @polygon , @n + 1)
poligono. NumInteriorRings                    | NumInteriorRing ( @polygon )

## <a name="additional-resources"></a>Risorse aggiuntive

* [Home page di SpatiaLite](https://www.gaia-gis.it/fossil/libspatialite)
* [Documentazione di NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
