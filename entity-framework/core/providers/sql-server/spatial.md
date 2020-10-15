---
title: Provider di database Microsoft SQL Server-dati spaziali-EF Core
description: Utilizzo dei dati spaziali con il provider di database Microsoft SQL Server Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sql-server/spatial
ms.openlocfilehash: 1356d2d86a497f01c4eacca777a8a260f33c0e9b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066661"
---
# <a name="spatial-data-in-the-sql-server-ef-core-provider"></a>Dati spaziali nel provider di EF Core SQL Server

Questa pagina include informazioni aggiuntive sull'utilizzo dei dati spaziali con il provider di database Microsoft SQL Server. Per informazioni generali sull'utilizzo dei dati spaziali in EF Core, vedere la documentazione principale sui [dati spaziali](xref:core/modeling/spatial) .

## <a name="geography-or-geometry"></a>Geografia o geometria

Per impostazione predefinita, viene eseguito il mapping delle proprietà spaziali alle `geography` colonne in SQL Server. Per utilizzare `geometry` , [configurare il tipo di colonna](xref:core/modeling/entity-properties#column-data-types) nel modello.

## <a name="geography-polygon-rings"></a>Anelli del poligono geografico

Quando si usa il `geography` tipo di colonna, SQL Server impone requisiti aggiuntivi sull'anello esterno (o sulla Shell) e sugli anelli interni (o buchi). L'anello esterno deve essere orientato in senso antiorario e gli anelli interni in senso orario. [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) convalida questa operazione prima di inviare valori al database.

## <a name="fullglobe"></a>FullGlobe

SQL Server dispone di un tipo geometry non standard per rappresentare l'intero globo quando si utilizza il `geography` tipo di colonna. Offre anche un modo per rappresentare i poligoni in base al globo completo (senza anello esterno). Nessuno di questi è supportato da NTS.

> [!WARNING]
> FullGlobe e i poligoni basati su di essi non sono supportati da NTS.

## <a name="curves"></a>Curve

Come indicato nella documentazione principale sui [dati spaziali](xref:core/modeling/spatial) , NTS non è attualmente in grado di rappresentare le curve. Ciò significa che è necessario trasformare i valori CircularString, CompoundCurve e CurePolygon usando il metodo [STCurveToLine](/sql/t-sql/spatial-geography/stcurvetoline-geography-data-type) prima di usarli in EF core.

> [!WARNING]
> CircularString, CompoundCurve e CurePolygon non sono supportati da NTS.

## <a name="spatial-function-mappings"></a>Mapping di funzioni spaziali

Questa tabella mostra i membri NTS che vengono tradotti in funzioni SQL. Si noti che le traduzioni variano a seconda che la colonna sia di tipo geography o Geometry.

.NET                                      | SQL (geografia)                                              | SQL (geometria)
----------------------------------------- | ------------------------------------------------------------ | --------------
Geometry. Area                             | @geometry.STArea()                                           | @geometry.STArea()
Geometry. AsBinary ()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
Geometry. AsText ()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
Geometry. Confine                         |                                                              | @geometry.STBoundary()
Geometry. Buffer (distanza)                 | @geometry.STBuffer(@distance)                                | @geometry.STBuffer(@distance)
Geometry. Centro                         |                                                              | @geometry.STCentroid()
Geometry. Contains (g)                      | @geometry.STContains(@g)                                     | @geometry.STContains(@g)
Geometry. ConvexHull()                     | @geometry.STConvexHull()                                     | @geometry.STConvexHull()
Geometry. Crosses (g)                       |                                                              | @geometry.STCrosses(@g)
Geometry. Differenza (altro)                | @geometry.STDifference(@other)                               | @geometry.STDifference(@other)
Geometry. Dimensione                        | @geometry.STDimension()                                      | @geometry.STDimension()
Geometry. Non contiguo (g)                      | @geometry.STDisjoint(@g)                                     | @geometry.STDisjoint(@g)
Geometry. Distanza (g)                      | @geometry.STDistance(@g)                                     | @geometry.STDistance(@g)
Geometry. Busta                         |                                                              | @geometry.STEnvelope()
Geometry. EqualsTopologically (g)           | @geometry.STEquals(@g)                                       | @geometry.STEquals(@g)
Geometry. GeometryType                     | @geometry.STGeometryType()                                   | @geometry.STGeometryType()
Geometry. Getgeometryn (n)                  | @geometry.STGeometryN( @n + 1)                                | @geometry.STGeometryN( @n + 1)
Geometry. InteriorPoint                    |                                                              | @geometry.STPointOnSurface()
Geometry. Intersezione (altro)              | @geometry.STIntersection(@other)                             | @geometry.STIntersection(@other)
Geometry. Interseca (g)                    | @geometry.STIntersects(@g)                                   | @geometry.STIntersects(@g)
Geometry. IsEmpty                          | @geometry.STIsEmpty()                                        | @geometry.STIsEmpty()
Geometry. IsSimple                         |                                                              | @geometry.STIsSimple()
Geometry. IsValid                          | @geometry.STIsValid()                                        | @geometry.STIsValid()
Geometry. IsWithinDistance (Geom, distance) | @geometry.STDistance( @geom ) <= @distance                     | @geometry.STDistance( @geom ) <= @distance
Geometry. Lunghezza                           | @geometry.STLength()                                         | @geometry.STLength()
Geometry. NumGeometries                    | @geometry.STNumGeometries()                                  | @geometry.STNumGeometries()
Geometry. NumPoints                        | @geometry.STNumPoints()                                      | @geometry.STNumPoints()
Geometry. OgcGeometryType                  | CASE @geometry.STGeometryType () quando N'Point ' then 1... FINE | CASE @geometry.STGeometryType () quando N'Point ' then 1... FINE
Geometry. Sovrapposizioni (g)                      | @geometry.STOverlaps(@g)                                     | @geometry.STOverlaps(@g)
Geometry. PointOnSurface                   |                                                              | @geometry.STPointOnSurface()
Geometry. Correlazione (g, intersectionPattern)   |                                                              | @geometry.STRelate(@g, @intersectionPattern)
Geometry. SRID                             | @geometry.STSrid                                             | @geometry.STSrid
Geometry. SymmetricDifference (altro)       | @geometry.STSymDifference(@other)                            | @geometry.STSymDifference(@other)
Geometry. ToBinary ()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
Geometry. ToText ()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
Geometry. Tocchi (g)                       |                                                              | @geometry.STTouches(@g)
Geometry. Union (altro)                     | @geometry.STUnion(@other)                                    | @geometry.STUnion(@other)
Geometry. All'interno di (g)                        | @geometry.STWithin(@g)                                       | @geometry.STWithin(@g)
GeometryCollection [i]                     | @geometryCollection.STGeometryN( @i + 1)                      | @geometryCollection.STGeometryN( @i + 1)
GeometryCollection. Count                  | @geometryCollection.STNumGeometries()                        | @geometryCollection.STNumGeometries()
lineString. Count                          | @lineString.STNumPoints()                                    | @lineString.STNumPoints()
lineString. EndPoint                       | @lineString.STEndPoint()                                     | @lineString.STEndPoint()
lineString. getpointn (n)                   | @lineString.STPointN( @n + 1)                                 | @lineString.STPointN( @n + 1)
lineString. di chiusura                       | @lineString.STIsClosed()                                     | @lineString.STIsClosed()
lineString. Ring                         |                                                              | @lineString.IsRing()
lineString. StartPoint                     | @lineString.STStartPoint()                                   | @lineString.STStartPoint()
MultiLineString. noclosed                  | @multiLineString.STIsClosed()                                | @multiLineString.STIsClosed()
punto. M                                   | @point.M                                                     | @point.M
punto. X                                   | @point.Long                                                  | @point.STX
punto. Y                                   | @point.Lat                                                   | @point.STY
punto. Z                                   | @point.Z                                                     | @point.Z
poligono. ExteriorRing                      | @polygon.RingN1                                            | @polygon.STExteriorRing()
poligono. GetInteriorRingN (n)               | @polygon.RingN( @n + 2)                                       | @polygon.STInteriorRingN( @n + 1)
poligono. NumInteriorRings                  | @polygon.NumRings()-1                                      | @polygon.STNumInteriorRing()

## <a name="additional-resources"></a>Risorse aggiuntive

* [Dati spaziali in SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
* [Documentazione di NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/)
