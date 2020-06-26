---
title: Dati spaziali-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 2222df84be7bfde3f252766bef1cfab39b476efa
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370448"
---
# <a name="spatial-data"></a>Dati spaziali

> [!NOTE]
> Questa funzionalità è stata aggiunta in EF Core 2,2.

I dati spaziali rappresentano la posizione fisica e la forma degli oggetti. Molti database forniscono supporto per questo tipo di dati, in modo che possa essere indicizzato e sottoposto a query insieme ad altri dati. Gli scenari comuni includono l'esecuzione di query per gli oggetti all'interno di una distanza specificata da una posizione o la selezione dell'oggetto il cui bordo contiene una determinata posizione. EF Core supporta il mapping ai tipi di dati spaziali mediante la libreria spaziale [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .

## <a name="installing"></a>Installazione

Per usare i dati spaziali con EF Core, è necessario installare il pacchetto NuGet di supporto appropriato. Il pacchetto che è necessario installare dipende dal provider in uso.

Provider di EF Core                        | Pacchetto NuGet spaziale
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>Reverse Engineering

I pacchetti NuGet spaziali abilitano anche [Reverse Engineering](../managing-schemas/scaffolding.md) modelli con proprietà spaziali, ma è necessario installare il pacchetto ***prima*** di eseguire `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` . In caso contrario, si riceveranno avvisi relativi alla mancata individuazione dei mapping dei tipi per le colonne e le colonne verranno ignorate.

## <a name="nettopologysuite-nts"></a>NetTopologySuite (NTS)

NetTopologySuite è una libreria spaziale per .NET. EF Core Abilita il mapping ai tipi di dati spaziali nel database usando i tipi NTS nel modello.

Per abilitare il mapping ai tipi spaziali tramite NTS, chiamare il metodo UseNetTopologySuite nel generatore di opzioni DbContext del provider. Ad esempio, con SQL Server è possibile chiamarlo come questo.

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

Esistono diversi tipi di dati spaziali. Il tipo da utilizzare dipende dai tipi di forme che si desidera consentire. Di seguito è illustrata la gerarchia di tipi NTS che è possibile usare per le proprietà nel modello. Si trovano nello `NetTopologySuite.Geometries` spazio dei nomi.

* Geometry
  * Point
  * LineString
  * Poligono
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> CircularString, CompoundCurve e CurePolygon non sono supportati da NTS.

L'utilizzo del tipo Geometry di base consente di specificare qualsiasi tipo di forma dalla proprietà.

Per eseguire il mapping alle tabelle nel [database di esempio Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630), è possibile usare le classi di entità seguenti.

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>Creazione di valori

È possibile utilizzare i costruttori per creare oggetti Geometry; Tuttavia, NTS consiglia di usare invece una factory Geometry. In questo modo è possibile specificare un SRID predefinito (il sistema di riferimento spaziale usato dalle coordinate) e il controllo su elementi più avanzati, ad esempio il modello di precisione (usato durante i calcoli) e la sequenza di coordinate (determina le ordinate, ovvero le dimensioni e le misure, sono disponibili).

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> 4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.

### <a name="longitude-and-latitude"></a>Longitudine e latitudine

Le coordinate in NTS sono espresse in termini di valori X e Y. Per rappresentare la longitudine e la latitudine, usare X per longitudine e Y per la latitudine. Si noti che questa operazione è all' **indietro** rispetto al `latitude, longitude` formato in cui vengono in genere visualizzati questi valori.

### <a name="srid-ignored-during-client-operations"></a>SRID ignorato durante le operazioni client

NTS ignora i valori SRID durante le operazioni. Si presuppone un sistema di coordinate planare. Ciò significa che se si specificano le coordinate in termini di longitudine e latitudine, alcuni valori valutati dal client, ad esempio distanza, lunghezza e area, saranno in gradi, non in metri. Per valori più significativi, è necessario innanzitutto proiettare le coordinate in un altro sistema di coordinate usando una libreria come [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) prima di calcolare questi valori.

Se un'operazione viene valutata dal server EF Core tramite SQL, l'unità risultante sarà determinata dal database.

Di seguito è riportato un esempio di utilizzo di ProjNet4GeoAPI per calcolare la distanza tra due città.

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>Esecuzione di query su dati

In LINQ, i metodi e le proprietà NTS disponibili come funzioni di database verranno convertiti in SQL. Ad esempio, i metodi Distance e Contains vengono convertiti nelle query seguenti. La tabella alla fine di questo articolo illustra i membri supportati da vari provider di EF Core.

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

Se si usa SQL Server, è necessario tenere presenti alcuni aspetti aggiuntivi.

### <a name="geography-or-geometry"></a>Geografia o geometria

Per impostazione predefinita, viene eseguito il mapping delle proprietà spaziali alle `geography` colonne in SQL Server. Per utilizzare `geometry` , [configurare il tipo di colonna](xref:core/modeling/entity-properties#column-data-types) nel modello.

### <a name="geography-polygon-rings"></a>Anelli del poligono geografico

Quando si usa il `geography` tipo di colonna, SQL Server impone requisiti aggiuntivi sull'anello esterno (o sulla Shell) e sugli anelli interni (o buchi). L'anello esterno deve essere orientato in senso antiorario e gli anelli interni in senso orario. NTS convalida questa operazione prima di inviare valori al database.

### <a name="fullglobe"></a>FullGlobe

SQL Server dispone di un tipo geometry non standard per rappresentare l'intero globo quando si utilizza il `geography` tipo di colonna. Offre anche un modo per rappresentare i poligoni in base al globo completo (senza anello esterno). Nessuno di questi è supportato da NTS.

> [!WARNING]
> FullGlobe e i poligoni basati su di essi non sono supportati da NTS.

## <a name="sqlite"></a>SQLite

Di seguito sono riportate alcune informazioni aggiuntive per quelle che usano SQLite.

### <a name="installing-spatialite"></a>Installazione di SpatiaLite

In Windows la libreria mod_spatialite nativa viene distribuita come dipendenza del pacchetto NuGet. Altre piattaforme devono essere installate separatamente. Questa operazione viene in genere eseguita utilizzando una gestione pacchetti software. Ad esempio, è possibile usare APT in Ubuntu e homebrew in MacOS.

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Sfortunatamente, le versioni più recenti di PROJ (una dipendenza da SpatiaLite) non sono compatibili con il [bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predefinito di EF. È possibile risolvere il problema creando un [provider SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personalizzato che usa la libreria SQLite di sistema oppure è possibile installare una build personalizzata di SpatiaLite disabilitando il supporto per proj.

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a>Configurazione di SRID

In SpatiaLite le colonne devono specificare un identificatore SRID per colonna. L'identificatore SRID predefinito è `0` . Specificare un identificatore SRID diverso usando il metodo ForSqliteHasSrid.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>Dimension

Analogamente a SRID, viene specificata anche una dimensione (o ordinata) di una colonna come parte della colonna. Le ordinate predefinite sono X e Y. abilitare le coordinate aggiuntive (Z e M) usando il metodo ForSqliteHasDimension.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>Operazioni convertite

Questa tabella mostra i membri NTS convertiti in SQL da ogni provider di EF Core.

NetTopologySuite | SQL Server (geometria) | SQL Server (geography) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry. area | ✔ | ✔ | ✔ | ✔
Geometry. AsBinary () | ✔ | ✔ | ✔ | ✔
Geometry. AsText () | ✔ | ✔ | ✔ | ✔
Geometry. Boundary | ✔ | | ✔ | ✔
Geometry. buffer (Double) | ✔ | ✔ | ✔ | ✔
Geometry. buffer (Double, int) | | | ✔ | ✔
Geometry. baricentro | ✔ | | ✔ | ✔
Geometry. Contains (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ConvexHull () | ✔ | ✔ | ✔ | ✔
Geometry. CoveredBy (Geometry) | | | ✔ | ✔
Geometry. Covers (geometria) | | | ✔ | ✔
Geometry. Crosses (geometria) | ✔ | | ✔ | ✔
Geometry. Difference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Dimension | ✔ | ✔ | ✔ | ✔
Geometry. unjoint (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. distance (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Envelope | ✔ | | ✔ | ✔
Geometry. EqualsExact (Geometry) | | | | ✔
Geometry. EqualsTopologically (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. GeometryType | ✔ | ✔ | ✔ | ✔
Geometry. getgeometryn (int) | ✔ | | ✔ | ✔
Geometry. InteriorPoint | ✔ | | ✔ | ✔
Geometry. intersezione (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Intersects (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry. Simple | ✔ | | ✔ | ✔
Geometry. IsValid | ✔ | ✔ | ✔ | ✔
Geometry. IsWithinDistance (Geometry, Double) | ✔ | | ✔ | ✔
Geometry. length | ✔ | ✔ | ✔ | ✔
Geometry. NumGeometries | ✔ | ✔ | ✔ | ✔
Geometry. NumPoints | ✔ | ✔ | ✔ | ✔
Geometry. OgcGeometryType | ✔ | ✔ | ✔ | ✔
Geometry. sovrapposizioni (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. PointOnSurface | ✔ | | ✔ | ✔
Geometry. correlate (Geometry, String) | ✔ | | ✔ | ✔
Geometry. Reverse () | | | ✔ | ✔
Geometry. SRID | ✔ | ✔ | ✔ | ✔
Geometry. SymmetricDifference (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. ToBinary () | ✔ | ✔ | ✔ | ✔
Geometry. ToText () | ✔ | ✔ | ✔ | ✔
Geometry. touchs (Geometry) | ✔ | | ✔ | ✔
Geometry. Union () | | | ✔ | ✔
Geometry. Union (Geometry) | ✔ | ✔ | ✔ | ✔
Geometry. Within (Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection. Count | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString. Count | ✔ | ✔ | ✔ | ✔
LineString. EndPoint | ✔ | ✔ | ✔ | ✔
LineString. getpointn (int) | ✔ | ✔ | ✔ | ✔
LineString. di chiusura | ✔ | ✔ | ✔ | ✔
LineString. Ring | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. noclosed | ✔ | ✔ | ✔ | ✔
Punto. M | ✔ | ✔ | ✔ | ✔
Punto. X | ✔ | ✔ | ✔ | ✔
Punto. Y | ✔ | ✔ | ✔ | ✔
Point. Z | ✔ | ✔ | ✔ | ✔
Poligono. ExteriorRing | ✔ | ✔ | ✔ | ✔
Poligono. GetInteriorRingN (int) | ✔ | ✔ | ✔ | ✔
Poligono. NumInteriorRings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>Risorse aggiuntive

* [Dati spaziali in SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Home page di SpatiaLite](https://www.gaia-gis.it/fossil/libspatialite)
* [Documentazione spaziale di npgsql](https://www.npgsql.org/efcore/mapping/nts.html)
* [Documentazione di PostGIS](https://postgis.net/documentation/)
