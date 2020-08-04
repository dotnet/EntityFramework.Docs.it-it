---
title: Dati spaziali-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 85124b7e252797ccd952d0d332e7309eff97ba56
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526667"
---
# <a name="spatial-data"></a><span data-ttu-id="e7f2a-102">Dati spaziali</span><span class="sxs-lookup"><span data-stu-id="e7f2a-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="e7f2a-103">Questa funzionalità è stata aggiunta in EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="e7f2a-104">I dati spaziali rappresentano la posizione fisica e la forma degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="e7f2a-105">Molti database forniscono supporto per questo tipo di dati, in modo che possa essere indicizzato e sottoposto a query insieme ad altri dati.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="e7f2a-106">Gli scenari comuni includono l'esecuzione di query per gli oggetti all'interno di una distanza specificata da una posizione o la selezione dell'oggetto il cui bordo contiene una determinata posizione.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="e7f2a-107">EF Core supporta il mapping ai tipi di dati spaziali mediante la libreria spaziale [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="e7f2a-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="e7f2a-108">Installazione</span><span class="sxs-lookup"><span data-stu-id="e7f2a-108">Installing</span></span>

<span data-ttu-id="e7f2a-109">Per usare i dati spaziali con EF Core, è necessario installare il pacchetto NuGet di supporto appropriato.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="e7f2a-110">Il pacchetto che è necessario installare dipende dal provider in uso.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="e7f2a-111">Provider di EF Core</span><span class="sxs-lookup"><span data-stu-id="e7f2a-111">EF Core Provider</span></span>                        | <span data-ttu-id="e7f2a-112">Pacchetto NuGet spaziale</span><span class="sxs-lookup"><span data-stu-id="e7f2a-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="e7f2a-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="e7f2a-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="e7f2a-114">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="e7f2a-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="e7f2a-116">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="e7f2a-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="e7f2a-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="e7f2a-118">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="e7f2a-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="e7f2a-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="e7f2a-120">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="e7f2a-121">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="e7f2a-121">Reverse engineering</span></span>

<span data-ttu-id="e7f2a-122">I pacchetti NuGet spaziali abilitano anche [Reverse Engineering](../managing-schemas/scaffolding.md) modelli con proprietà spaziali, ma è necessario installare il pacchetto ***prima*** di eseguire `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="e7f2a-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="e7f2a-123">In caso contrario, si riceveranno avvisi relativi alla mancata individuazione dei mapping dei tipi per le colonne e le colonne verranno ignorate.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="e7f2a-124">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="e7f2a-125">NetTopologySuite è una libreria spaziale per .NET.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="e7f2a-126">EF Core Abilita il mapping ai tipi di dati spaziali nel database usando i tipi NTS nel modello.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="e7f2a-127">Per abilitare il mapping ai tipi spaziali tramite NTS, chiamare il metodo UseNetTopologySuite nel generatore di opzioni DbContext del provider.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="e7f2a-128">Ad esempio, con SQL Server è possibile chiamarlo come questo.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="e7f2a-129">Esistono diversi tipi di dati spaziali.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-129">There are several spatial data types.</span></span> <span data-ttu-id="e7f2a-130">Il tipo da utilizzare dipende dai tipi di forme che si desidera consentire.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="e7f2a-131">Di seguito è illustrata la gerarchia di tipi NTS che è possibile usare per le proprietà nel modello.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="e7f2a-132">Si trovano nello `NetTopologySuite.Geometries` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="e7f2a-133">Geometria</span><span class="sxs-lookup"><span data-stu-id="e7f2a-133">Geometry</span></span>
  * <span data-ttu-id="e7f2a-134">Point</span><span class="sxs-lookup"><span data-stu-id="e7f2a-134">Point</span></span>
  * <span data-ttu-id="e7f2a-135">LineString</span><span class="sxs-lookup"><span data-stu-id="e7f2a-135">LineString</span></span>
  * <span data-ttu-id="e7f2a-136">Poligono</span><span class="sxs-lookup"><span data-stu-id="e7f2a-136">Polygon</span></span>
  * <span data-ttu-id="e7f2a-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="e7f2a-137">GeometryCollection</span></span>
    * <span data-ttu-id="e7f2a-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="e7f2a-138">MultiPoint</span></span>
    * <span data-ttu-id="e7f2a-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="e7f2a-139">MultiLineString</span></span>
    * <span data-ttu-id="e7f2a-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="e7f2a-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="e7f2a-141">CircularString, CompoundCurve e CurePolygon non sono supportati da NTS.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="e7f2a-142">L'utilizzo del tipo Geometry di base consente di specificare qualsiasi tipo di forma dalla proprietà.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="e7f2a-143">Per eseguire il mapping alle tabelle nel [database di esempio Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630), è possibile usare le classi di entità seguenti.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="e7f2a-144">Creazione di valori</span><span class="sxs-lookup"><span data-stu-id="e7f2a-144">Creating values</span></span>

<span data-ttu-id="e7f2a-145">È possibile utilizzare i costruttori per creare oggetti Geometry; Tuttavia, NTS consiglia di usare invece una factory Geometry.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="e7f2a-146">In questo modo è possibile specificare un SRID predefinito (il sistema di riferimento spaziale usato dalle coordinate) e il controllo su elementi più avanzati, ad esempio il modello di precisione (usato durante i calcoli) e la sequenza di coordinate (determina le ordinate, ovvero le dimensioni e le misure, sono disponibili).</span><span class="sxs-lookup"><span data-stu-id="e7f2a-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="e7f2a-147">4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="e7f2a-148">Longitudine e latitudine</span><span class="sxs-lookup"><span data-stu-id="e7f2a-148">Longitude and Latitude</span></span>

<span data-ttu-id="e7f2a-149">Le coordinate in NTS sono espresse in termini di valori X e Y.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="e7f2a-150">Per rappresentare la longitudine e la latitudine, usare X per longitudine e Y per la latitudine.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="e7f2a-151">Si noti che questa operazione è all' **indietro** rispetto al `latitude, longitude` formato in cui vengono in genere visualizzati questi valori.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="e7f2a-152">SRID ignorato durante le operazioni client</span><span class="sxs-lookup"><span data-stu-id="e7f2a-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="e7f2a-153">NTS ignora i valori SRID durante le operazioni.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="e7f2a-154">Si presuppone un sistema di coordinate planare.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="e7f2a-155">Ciò significa che se si specificano le coordinate in termini di longitudine e latitudine, alcuni valori valutati dal client, ad esempio distanza, lunghezza e area, saranno in gradi, non in metri.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="e7f2a-156">Per valori più significativi, è necessario innanzitutto proiettare le coordinate in un altro sistema di coordinate usando una libreria come [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) prima di calcolare questi valori.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="e7f2a-157">Se un'operazione viene valutata dal server EF Core tramite SQL, l'unità risultante sarà determinata dal database.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="e7f2a-158">Di seguito è riportato un esempio di utilizzo di ProjNet4GeoAPI per calcolare la distanza tra due città.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="e7f2a-159">Esecuzione di query su dati</span><span class="sxs-lookup"><span data-stu-id="e7f2a-159">Querying Data</span></span>

<span data-ttu-id="e7f2a-160">In LINQ, i metodi e le proprietà NTS disponibili come funzioni di database verranno convertiti in SQL.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="e7f2a-161">Ad esempio, i metodi Distance e Contains vengono convertiti nelle query seguenti.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="e7f2a-162">La tabella alla fine di questo articolo illustra i membri supportati da vari provider di EF Core.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="e7f2a-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="e7f2a-163">SQL Server</span></span>

<span data-ttu-id="e7f2a-164">Se si usa SQL Server, è necessario tenere presenti alcuni aspetti aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="e7f2a-165">Geografia o geometria</span><span class="sxs-lookup"><span data-stu-id="e7f2a-165">Geography or geometry</span></span>

<span data-ttu-id="e7f2a-166">Per impostazione predefinita, viene eseguito il mapping delle proprietà spaziali alle `geography` colonne in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="e7f2a-167">Per utilizzare `geometry` , [configurare il tipo di colonna](xref:core/modeling/entity-properties#column-data-types) nel modello.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="e7f2a-168">Anelli del poligono geografico</span><span class="sxs-lookup"><span data-stu-id="e7f2a-168">Geography polygon rings</span></span>

<span data-ttu-id="e7f2a-169">Quando si usa il `geography` tipo di colonna, SQL Server impone requisiti aggiuntivi sull'anello esterno (o sulla Shell) e sugli anelli interni (o buchi).</span><span class="sxs-lookup"><span data-stu-id="e7f2a-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="e7f2a-170">L'anello esterno deve essere orientato in senso antiorario e gli anelli interni in senso orario.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="e7f2a-171">NTS convalida questa operazione prima di inviare valori al database.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="e7f2a-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="e7f2a-172">FullGlobe</span></span>

<span data-ttu-id="e7f2a-173">SQL Server dispone di un tipo geometry non standard per rappresentare l'intero globo quando si utilizza il `geography` tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="e7f2a-174">Offre anche un modo per rappresentare i poligoni in base al globo completo (senza anello esterno).</span><span class="sxs-lookup"><span data-stu-id="e7f2a-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="e7f2a-175">Nessuno di questi è supportato da NTS.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="e7f2a-176">FullGlobe e i poligoni basati su di essi non sono supportati da NTS.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="e7f2a-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-177">SQLite</span></span>

<span data-ttu-id="e7f2a-178">Di seguito sono riportate alcune informazioni aggiuntive per quelle che usano SQLite.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="e7f2a-179">Installazione di SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-179">Installing SpatiaLite</span></span>

<span data-ttu-id="e7f2a-180">In Windows la libreria mod_spatialite nativa viene distribuita come dipendenza del pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="e7f2a-181">Altre piattaforme devono essere installate separatamente.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="e7f2a-182">Questa operazione viene in genere eseguita utilizzando una gestione pacchetti software.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="e7f2a-183">Ad esempio, è possibile usare APT in Ubuntu e homebrew in MacOS.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="e7f2a-184">Sfortunatamente, le versioni più recenti di PROJ (una dipendenza da SpatiaLite) non sono compatibili con il [bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predefinito di EF.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="e7f2a-185">È possibile risolvere il problema creando un [provider SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personalizzato che usa la libreria SQLite di sistema oppure è possibile installare una build personalizzata di SpatiaLite disabilitando il supporto per proj.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="e7f2a-186">Configurazione di SRID</span><span class="sxs-lookup"><span data-stu-id="e7f2a-186">Configuring SRID</span></span>

<span data-ttu-id="e7f2a-187">In SpatiaLite le colonne devono specificare un identificatore SRID per colonna.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="e7f2a-188">L'identificatore SRID predefinito è `0` .</span><span class="sxs-lookup"><span data-stu-id="e7f2a-188">The default SRID is `0`.</span></span> <span data-ttu-id="e7f2a-189">Specificare un identificatore SRID diverso usando il metodo ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="e7f2a-190">Dimension</span><span class="sxs-lookup"><span data-stu-id="e7f2a-190">Dimension</span></span>

<span data-ttu-id="e7f2a-191">Analogamente a SRID, viene specificata anche una dimensione (o ordinata) di una colonna come parte della colonna.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="e7f2a-192">Le ordinate predefinite sono X e Y. abilitare le coordinate aggiuntive (Z e M) usando il metodo ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="e7f2a-193">Operazioni convertite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-193">Translated Operations</span></span>

<span data-ttu-id="e7f2a-194">Questa tabella mostra i membri NTS convertiti in SQL da ogni provider di EF Core.</span><span class="sxs-lookup"><span data-stu-id="e7f2a-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="e7f2a-195">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-195">NetTopologySuite</span></span> | <span data-ttu-id="e7f2a-196">SQL Server (geometria)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-196">SQL Server (geometry)</span></span> | <span data-ttu-id="e7f2a-197">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-197">SQL Server (geography)</span></span> | <span data-ttu-id="e7f2a-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-198">SQLite</span></span> | <span data-ttu-id="e7f2a-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="e7f2a-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="e7f2a-200">Geometry. area</span><span class="sxs-lookup"><span data-stu-id="e7f2a-200">Geometry.Area</span></span> | <span data-ttu-id="e7f2a-201">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-201">✔</span></span> | <span data-ttu-id="e7f2a-202">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-202">✔</span></span> | <span data-ttu-id="e7f2a-203">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-203">✔</span></span> | <span data-ttu-id="e7f2a-204">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-204">✔</span></span>
<span data-ttu-id="e7f2a-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="e7f2a-206">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-206">✔</span></span> | <span data-ttu-id="e7f2a-207">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-207">✔</span></span> | <span data-ttu-id="e7f2a-208">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-208">✔</span></span> | <span data-ttu-id="e7f2a-209">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-209">✔</span></span>
<span data-ttu-id="e7f2a-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-210">Geometry.AsText()</span></span> | <span data-ttu-id="e7f2a-211">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-211">✔</span></span> | <span data-ttu-id="e7f2a-212">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-212">✔</span></span> | <span data-ttu-id="e7f2a-213">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-213">✔</span></span> | <span data-ttu-id="e7f2a-214">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-214">✔</span></span>
<span data-ttu-id="e7f2a-215">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="e7f2a-215">Geometry.Boundary</span></span> | <span data-ttu-id="e7f2a-216">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-216">✔</span></span> | | <span data-ttu-id="e7f2a-217">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-217">✔</span></span> | <span data-ttu-id="e7f2a-218">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-218">✔</span></span>
<span data-ttu-id="e7f2a-219">Geometry. buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="e7f2a-220">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-220">✔</span></span> | <span data-ttu-id="e7f2a-221">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-221">✔</span></span> | <span data-ttu-id="e7f2a-222">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-222">✔</span></span> | <span data-ttu-id="e7f2a-223">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-223">✔</span></span>
<span data-ttu-id="e7f2a-224">Geometry. buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="e7f2a-225">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-225">✔</span></span> | <span data-ttu-id="e7f2a-226">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-226">✔</span></span>
<span data-ttu-id="e7f2a-227">Geometry. baricentro</span><span class="sxs-lookup"><span data-stu-id="e7f2a-227">Geometry.Centroid</span></span> | <span data-ttu-id="e7f2a-228">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-228">✔</span></span> | | <span data-ttu-id="e7f2a-229">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-229">✔</span></span> | <span data-ttu-id="e7f2a-230">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-230">✔</span></span>
<span data-ttu-id="e7f2a-231">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="e7f2a-232">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-232">✔</span></span> | <span data-ttu-id="e7f2a-233">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-233">✔</span></span> | <span data-ttu-id="e7f2a-234">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-234">✔</span></span> | <span data-ttu-id="e7f2a-235">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-235">✔</span></span>
<span data-ttu-id="e7f2a-236">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="e7f2a-237">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-237">✔</span></span> | <span data-ttu-id="e7f2a-238">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-238">✔</span></span> | <span data-ttu-id="e7f2a-239">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-239">✔</span></span> | <span data-ttu-id="e7f2a-240">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-240">✔</span></span>
<span data-ttu-id="e7f2a-241">Geometry. CoveredBy (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="e7f2a-242">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-242">✔</span></span> | <span data-ttu-id="e7f2a-243">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-243">✔</span></span>
<span data-ttu-id="e7f2a-244">Geometry. Covers (geometria)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="e7f2a-245">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-245">✔</span></span> | <span data-ttu-id="e7f2a-246">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-246">✔</span></span>
<span data-ttu-id="e7f2a-247">Geometry. Crosses (geometria)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="e7f2a-248">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-248">✔</span></span> | | <span data-ttu-id="e7f2a-249">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-249">✔</span></span> | <span data-ttu-id="e7f2a-250">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-250">✔</span></span>
<span data-ttu-id="e7f2a-251">Geometry. Difference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="e7f2a-252">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-252">✔</span></span> | <span data-ttu-id="e7f2a-253">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-253">✔</span></span> | <span data-ttu-id="e7f2a-254">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-254">✔</span></span> | <span data-ttu-id="e7f2a-255">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-255">✔</span></span>
<span data-ttu-id="e7f2a-256">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="e7f2a-256">Geometry.Dimension</span></span> | <span data-ttu-id="e7f2a-257">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-257">✔</span></span> | <span data-ttu-id="e7f2a-258">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-258">✔</span></span> | <span data-ttu-id="e7f2a-259">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-259">✔</span></span> | <span data-ttu-id="e7f2a-260">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-260">✔</span></span>
<span data-ttu-id="e7f2a-261">Geometry. unjoint (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="e7f2a-262">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-262">✔</span></span> | <span data-ttu-id="e7f2a-263">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-263">✔</span></span> | <span data-ttu-id="e7f2a-264">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-264">✔</span></span> | <span data-ttu-id="e7f2a-265">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-265">✔</span></span>
<span data-ttu-id="e7f2a-266">Geometry. distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="e7f2a-267">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-267">✔</span></span> | <span data-ttu-id="e7f2a-268">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-268">✔</span></span> | <span data-ttu-id="e7f2a-269">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-269">✔</span></span> | <span data-ttu-id="e7f2a-270">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-270">✔</span></span>
<span data-ttu-id="e7f2a-271">Geometry. Envelope</span><span class="sxs-lookup"><span data-stu-id="e7f2a-271">Geometry.Envelope</span></span> | <span data-ttu-id="e7f2a-272">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-272">✔</span></span> | | <span data-ttu-id="e7f2a-273">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-273">✔</span></span> | <span data-ttu-id="e7f2a-274">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-274">✔</span></span>
<span data-ttu-id="e7f2a-275">Geometry. EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="e7f2a-276">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-276">✔</span></span>
<span data-ttu-id="e7f2a-277">Geometry. EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="e7f2a-278">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-278">✔</span></span> | <span data-ttu-id="e7f2a-279">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-279">✔</span></span> | <span data-ttu-id="e7f2a-280">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-280">✔</span></span> | <span data-ttu-id="e7f2a-281">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-281">✔</span></span>
<span data-ttu-id="e7f2a-282">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="e7f2a-282">Geometry.GeometryType</span></span> | <span data-ttu-id="e7f2a-283">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-283">✔</span></span> | <span data-ttu-id="e7f2a-284">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-284">✔</span></span> | <span data-ttu-id="e7f2a-285">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-285">✔</span></span> | <span data-ttu-id="e7f2a-286">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-286">✔</span></span>
<span data-ttu-id="e7f2a-287">Geometry. getgeometryn (int)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="e7f2a-288">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-288">✔</span></span> | | <span data-ttu-id="e7f2a-289">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-289">✔</span></span> | <span data-ttu-id="e7f2a-290">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-290">✔</span></span>
<span data-ttu-id="e7f2a-291">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="e7f2a-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="e7f2a-292">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-292">✔</span></span> | | <span data-ttu-id="e7f2a-293">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-293">✔</span></span> | <span data-ttu-id="e7f2a-294">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-294">✔</span></span>
<span data-ttu-id="e7f2a-295">Geometry. intersezione (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="e7f2a-296">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-296">✔</span></span> | <span data-ttu-id="e7f2a-297">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-297">✔</span></span> | <span data-ttu-id="e7f2a-298">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-298">✔</span></span> | <span data-ttu-id="e7f2a-299">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-299">✔</span></span>
<span data-ttu-id="e7f2a-300">Geometry. Intersects (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="e7f2a-301">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-301">✔</span></span> | <span data-ttu-id="e7f2a-302">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-302">✔</span></span> | <span data-ttu-id="e7f2a-303">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-303">✔</span></span> | <span data-ttu-id="e7f2a-304">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-304">✔</span></span>
<span data-ttu-id="e7f2a-305">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="e7f2a-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="e7f2a-306">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-306">✔</span></span> | <span data-ttu-id="e7f2a-307">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-307">✔</span></span> | <span data-ttu-id="e7f2a-308">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-308">✔</span></span> | <span data-ttu-id="e7f2a-309">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-309">✔</span></span>
<span data-ttu-id="e7f2a-310">Geometry. Simple</span><span class="sxs-lookup"><span data-stu-id="e7f2a-310">Geometry.IsSimple</span></span> | <span data-ttu-id="e7f2a-311">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-311">✔</span></span> | | <span data-ttu-id="e7f2a-312">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-312">✔</span></span> | <span data-ttu-id="e7f2a-313">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-313">✔</span></span>
<span data-ttu-id="e7f2a-314">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="e7f2a-314">Geometry.IsValid</span></span> | <span data-ttu-id="e7f2a-315">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-315">✔</span></span> | <span data-ttu-id="e7f2a-316">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-316">✔</span></span> | <span data-ttu-id="e7f2a-317">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-317">✔</span></span> | <span data-ttu-id="e7f2a-318">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-318">✔</span></span>
<span data-ttu-id="e7f2a-319">Geometry. IsWithinDistance (Geometry, Double)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="e7f2a-320">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-320">✔</span></span> | | <span data-ttu-id="e7f2a-321">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-321">✔</span></span> | <span data-ttu-id="e7f2a-322">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-322">✔</span></span>
<span data-ttu-id="e7f2a-323">Geometry. length</span><span class="sxs-lookup"><span data-stu-id="e7f2a-323">Geometry.Length</span></span> | <span data-ttu-id="e7f2a-324">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-324">✔</span></span> | <span data-ttu-id="e7f2a-325">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-325">✔</span></span> | <span data-ttu-id="e7f2a-326">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-326">✔</span></span> | <span data-ttu-id="e7f2a-327">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-327">✔</span></span>
<span data-ttu-id="e7f2a-328">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="e7f2a-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="e7f2a-329">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-329">✔</span></span> | <span data-ttu-id="e7f2a-330">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-330">✔</span></span> | <span data-ttu-id="e7f2a-331">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-331">✔</span></span> | <span data-ttu-id="e7f2a-332">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-332">✔</span></span>
<span data-ttu-id="e7f2a-333">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="e7f2a-333">Geometry.NumPoints</span></span> | <span data-ttu-id="e7f2a-334">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-334">✔</span></span> | <span data-ttu-id="e7f2a-335">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-335">✔</span></span> | <span data-ttu-id="e7f2a-336">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-336">✔</span></span> | <span data-ttu-id="e7f2a-337">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-337">✔</span></span>
<span data-ttu-id="e7f2a-338">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="e7f2a-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="e7f2a-339">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-339">✔</span></span> | <span data-ttu-id="e7f2a-340">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-340">✔</span></span> | <span data-ttu-id="e7f2a-341">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-341">✔</span></span> | <span data-ttu-id="e7f2a-342">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-342">✔</span></span>
<span data-ttu-id="e7f2a-343">Geometry. sovrapposizioni (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="e7f2a-344">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-344">✔</span></span> | <span data-ttu-id="e7f2a-345">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-345">✔</span></span> | <span data-ttu-id="e7f2a-346">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-346">✔</span></span> | <span data-ttu-id="e7f2a-347">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-347">✔</span></span>
<span data-ttu-id="e7f2a-348">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="e7f2a-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="e7f2a-349">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-349">✔</span></span> | | <span data-ttu-id="e7f2a-350">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-350">✔</span></span> | <span data-ttu-id="e7f2a-351">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-351">✔</span></span>
<span data-ttu-id="e7f2a-352">Geometry. correlate (Geometry, String)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="e7f2a-353">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-353">✔</span></span> | | <span data-ttu-id="e7f2a-354">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-354">✔</span></span> | <span data-ttu-id="e7f2a-355">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-355">✔</span></span>
<span data-ttu-id="e7f2a-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="e7f2a-357">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-357">✔</span></span> | <span data-ttu-id="e7f2a-358">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-358">✔</span></span>
<span data-ttu-id="e7f2a-359">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="e7f2a-359">Geometry.SRID</span></span> | <span data-ttu-id="e7f2a-360">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-360">✔</span></span> | <span data-ttu-id="e7f2a-361">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-361">✔</span></span> | <span data-ttu-id="e7f2a-362">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-362">✔</span></span> | <span data-ttu-id="e7f2a-363">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-363">✔</span></span>
<span data-ttu-id="e7f2a-364">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="e7f2a-365">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-365">✔</span></span> | <span data-ttu-id="e7f2a-366">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-366">✔</span></span> | <span data-ttu-id="e7f2a-367">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-367">✔</span></span> | <span data-ttu-id="e7f2a-368">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-368">✔</span></span>
<span data-ttu-id="e7f2a-369">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="e7f2a-370">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-370">✔</span></span> | <span data-ttu-id="e7f2a-371">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-371">✔</span></span> | <span data-ttu-id="e7f2a-372">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-372">✔</span></span> | <span data-ttu-id="e7f2a-373">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-373">✔</span></span>
<span data-ttu-id="e7f2a-374">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-374">Geometry.ToText()</span></span> | <span data-ttu-id="e7f2a-375">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-375">✔</span></span> | <span data-ttu-id="e7f2a-376">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-376">✔</span></span> | <span data-ttu-id="e7f2a-377">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-377">✔</span></span> | <span data-ttu-id="e7f2a-378">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-378">✔</span></span>
<span data-ttu-id="e7f2a-379">Geometry. touchs (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="e7f2a-380">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-380">✔</span></span> | | <span data-ttu-id="e7f2a-381">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-381">✔</span></span> | <span data-ttu-id="e7f2a-382">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-382">✔</span></span>
<span data-ttu-id="e7f2a-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="e7f2a-383">Geometry.Union()</span></span> | | | <span data-ttu-id="e7f2a-384">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-384">✔</span></span> | <span data-ttu-id="e7f2a-385">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-385">✔</span></span>
<span data-ttu-id="e7f2a-386">Geometry. Union (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="e7f2a-387">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-387">✔</span></span> | <span data-ttu-id="e7f2a-388">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-388">✔</span></span> | <span data-ttu-id="e7f2a-389">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-389">✔</span></span> | <span data-ttu-id="e7f2a-390">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-390">✔</span></span>
<span data-ttu-id="e7f2a-391">Geometry. Within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="e7f2a-392">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-392">✔</span></span> | <span data-ttu-id="e7f2a-393">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-393">✔</span></span> | <span data-ttu-id="e7f2a-394">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-394">✔</span></span> | <span data-ttu-id="e7f2a-395">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-395">✔</span></span>
<span data-ttu-id="e7f2a-396">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="e7f2a-396">GeometryCollection.Count</span></span> | <span data-ttu-id="e7f2a-397">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-397">✔</span></span> | <span data-ttu-id="e7f2a-398">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-398">✔</span></span> | <span data-ttu-id="e7f2a-399">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-399">✔</span></span> | <span data-ttu-id="e7f2a-400">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-400">✔</span></span>
<span data-ttu-id="e7f2a-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="e7f2a-401">GeometryCollection[int]</span></span> | <span data-ttu-id="e7f2a-402">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-402">✔</span></span> | <span data-ttu-id="e7f2a-403">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-403">✔</span></span> | <span data-ttu-id="e7f2a-404">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-404">✔</span></span> | <span data-ttu-id="e7f2a-405">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-405">✔</span></span>
<span data-ttu-id="e7f2a-406">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="e7f2a-406">LineString.Count</span></span> | <span data-ttu-id="e7f2a-407">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-407">✔</span></span> | <span data-ttu-id="e7f2a-408">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-408">✔</span></span> | <span data-ttu-id="e7f2a-409">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-409">✔</span></span> | <span data-ttu-id="e7f2a-410">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-410">✔</span></span>
<span data-ttu-id="e7f2a-411">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="e7f2a-411">LineString.EndPoint</span></span> | <span data-ttu-id="e7f2a-412">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-412">✔</span></span> | <span data-ttu-id="e7f2a-413">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-413">✔</span></span> | <span data-ttu-id="e7f2a-414">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-414">✔</span></span> | <span data-ttu-id="e7f2a-415">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-415">✔</span></span>
<span data-ttu-id="e7f2a-416">LineString. getpointn (int)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="e7f2a-417">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-417">✔</span></span> | <span data-ttu-id="e7f2a-418">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-418">✔</span></span> | <span data-ttu-id="e7f2a-419">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-419">✔</span></span> | <span data-ttu-id="e7f2a-420">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-420">✔</span></span>
<span data-ttu-id="e7f2a-421">LineString. di chiusura</span><span class="sxs-lookup"><span data-stu-id="e7f2a-421">LineString.IsClosed</span></span> | <span data-ttu-id="e7f2a-422">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-422">✔</span></span> | <span data-ttu-id="e7f2a-423">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-423">✔</span></span> | <span data-ttu-id="e7f2a-424">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-424">✔</span></span> | <span data-ttu-id="e7f2a-425">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-425">✔</span></span>
<span data-ttu-id="e7f2a-426">LineString. Ring</span><span class="sxs-lookup"><span data-stu-id="e7f2a-426">LineString.IsRing</span></span> | <span data-ttu-id="e7f2a-427">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-427">✔</span></span> | | <span data-ttu-id="e7f2a-428">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-428">✔</span></span> | <span data-ttu-id="e7f2a-429">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-429">✔</span></span>
<span data-ttu-id="e7f2a-430">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="e7f2a-430">LineString.StartPoint</span></span> | <span data-ttu-id="e7f2a-431">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-431">✔</span></span> | <span data-ttu-id="e7f2a-432">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-432">✔</span></span> | <span data-ttu-id="e7f2a-433">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-433">✔</span></span> | <span data-ttu-id="e7f2a-434">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-434">✔</span></span>
<span data-ttu-id="e7f2a-435">MultiLineString. noclosed</span><span class="sxs-lookup"><span data-stu-id="e7f2a-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="e7f2a-436">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-436">✔</span></span> | <span data-ttu-id="e7f2a-437">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-437">✔</span></span> | <span data-ttu-id="e7f2a-438">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-438">✔</span></span> | <span data-ttu-id="e7f2a-439">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-439">✔</span></span>
<span data-ttu-id="e7f2a-440">Punto. M</span><span class="sxs-lookup"><span data-stu-id="e7f2a-440">Point.M</span></span> | <span data-ttu-id="e7f2a-441">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-441">✔</span></span> | <span data-ttu-id="e7f2a-442">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-442">✔</span></span> | <span data-ttu-id="e7f2a-443">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-443">✔</span></span> | <span data-ttu-id="e7f2a-444">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-444">✔</span></span>
<span data-ttu-id="e7f2a-445">Punto. X</span><span class="sxs-lookup"><span data-stu-id="e7f2a-445">Point.X</span></span> | <span data-ttu-id="e7f2a-446">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-446">✔</span></span> | <span data-ttu-id="e7f2a-447">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-447">✔</span></span> | <span data-ttu-id="e7f2a-448">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-448">✔</span></span> | <span data-ttu-id="e7f2a-449">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-449">✔</span></span>
<span data-ttu-id="e7f2a-450">Punto. Y</span><span class="sxs-lookup"><span data-stu-id="e7f2a-450">Point.Y</span></span> | <span data-ttu-id="e7f2a-451">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-451">✔</span></span> | <span data-ttu-id="e7f2a-452">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-452">✔</span></span> | <span data-ttu-id="e7f2a-453">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-453">✔</span></span> | <span data-ttu-id="e7f2a-454">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-454">✔</span></span>
<span data-ttu-id="e7f2a-455">Point. Z</span><span class="sxs-lookup"><span data-stu-id="e7f2a-455">Point.Z</span></span> | <span data-ttu-id="e7f2a-456">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-456">✔</span></span> | <span data-ttu-id="e7f2a-457">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-457">✔</span></span> | <span data-ttu-id="e7f2a-458">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-458">✔</span></span> | <span data-ttu-id="e7f2a-459">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-459">✔</span></span>
<span data-ttu-id="e7f2a-460">Poligono. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="e7f2a-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="e7f2a-461">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-461">✔</span></span> | <span data-ttu-id="e7f2a-462">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-462">✔</span></span> | <span data-ttu-id="e7f2a-463">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-463">✔</span></span> | <span data-ttu-id="e7f2a-464">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-464">✔</span></span>
<span data-ttu-id="e7f2a-465">Poligono. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="e7f2a-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="e7f2a-466">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-466">✔</span></span> | <span data-ttu-id="e7f2a-467">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-467">✔</span></span> | <span data-ttu-id="e7f2a-468">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-468">✔</span></span> | <span data-ttu-id="e7f2a-469">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-469">✔</span></span>
<span data-ttu-id="e7f2a-470">Poligono. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="e7f2a-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="e7f2a-471">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-471">✔</span></span> | <span data-ttu-id="e7f2a-472">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-472">✔</span></span> | <span data-ttu-id="e7f2a-473">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-473">✔</span></span> | <span data-ttu-id="e7f2a-474">✔</span><span class="sxs-lookup"><span data-stu-id="e7f2a-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7f2a-475">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e7f2a-475">Additional resources</span></span>

* [<span data-ttu-id="e7f2a-476">Dati spaziali in SQL Server</span><span class="sxs-lookup"><span data-stu-id="e7f2a-476">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="e7f2a-477">Home page di SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="e7f2a-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="e7f2a-478">Documentazione spaziale di npgsql</span><span class="sxs-lookup"><span data-stu-id="e7f2a-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="e7f2a-479">Documentazione di PostGIS</span><span class="sxs-lookup"><span data-stu-id="e7f2a-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
