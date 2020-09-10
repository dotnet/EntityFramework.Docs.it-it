---
title: Dati spaziali-EF Core
description: Utilizzo di dati spaziali in un modello di Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 42386fb132f135d725a2068d91dc49c7f613e277
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616687"
---
# <a name="spatial-data"></a><span data-ttu-id="7cb60-103">Dati spaziali</span><span class="sxs-lookup"><span data-stu-id="7cb60-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="7cb60-104">Questa funzionalità è stata aggiunta in EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="7cb60-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="7cb60-105">I dati spaziali rappresentano la posizione fisica e la forma degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="7cb60-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="7cb60-106">Molti database forniscono supporto per questo tipo di dati, in modo che possa essere indicizzato e sottoposto a query insieme ad altri dati.</span><span class="sxs-lookup"><span data-stu-id="7cb60-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="7cb60-107">Gli scenari comuni includono l'esecuzione di query per gli oggetti all'interno di una distanza specificata da una posizione o la selezione dell'oggetto il cui bordo contiene una determinata posizione.</span><span class="sxs-lookup"><span data-stu-id="7cb60-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="7cb60-108">EF Core supporta il mapping ai tipi di dati spaziali mediante la libreria spaziale [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="7cb60-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="7cb60-109">Installazione</span><span class="sxs-lookup"><span data-stu-id="7cb60-109">Installing</span></span>

<span data-ttu-id="7cb60-110">Per usare i dati spaziali con EF Core, è necessario installare il pacchetto NuGet di supporto appropriato.</span><span class="sxs-lookup"><span data-stu-id="7cb60-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="7cb60-111">Il pacchetto che è necessario installare dipende dal provider in uso.</span><span class="sxs-lookup"><span data-stu-id="7cb60-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="7cb60-112">Provider di EF Core</span><span class="sxs-lookup"><span data-stu-id="7cb60-112">EF Core Provider</span></span>                        | <span data-ttu-id="7cb60-113">Pacchetto NuGet spaziale</span><span class="sxs-lookup"><span data-stu-id="7cb60-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="7cb60-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7cb60-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="7cb60-115">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7cb60-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="7cb60-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7cb60-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="7cb60-117">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7cb60-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="7cb60-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="7cb60-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="7cb60-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7cb60-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="7cb60-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="7cb60-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="7cb60-121">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7cb60-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="7cb60-122">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="7cb60-122">Reverse engineering</span></span>

<span data-ttu-id="7cb60-123">I pacchetti NuGet spaziali abilitano anche [Reverse Engineering](xref:core/managing-schemas/scaffolding) modelli con proprietà spaziali, ma è necessario installare il pacchetto ***prima*** di eseguire `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="7cb60-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="7cb60-124">In caso contrario, si riceveranno avvisi relativi alla mancata individuazione dei mapping dei tipi per le colonne e le colonne verranno ignorate.</span><span class="sxs-lookup"><span data-stu-id="7cb60-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="7cb60-125">NetTopologySuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="7cb60-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="7cb60-126">NetTopologySuite è una libreria spaziale per .NET.</span><span class="sxs-lookup"><span data-stu-id="7cb60-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="7cb60-127">EF Core Abilita il mapping ai tipi di dati spaziali nel database usando i tipi NTS nel modello.</span><span class="sxs-lookup"><span data-stu-id="7cb60-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="7cb60-128">Per abilitare il mapping ai tipi spaziali tramite NTS, chiamare il metodo UseNetTopologySuite nel generatore di opzioni DbContext del provider.</span><span class="sxs-lookup"><span data-stu-id="7cb60-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="7cb60-129">Ad esempio, con SQL Server è possibile chiamarlo come questo.</span><span class="sxs-lookup"><span data-stu-id="7cb60-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="7cb60-130">Esistono diversi tipi di dati spaziali.</span><span class="sxs-lookup"><span data-stu-id="7cb60-130">There are several spatial data types.</span></span> <span data-ttu-id="7cb60-131">Il tipo da utilizzare dipende dai tipi di forme che si desidera consentire.</span><span class="sxs-lookup"><span data-stu-id="7cb60-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="7cb60-132">Di seguito è illustrata la gerarchia di tipi NTS che è possibile usare per le proprietà nel modello.</span><span class="sxs-lookup"><span data-stu-id="7cb60-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="7cb60-133">Si trovano nello `NetTopologySuite.Geometries` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="7cb60-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="7cb60-134">Geometria</span><span class="sxs-lookup"><span data-stu-id="7cb60-134">Geometry</span></span>
  * <span data-ttu-id="7cb60-135">Point</span><span class="sxs-lookup"><span data-stu-id="7cb60-135">Point</span></span>
  * <span data-ttu-id="7cb60-136">LineString</span><span class="sxs-lookup"><span data-stu-id="7cb60-136">LineString</span></span>
  * <span data-ttu-id="7cb60-137">Polygon</span><span class="sxs-lookup"><span data-stu-id="7cb60-137">Polygon</span></span>
  * <span data-ttu-id="7cb60-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="7cb60-138">GeometryCollection</span></span>
    * <span data-ttu-id="7cb60-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="7cb60-139">MultiPoint</span></span>
    * <span data-ttu-id="7cb60-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="7cb60-140">MultiLineString</span></span>
    * <span data-ttu-id="7cb60-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="7cb60-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="7cb60-142">CircularString, CompoundCurve e CurePolygon non sono supportati da NTS.</span><span class="sxs-lookup"><span data-stu-id="7cb60-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="7cb60-143">L'utilizzo del tipo Geometry di base consente di specificare qualsiasi tipo di forma dalla proprietà.</span><span class="sxs-lookup"><span data-stu-id="7cb60-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="7cb60-144">Per eseguire il mapping alle tabelle nel [database di esempio Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630), è possibile usare le classi di entità seguenti.</span><span class="sxs-lookup"><span data-stu-id="7cb60-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="7cb60-145">Creazione di valori</span><span class="sxs-lookup"><span data-stu-id="7cb60-145">Creating values</span></span>

<span data-ttu-id="7cb60-146">È possibile utilizzare i costruttori per creare oggetti Geometry; Tuttavia, NTS consiglia di usare invece una factory Geometry.</span><span class="sxs-lookup"><span data-stu-id="7cb60-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="7cb60-147">In questo modo è possibile specificare un SRID predefinito (il sistema di riferimento spaziale usato dalle coordinate) e il controllo su elementi più avanzati, ad esempio il modello di precisione (usato durante i calcoli) e la sequenza di coordinate (determina le ordinate, ovvero le dimensioni e le misure, sono disponibili).</span><span class="sxs-lookup"><span data-stu-id="7cb60-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="7cb60-148">4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.</span><span class="sxs-lookup"><span data-stu-id="7cb60-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="7cb60-149">Longitudine e latitudine</span><span class="sxs-lookup"><span data-stu-id="7cb60-149">Longitude and Latitude</span></span>

<span data-ttu-id="7cb60-150">Le coordinate in NTS sono espresse in termini di valori X e Y.</span><span class="sxs-lookup"><span data-stu-id="7cb60-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="7cb60-151">Per rappresentare la longitudine e la latitudine, usare X per longitudine e Y per la latitudine.</span><span class="sxs-lookup"><span data-stu-id="7cb60-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="7cb60-152">Si noti che questa operazione è all' **indietro** rispetto al `latitude, longitude` formato in cui vengono in genere visualizzati questi valori.</span><span class="sxs-lookup"><span data-stu-id="7cb60-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="7cb60-153">SRID ignorato durante le operazioni client</span><span class="sxs-lookup"><span data-stu-id="7cb60-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="7cb60-154">NTS ignora i valori SRID durante le operazioni.</span><span class="sxs-lookup"><span data-stu-id="7cb60-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="7cb60-155">Si presuppone un sistema di coordinate planare.</span><span class="sxs-lookup"><span data-stu-id="7cb60-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="7cb60-156">Ciò significa che se si specificano le coordinate in termini di longitudine e latitudine, alcuni valori valutati dal client, ad esempio distanza, lunghezza e area, saranno in gradi, non in metri.</span><span class="sxs-lookup"><span data-stu-id="7cb60-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="7cb60-157">Per valori più significativi, è necessario innanzitutto proiettare le coordinate in un altro sistema di coordinate usando una libreria come [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) prima di calcolare questi valori.</span><span class="sxs-lookup"><span data-stu-id="7cb60-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="7cb60-158">Se un'operazione viene valutata dal server EF Core tramite SQL, l'unità risultante sarà determinata dal database.</span><span class="sxs-lookup"><span data-stu-id="7cb60-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="7cb60-159">Di seguito è riportato un esempio di utilizzo di ProjNet4GeoAPI per calcolare la distanza tra due città.</span><span class="sxs-lookup"><span data-stu-id="7cb60-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="7cb60-160">Esecuzione di query su dati</span><span class="sxs-lookup"><span data-stu-id="7cb60-160">Querying Data</span></span>

<span data-ttu-id="7cb60-161">In LINQ, i metodi e le proprietà NTS disponibili come funzioni di database verranno convertiti in SQL.</span><span class="sxs-lookup"><span data-stu-id="7cb60-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="7cb60-162">Ad esempio, i metodi Distance e Contains vengono convertiti nelle query seguenti.</span><span class="sxs-lookup"><span data-stu-id="7cb60-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="7cb60-163">La tabella alla fine di questo articolo illustra i membri supportati da vari provider di EF Core.</span><span class="sxs-lookup"><span data-stu-id="7cb60-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="7cb60-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="7cb60-164">SQL Server</span></span>

<span data-ttu-id="7cb60-165">Se si usa SQL Server, è necessario tenere presenti alcuni aspetti aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="7cb60-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="7cb60-166">Geografia o geometria</span><span class="sxs-lookup"><span data-stu-id="7cb60-166">Geography or geometry</span></span>

<span data-ttu-id="7cb60-167">Per impostazione predefinita, viene eseguito il mapping delle proprietà spaziali alle `geography` colonne in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7cb60-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="7cb60-168">Per utilizzare `geometry` , [configurare il tipo di colonna](xref:core/modeling/entity-properties#column-data-types) nel modello.</span><span class="sxs-lookup"><span data-stu-id="7cb60-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="7cb60-169">Anelli del poligono geografico</span><span class="sxs-lookup"><span data-stu-id="7cb60-169">Geography polygon rings</span></span>

<span data-ttu-id="7cb60-170">Quando si usa il `geography` tipo di colonna, SQL Server impone requisiti aggiuntivi sull'anello esterno (o sulla Shell) e sugli anelli interni (o buchi).</span><span class="sxs-lookup"><span data-stu-id="7cb60-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="7cb60-171">L'anello esterno deve essere orientato in senso antiorario e gli anelli interni in senso orario.</span><span class="sxs-lookup"><span data-stu-id="7cb60-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="7cb60-172">NTS convalida questa operazione prima di inviare valori al database.</span><span class="sxs-lookup"><span data-stu-id="7cb60-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="7cb60-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="7cb60-173">FullGlobe</span></span>

<span data-ttu-id="7cb60-174">SQL Server dispone di un tipo geometry non standard per rappresentare l'intero globo quando si utilizza il `geography` tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="7cb60-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="7cb60-175">Offre anche un modo per rappresentare i poligoni in base al globo completo (senza anello esterno).</span><span class="sxs-lookup"><span data-stu-id="7cb60-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="7cb60-176">Nessuno di questi è supportato da NTS.</span><span class="sxs-lookup"><span data-stu-id="7cb60-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="7cb60-177">FullGlobe e i poligoni basati su di essi non sono supportati da NTS.</span><span class="sxs-lookup"><span data-stu-id="7cb60-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="7cb60-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="7cb60-178">SQLite</span></span>

<span data-ttu-id="7cb60-179">Di seguito sono riportate alcune informazioni aggiuntive per quelle che usano SQLite.</span><span class="sxs-lookup"><span data-stu-id="7cb60-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="7cb60-180">Installazione di SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="7cb60-180">Installing SpatiaLite</span></span>

<span data-ttu-id="7cb60-181">In Windows la libreria mod_spatialite nativa viene distribuita come dipendenza del pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="7cb60-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="7cb60-182">Altre piattaforme devono essere installate separatamente.</span><span class="sxs-lookup"><span data-stu-id="7cb60-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="7cb60-183">Questa operazione viene in genere eseguita utilizzando una gestione pacchetti software.</span><span class="sxs-lookup"><span data-stu-id="7cb60-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="7cb60-184">Ad esempio, è possibile usare APT in Ubuntu e homebrew in MacOS.</span><span class="sxs-lookup"><span data-stu-id="7cb60-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="7cb60-185">Sfortunatamente, le versioni più recenti di PROJ (una dipendenza da SpatiaLite) non sono compatibili con il [bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predefinito di EF.</span><span class="sxs-lookup"><span data-stu-id="7cb60-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="7cb60-186">È possibile risolvere il problema creando un [provider SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) personalizzato che usa la libreria SQLite di sistema oppure è possibile installare una build personalizzata di SpatiaLite disabilitando il supporto per proj.</span><span class="sxs-lookup"><span data-stu-id="7cb60-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="7cb60-187">Configurazione di SRID</span><span class="sxs-lookup"><span data-stu-id="7cb60-187">Configuring SRID</span></span>

<span data-ttu-id="7cb60-188">In SpatiaLite le colonne devono specificare un identificatore SRID per colonna.</span><span class="sxs-lookup"><span data-stu-id="7cb60-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="7cb60-189">L'identificatore SRID predefinito è `0` .</span><span class="sxs-lookup"><span data-stu-id="7cb60-189">The default SRID is `0`.</span></span> <span data-ttu-id="7cb60-190">Specificare un identificatore SRID diverso usando il metodo ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="7cb60-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="7cb60-191">Dimension</span><span class="sxs-lookup"><span data-stu-id="7cb60-191">Dimension</span></span>

<span data-ttu-id="7cb60-192">Analogamente a SRID, viene specificata anche una dimensione (o ordinata) di una colonna come parte della colonna.</span><span class="sxs-lookup"><span data-stu-id="7cb60-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="7cb60-193">Le ordinate predefinite sono X e Y. abilitare le coordinate aggiuntive (Z e M) usando il metodo ForSqliteHasDimension.</span><span class="sxs-lookup"><span data-stu-id="7cb60-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="7cb60-194">Operazioni convertite</span><span class="sxs-lookup"><span data-stu-id="7cb60-194">Translated Operations</span></span>

<span data-ttu-id="7cb60-195">Questa tabella mostra i membri NTS convertiti in SQL da ogni provider di EF Core.</span><span class="sxs-lookup"><span data-stu-id="7cb60-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="7cb60-196">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="7cb60-196">NetTopologySuite</span></span> | <span data-ttu-id="7cb60-197">SQL Server (geometria)</span><span class="sxs-lookup"><span data-stu-id="7cb60-197">SQL Server (geometry)</span></span> | <span data-ttu-id="7cb60-198">SQL Server (geography)</span><span class="sxs-lookup"><span data-stu-id="7cb60-198">SQL Server (geography)</span></span> | <span data-ttu-id="7cb60-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="7cb60-199">SQLite</span></span> | <span data-ttu-id="7cb60-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="7cb60-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="7cb60-201">Geometry. area</span><span class="sxs-lookup"><span data-stu-id="7cb60-201">Geometry.Area</span></span> | <span data-ttu-id="7cb60-202">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-202">✔</span></span> | <span data-ttu-id="7cb60-203">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-203">✔</span></span> | <span data-ttu-id="7cb60-204">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-204">✔</span></span> | <span data-ttu-id="7cb60-205">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-205">✔</span></span>
<span data-ttu-id="7cb60-206">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="7cb60-207">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-207">✔</span></span> | <span data-ttu-id="7cb60-208">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-208">✔</span></span> | <span data-ttu-id="7cb60-209">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-209">✔</span></span> | <span data-ttu-id="7cb60-210">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-210">✔</span></span>
<span data-ttu-id="7cb60-211">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-211">Geometry.AsText()</span></span> | <span data-ttu-id="7cb60-212">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-212">✔</span></span> | <span data-ttu-id="7cb60-213">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-213">✔</span></span> | <span data-ttu-id="7cb60-214">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-214">✔</span></span> | <span data-ttu-id="7cb60-215">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-215">✔</span></span>
<span data-ttu-id="7cb60-216">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="7cb60-216">Geometry.Boundary</span></span> | <span data-ttu-id="7cb60-217">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-217">✔</span></span> | | <span data-ttu-id="7cb60-218">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-218">✔</span></span> | <span data-ttu-id="7cb60-219">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-219">✔</span></span>
<span data-ttu-id="7cb60-220">Geometry. buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="7cb60-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="7cb60-221">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-221">✔</span></span> | <span data-ttu-id="7cb60-222">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-222">✔</span></span> | <span data-ttu-id="7cb60-223">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-223">✔</span></span> | <span data-ttu-id="7cb60-224">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-224">✔</span></span>
<span data-ttu-id="7cb60-225">Geometry. buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="7cb60-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="7cb60-226">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-226">✔</span></span> | <span data-ttu-id="7cb60-227">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-227">✔</span></span>
<span data-ttu-id="7cb60-228">Geometry. baricentro</span><span class="sxs-lookup"><span data-stu-id="7cb60-228">Geometry.Centroid</span></span> | <span data-ttu-id="7cb60-229">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-229">✔</span></span> | | <span data-ttu-id="7cb60-230">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-230">✔</span></span> | <span data-ttu-id="7cb60-231">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-231">✔</span></span>
<span data-ttu-id="7cb60-232">Geometry. Contains (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="7cb60-233">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-233">✔</span></span> | <span data-ttu-id="7cb60-234">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-234">✔</span></span> | <span data-ttu-id="7cb60-235">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-235">✔</span></span> | <span data-ttu-id="7cb60-236">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-236">✔</span></span>
<span data-ttu-id="7cb60-237">Geometry. ConvexHull ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="7cb60-238">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-238">✔</span></span> | <span data-ttu-id="7cb60-239">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-239">✔</span></span> | <span data-ttu-id="7cb60-240">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-240">✔</span></span> | <span data-ttu-id="7cb60-241">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-241">✔</span></span>
<span data-ttu-id="7cb60-242">Geometry. CoveredBy (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="7cb60-243">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-243">✔</span></span> | <span data-ttu-id="7cb60-244">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-244">✔</span></span>
<span data-ttu-id="7cb60-245">Geometry. Covers (geometria)</span><span class="sxs-lookup"><span data-stu-id="7cb60-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="7cb60-246">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-246">✔</span></span> | <span data-ttu-id="7cb60-247">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-247">✔</span></span>
<span data-ttu-id="7cb60-248">Geometry. Crosses (geometria)</span><span class="sxs-lookup"><span data-stu-id="7cb60-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="7cb60-249">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-249">✔</span></span> | | <span data-ttu-id="7cb60-250">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-250">✔</span></span> | <span data-ttu-id="7cb60-251">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-251">✔</span></span>
<span data-ttu-id="7cb60-252">Geometry. Difference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="7cb60-253">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-253">✔</span></span> | <span data-ttu-id="7cb60-254">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-254">✔</span></span> | <span data-ttu-id="7cb60-255">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-255">✔</span></span> | <span data-ttu-id="7cb60-256">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-256">✔</span></span>
<span data-ttu-id="7cb60-257">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="7cb60-257">Geometry.Dimension</span></span> | <span data-ttu-id="7cb60-258">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-258">✔</span></span> | <span data-ttu-id="7cb60-259">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-259">✔</span></span> | <span data-ttu-id="7cb60-260">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-260">✔</span></span> | <span data-ttu-id="7cb60-261">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-261">✔</span></span>
<span data-ttu-id="7cb60-262">Geometry. unjoint (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="7cb60-263">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-263">✔</span></span> | <span data-ttu-id="7cb60-264">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-264">✔</span></span> | <span data-ttu-id="7cb60-265">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-265">✔</span></span> | <span data-ttu-id="7cb60-266">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-266">✔</span></span>
<span data-ttu-id="7cb60-267">Geometry. distance (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="7cb60-268">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-268">✔</span></span> | <span data-ttu-id="7cb60-269">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-269">✔</span></span> | <span data-ttu-id="7cb60-270">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-270">✔</span></span> | <span data-ttu-id="7cb60-271">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-271">✔</span></span>
<span data-ttu-id="7cb60-272">Geometry. Envelope</span><span class="sxs-lookup"><span data-stu-id="7cb60-272">Geometry.Envelope</span></span> | <span data-ttu-id="7cb60-273">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-273">✔</span></span> | | <span data-ttu-id="7cb60-274">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-274">✔</span></span> | <span data-ttu-id="7cb60-275">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-275">✔</span></span>
<span data-ttu-id="7cb60-276">Geometry. EqualsExact (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="7cb60-277">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-277">✔</span></span>
<span data-ttu-id="7cb60-278">Geometry. EqualsTopologically (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="7cb60-279">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-279">✔</span></span> | <span data-ttu-id="7cb60-280">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-280">✔</span></span> | <span data-ttu-id="7cb60-281">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-281">✔</span></span> | <span data-ttu-id="7cb60-282">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-282">✔</span></span>
<span data-ttu-id="7cb60-283">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="7cb60-283">Geometry.GeometryType</span></span> | <span data-ttu-id="7cb60-284">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-284">✔</span></span> | <span data-ttu-id="7cb60-285">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-285">✔</span></span> | <span data-ttu-id="7cb60-286">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-286">✔</span></span> | <span data-ttu-id="7cb60-287">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-287">✔</span></span>
<span data-ttu-id="7cb60-288">Geometry. getgeometryn (int)</span><span class="sxs-lookup"><span data-stu-id="7cb60-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="7cb60-289">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-289">✔</span></span> | | <span data-ttu-id="7cb60-290">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-290">✔</span></span> | <span data-ttu-id="7cb60-291">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-291">✔</span></span>
<span data-ttu-id="7cb60-292">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="7cb60-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="7cb60-293">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-293">✔</span></span> | | <span data-ttu-id="7cb60-294">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-294">✔</span></span> | <span data-ttu-id="7cb60-295">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-295">✔</span></span>
<span data-ttu-id="7cb60-296">Geometry. intersezione (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="7cb60-297">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-297">✔</span></span> | <span data-ttu-id="7cb60-298">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-298">✔</span></span> | <span data-ttu-id="7cb60-299">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-299">✔</span></span> | <span data-ttu-id="7cb60-300">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-300">✔</span></span>
<span data-ttu-id="7cb60-301">Geometry. Intersects (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="7cb60-302">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-302">✔</span></span> | <span data-ttu-id="7cb60-303">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-303">✔</span></span> | <span data-ttu-id="7cb60-304">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-304">✔</span></span> | <span data-ttu-id="7cb60-305">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-305">✔</span></span>
<span data-ttu-id="7cb60-306">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="7cb60-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="7cb60-307">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-307">✔</span></span> | <span data-ttu-id="7cb60-308">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-308">✔</span></span> | <span data-ttu-id="7cb60-309">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-309">✔</span></span> | <span data-ttu-id="7cb60-310">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-310">✔</span></span>
<span data-ttu-id="7cb60-311">Geometry. Simple</span><span class="sxs-lookup"><span data-stu-id="7cb60-311">Geometry.IsSimple</span></span> | <span data-ttu-id="7cb60-312">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-312">✔</span></span> | | <span data-ttu-id="7cb60-313">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-313">✔</span></span> | <span data-ttu-id="7cb60-314">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-314">✔</span></span>
<span data-ttu-id="7cb60-315">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="7cb60-315">Geometry.IsValid</span></span> | <span data-ttu-id="7cb60-316">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-316">✔</span></span> | <span data-ttu-id="7cb60-317">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-317">✔</span></span> | <span data-ttu-id="7cb60-318">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-318">✔</span></span> | <span data-ttu-id="7cb60-319">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-319">✔</span></span>
<span data-ttu-id="7cb60-320">Geometry. IsWithinDistance (Geometry, Double)</span><span class="sxs-lookup"><span data-stu-id="7cb60-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="7cb60-321">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-321">✔</span></span> | | <span data-ttu-id="7cb60-322">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-322">✔</span></span> | <span data-ttu-id="7cb60-323">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-323">✔</span></span>
<span data-ttu-id="7cb60-324">Geometry. length</span><span class="sxs-lookup"><span data-stu-id="7cb60-324">Geometry.Length</span></span> | <span data-ttu-id="7cb60-325">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-325">✔</span></span> | <span data-ttu-id="7cb60-326">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-326">✔</span></span> | <span data-ttu-id="7cb60-327">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-327">✔</span></span> | <span data-ttu-id="7cb60-328">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-328">✔</span></span>
<span data-ttu-id="7cb60-329">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="7cb60-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="7cb60-330">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-330">✔</span></span> | <span data-ttu-id="7cb60-331">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-331">✔</span></span> | <span data-ttu-id="7cb60-332">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-332">✔</span></span> | <span data-ttu-id="7cb60-333">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-333">✔</span></span>
<span data-ttu-id="7cb60-334">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="7cb60-334">Geometry.NumPoints</span></span> | <span data-ttu-id="7cb60-335">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-335">✔</span></span> | <span data-ttu-id="7cb60-336">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-336">✔</span></span> | <span data-ttu-id="7cb60-337">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-337">✔</span></span> | <span data-ttu-id="7cb60-338">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-338">✔</span></span>
<span data-ttu-id="7cb60-339">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="7cb60-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="7cb60-340">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-340">✔</span></span> | <span data-ttu-id="7cb60-341">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-341">✔</span></span> | <span data-ttu-id="7cb60-342">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-342">✔</span></span> | <span data-ttu-id="7cb60-343">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-343">✔</span></span>
<span data-ttu-id="7cb60-344">Geometry. sovrapposizioni (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="7cb60-345">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-345">✔</span></span> | <span data-ttu-id="7cb60-346">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-346">✔</span></span> | <span data-ttu-id="7cb60-347">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-347">✔</span></span> | <span data-ttu-id="7cb60-348">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-348">✔</span></span>
<span data-ttu-id="7cb60-349">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="7cb60-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="7cb60-350">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-350">✔</span></span> | | <span data-ttu-id="7cb60-351">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-351">✔</span></span> | <span data-ttu-id="7cb60-352">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-352">✔</span></span>
<span data-ttu-id="7cb60-353">Geometry. correlate (Geometry, String)</span><span class="sxs-lookup"><span data-stu-id="7cb60-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="7cb60-354">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-354">✔</span></span> | | <span data-ttu-id="7cb60-355">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-355">✔</span></span> | <span data-ttu-id="7cb60-356">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-356">✔</span></span>
<span data-ttu-id="7cb60-357">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="7cb60-358">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-358">✔</span></span> | <span data-ttu-id="7cb60-359">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-359">✔</span></span>
<span data-ttu-id="7cb60-360">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="7cb60-360">Geometry.SRID</span></span> | <span data-ttu-id="7cb60-361">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-361">✔</span></span> | <span data-ttu-id="7cb60-362">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-362">✔</span></span> | <span data-ttu-id="7cb60-363">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-363">✔</span></span> | <span data-ttu-id="7cb60-364">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-364">✔</span></span>
<span data-ttu-id="7cb60-365">Geometry. SymmetricDifference (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="7cb60-366">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-366">✔</span></span> | <span data-ttu-id="7cb60-367">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-367">✔</span></span> | <span data-ttu-id="7cb60-368">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-368">✔</span></span> | <span data-ttu-id="7cb60-369">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-369">✔</span></span>
<span data-ttu-id="7cb60-370">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="7cb60-371">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-371">✔</span></span> | <span data-ttu-id="7cb60-372">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-372">✔</span></span> | <span data-ttu-id="7cb60-373">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-373">✔</span></span> | <span data-ttu-id="7cb60-374">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-374">✔</span></span>
<span data-ttu-id="7cb60-375">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-375">Geometry.ToText()</span></span> | <span data-ttu-id="7cb60-376">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-376">✔</span></span> | <span data-ttu-id="7cb60-377">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-377">✔</span></span> | <span data-ttu-id="7cb60-378">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-378">✔</span></span> | <span data-ttu-id="7cb60-379">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-379">✔</span></span>
<span data-ttu-id="7cb60-380">Geometry. touchs (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="7cb60-381">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-381">✔</span></span> | | <span data-ttu-id="7cb60-382">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-382">✔</span></span> | <span data-ttu-id="7cb60-383">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-383">✔</span></span>
<span data-ttu-id="7cb60-384">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="7cb60-384">Geometry.Union()</span></span> | | | <span data-ttu-id="7cb60-385">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-385">✔</span></span> | <span data-ttu-id="7cb60-386">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-386">✔</span></span>
<span data-ttu-id="7cb60-387">Geometry. Union (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="7cb60-388">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-388">✔</span></span> | <span data-ttu-id="7cb60-389">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-389">✔</span></span> | <span data-ttu-id="7cb60-390">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-390">✔</span></span> | <span data-ttu-id="7cb60-391">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-391">✔</span></span>
<span data-ttu-id="7cb60-392">Geometry. Within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="7cb60-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="7cb60-393">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-393">✔</span></span> | <span data-ttu-id="7cb60-394">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-394">✔</span></span> | <span data-ttu-id="7cb60-395">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-395">✔</span></span> | <span data-ttu-id="7cb60-396">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-396">✔</span></span>
<span data-ttu-id="7cb60-397">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="7cb60-397">GeometryCollection.Count</span></span> | <span data-ttu-id="7cb60-398">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-398">✔</span></span> | <span data-ttu-id="7cb60-399">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-399">✔</span></span> | <span data-ttu-id="7cb60-400">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-400">✔</span></span> | <span data-ttu-id="7cb60-401">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-401">✔</span></span>
<span data-ttu-id="7cb60-402">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="7cb60-402">GeometryCollection[int]</span></span> | <span data-ttu-id="7cb60-403">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-403">✔</span></span> | <span data-ttu-id="7cb60-404">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-404">✔</span></span> | <span data-ttu-id="7cb60-405">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-405">✔</span></span> | <span data-ttu-id="7cb60-406">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-406">✔</span></span>
<span data-ttu-id="7cb60-407">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="7cb60-407">LineString.Count</span></span> | <span data-ttu-id="7cb60-408">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-408">✔</span></span> | <span data-ttu-id="7cb60-409">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-409">✔</span></span> | <span data-ttu-id="7cb60-410">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-410">✔</span></span> | <span data-ttu-id="7cb60-411">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-411">✔</span></span>
<span data-ttu-id="7cb60-412">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="7cb60-412">LineString.EndPoint</span></span> | <span data-ttu-id="7cb60-413">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-413">✔</span></span> | <span data-ttu-id="7cb60-414">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-414">✔</span></span> | <span data-ttu-id="7cb60-415">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-415">✔</span></span> | <span data-ttu-id="7cb60-416">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-416">✔</span></span>
<span data-ttu-id="7cb60-417">LineString. getpointn (int)</span><span class="sxs-lookup"><span data-stu-id="7cb60-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="7cb60-418">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-418">✔</span></span> | <span data-ttu-id="7cb60-419">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-419">✔</span></span> | <span data-ttu-id="7cb60-420">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-420">✔</span></span> | <span data-ttu-id="7cb60-421">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-421">✔</span></span>
<span data-ttu-id="7cb60-422">LineString. di chiusura</span><span class="sxs-lookup"><span data-stu-id="7cb60-422">LineString.IsClosed</span></span> | <span data-ttu-id="7cb60-423">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-423">✔</span></span> | <span data-ttu-id="7cb60-424">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-424">✔</span></span> | <span data-ttu-id="7cb60-425">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-425">✔</span></span> | <span data-ttu-id="7cb60-426">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-426">✔</span></span>
<span data-ttu-id="7cb60-427">LineString. Ring</span><span class="sxs-lookup"><span data-stu-id="7cb60-427">LineString.IsRing</span></span> | <span data-ttu-id="7cb60-428">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-428">✔</span></span> | | <span data-ttu-id="7cb60-429">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-429">✔</span></span> | <span data-ttu-id="7cb60-430">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-430">✔</span></span>
<span data-ttu-id="7cb60-431">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="7cb60-431">LineString.StartPoint</span></span> | <span data-ttu-id="7cb60-432">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-432">✔</span></span> | <span data-ttu-id="7cb60-433">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-433">✔</span></span> | <span data-ttu-id="7cb60-434">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-434">✔</span></span> | <span data-ttu-id="7cb60-435">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-435">✔</span></span>
<span data-ttu-id="7cb60-436">MultiLineString. noclosed</span><span class="sxs-lookup"><span data-stu-id="7cb60-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="7cb60-437">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-437">✔</span></span> | <span data-ttu-id="7cb60-438">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-438">✔</span></span> | <span data-ttu-id="7cb60-439">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-439">✔</span></span> | <span data-ttu-id="7cb60-440">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-440">✔</span></span>
<span data-ttu-id="7cb60-441">Punto. M</span><span class="sxs-lookup"><span data-stu-id="7cb60-441">Point.M</span></span> | <span data-ttu-id="7cb60-442">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-442">✔</span></span> | <span data-ttu-id="7cb60-443">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-443">✔</span></span> | <span data-ttu-id="7cb60-444">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-444">✔</span></span> | <span data-ttu-id="7cb60-445">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-445">✔</span></span>
<span data-ttu-id="7cb60-446">Punto. X</span><span class="sxs-lookup"><span data-stu-id="7cb60-446">Point.X</span></span> | <span data-ttu-id="7cb60-447">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-447">✔</span></span> | <span data-ttu-id="7cb60-448">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-448">✔</span></span> | <span data-ttu-id="7cb60-449">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-449">✔</span></span> | <span data-ttu-id="7cb60-450">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-450">✔</span></span>
<span data-ttu-id="7cb60-451">Punto. Y</span><span class="sxs-lookup"><span data-stu-id="7cb60-451">Point.Y</span></span> | <span data-ttu-id="7cb60-452">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-452">✔</span></span> | <span data-ttu-id="7cb60-453">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-453">✔</span></span> | <span data-ttu-id="7cb60-454">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-454">✔</span></span> | <span data-ttu-id="7cb60-455">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-455">✔</span></span>
<span data-ttu-id="7cb60-456">Point. Z</span><span class="sxs-lookup"><span data-stu-id="7cb60-456">Point.Z</span></span> | <span data-ttu-id="7cb60-457">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-457">✔</span></span> | <span data-ttu-id="7cb60-458">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-458">✔</span></span> | <span data-ttu-id="7cb60-459">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-459">✔</span></span> | <span data-ttu-id="7cb60-460">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-460">✔</span></span>
<span data-ttu-id="7cb60-461">Poligono. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="7cb60-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="7cb60-462">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-462">✔</span></span> | <span data-ttu-id="7cb60-463">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-463">✔</span></span> | <span data-ttu-id="7cb60-464">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-464">✔</span></span> | <span data-ttu-id="7cb60-465">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-465">✔</span></span>
<span data-ttu-id="7cb60-466">Poligono. GetInteriorRingN (int)</span><span class="sxs-lookup"><span data-stu-id="7cb60-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="7cb60-467">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-467">✔</span></span> | <span data-ttu-id="7cb60-468">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-468">✔</span></span> | <span data-ttu-id="7cb60-469">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-469">✔</span></span> | <span data-ttu-id="7cb60-470">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-470">✔</span></span>
<span data-ttu-id="7cb60-471">Poligono. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="7cb60-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="7cb60-472">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-472">✔</span></span> | <span data-ttu-id="7cb60-473">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-473">✔</span></span> | <span data-ttu-id="7cb60-474">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-474">✔</span></span> | <span data-ttu-id="7cb60-475">✔</span><span class="sxs-lookup"><span data-stu-id="7cb60-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7cb60-476">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7cb60-476">Additional resources</span></span>

* [<span data-ttu-id="7cb60-477">Dati spaziali in SQL Server</span><span class="sxs-lookup"><span data-stu-id="7cb60-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="7cb60-478">Home page di SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="7cb60-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="7cb60-479">Documentazione spaziale di npgsql</span><span class="sxs-lookup"><span data-stu-id="7cb60-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="7cb60-480">Documentazione di PostGIS</span><span class="sxs-lookup"><span data-stu-id="7cb60-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
