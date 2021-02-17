---
title: Dati spaziali-EF Core
description: Utilizzo di dati spaziali in un modello di Entity Framework Core
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 721aa2628d17b89b79160f8f658f8ef0dd78d6a6
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543289"
---
# <a name="spatial-data"></a><span data-ttu-id="9df27-103">Dati spaziali</span><span class="sxs-lookup"><span data-stu-id="9df27-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="9df27-104">Questa funzionalità è stata introdotta in EF Core 2,2.</span><span class="sxs-lookup"><span data-stu-id="9df27-104">This feature was introduced in EF Core 2.2.</span></span>

<span data-ttu-id="9df27-105">I dati spaziali rappresentano la posizione fisica e la forma degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="9df27-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="9df27-106">Molti database forniscono supporto per questo tipo di dati, in modo che possa essere indicizzato e sottoposto a query insieme ad altri dati.</span><span class="sxs-lookup"><span data-stu-id="9df27-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="9df27-107">Gli scenari comuni includono l'esecuzione di query per gli oggetti all'interno di una distanza specificata da una posizione o la selezione dell'oggetto il cui bordo contiene una determinata posizione.</span><span class="sxs-lookup"><span data-stu-id="9df27-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="9df27-108">EF Core supporta il mapping ai tipi di dati spaziali mediante la libreria spaziale NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="9df27-108">EF Core supports mapping to spatial data types using the NetTopologySuite spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="9df27-109">Installazione</span><span class="sxs-lookup"><span data-stu-id="9df27-109">Installing</span></span>

<span data-ttu-id="9df27-110">Per usare i dati spaziali con EF Core, è necessario installare il pacchetto NuGet di supporto appropriato.</span><span class="sxs-lookup"><span data-stu-id="9df27-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="9df27-111">Il pacchetto che è necessario installare dipende dal provider in uso.</span><span class="sxs-lookup"><span data-stu-id="9df27-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="9df27-112">Provider di EF Core</span><span class="sxs-lookup"><span data-stu-id="9df27-112">EF Core Provider</span></span>                        | <span data-ttu-id="9df27-113">Pacchetto NuGet spaziale</span><span class="sxs-lookup"><span data-stu-id="9df27-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="9df27-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="9df27-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="9df27-115">Microsoft. EntityFrameworkCore. SqlServer. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="9df27-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="9df27-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="9df27-117">Microsoft. EntityFrameworkCore. sqlite. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="9df27-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="9df27-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="9df27-119">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="9df27-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="9df27-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="9df27-121">Npgsql. EntityFrameworkCore. PostgreSQL. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
<span data-ttu-id="9df27-122">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="9df27-122">Pomelo.EntityFrameworkCore.MySql</span></span>        | [<span data-ttu-id="9df27-123">Pomelo. EntityFrameworkCore. MySql. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-123">Pomelo.EntityFrameworkCore.MySql.NetTopologySuite</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
<span data-ttu-id="9df27-124">Devart.Data.MySql.EFCore</span><span class="sxs-lookup"><span data-stu-id="9df27-124">Devart.Data.MySql.EFCore</span></span>                | [<span data-ttu-id="9df27-125">Devart. Data. MySql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-125">Devart.Data.MySql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
<span data-ttu-id="9df27-126">Devart.Data.PostgreSql.EFCore</span><span class="sxs-lookup"><span data-stu-id="9df27-126">Devart.Data.PostgreSql.EFCore</span></span>           | [<span data-ttu-id="9df27-127">Devart. Data. PostgreSql. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-127">Devart.Data.PostgreSql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
<span data-ttu-id="9df27-128">Devart.Data.SQLite.EFCore</span><span class="sxs-lookup"><span data-stu-id="9df27-128">Devart.Data.SQLite.EFCore</span></span>               | [<span data-ttu-id="9df27-129">Devart. Data. SQLite. EFCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-129">Devart.Data.SQLite.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
<span data-ttu-id="9df27-130">Teradata.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9df27-130">Teradata.EntityFrameworkCore</span></span>            | [<span data-ttu-id="9df27-131">Teradata. EntityFrameworkCore. NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-131">Teradata.EntityFrameworkCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a><span data-ttu-id="9df27-132">NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-132">NetTopologySuite</span></span>

<span data-ttu-id="9df27-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) è una libreria spaziale per .NET.</span><span class="sxs-lookup"><span data-stu-id="9df27-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) is a spatial library for .NET.</span></span> <span data-ttu-id="9df27-134">EF Core Abilita il mapping ai tipi di dati spaziali nel database usando i tipi NTS nel modello.</span><span class="sxs-lookup"><span data-stu-id="9df27-134">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="9df27-135">Per abilitare il mapping ai tipi spaziali tramite NTS, chiamare il metodo UseNetTopologySuite nel generatore di opzioni DbContext del provider.</span><span class="sxs-lookup"><span data-stu-id="9df27-135">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="9df27-136">Ad esempio, con SQL Server è possibile chiamarlo come questo.</span><span class="sxs-lookup"><span data-stu-id="9df27-136">For example, with SQL Server you'd call it like this.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

<span data-ttu-id="9df27-137">Esistono diversi tipi di dati spaziali.</span><span class="sxs-lookup"><span data-stu-id="9df27-137">There are several spatial data types.</span></span> <span data-ttu-id="9df27-138">Il tipo da utilizzare dipende dai tipi di forme che si desidera consentire.</span><span class="sxs-lookup"><span data-stu-id="9df27-138">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="9df27-139">Di seguito è illustrata la gerarchia di tipi NTS che è possibile usare per le proprietà nel modello.</span><span class="sxs-lookup"><span data-stu-id="9df27-139">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="9df27-140">Si trovano nello `NetTopologySuite.Geometries` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="9df27-140">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="9df27-141">Geometria</span><span class="sxs-lookup"><span data-stu-id="9df27-141">Geometry</span></span>
  * <span data-ttu-id="9df27-142">Point</span><span class="sxs-lookup"><span data-stu-id="9df27-142">Point</span></span>
  * <span data-ttu-id="9df27-143">LineString</span><span class="sxs-lookup"><span data-stu-id="9df27-143">LineString</span></span>
  * <span data-ttu-id="9df27-144">Polygon</span><span class="sxs-lookup"><span data-stu-id="9df27-144">Polygon</span></span>
  * <span data-ttu-id="9df27-145">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="9df27-145">GeometryCollection</span></span>
    * <span data-ttu-id="9df27-146">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="9df27-146">MultiPoint</span></span>
    * <span data-ttu-id="9df27-147">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="9df27-147">MultiLineString</span></span>
    * <span data-ttu-id="9df27-148">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="9df27-148">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="9df27-149">CircularString, CompoundCurve e CurePolygon non sono supportati da NTS.</span><span class="sxs-lookup"><span data-stu-id="9df27-149">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="9df27-150">L'utilizzo del tipo Geometry di base consente di specificare qualsiasi tipo di forma dalla proprietà.</span><span class="sxs-lookup"><span data-stu-id="9df27-150">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

## <a name="longitude-and-latitude"></a><span data-ttu-id="9df27-151">Longitudine e latitudine</span><span class="sxs-lookup"><span data-stu-id="9df27-151">Longitude and Latitude</span></span>

<span data-ttu-id="9df27-152">Le coordinate in NTS sono espresse in termini di valori X e Y.</span><span class="sxs-lookup"><span data-stu-id="9df27-152">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="9df27-153">Per rappresentare la longitudine e la latitudine, usare X per longitudine e Y per la latitudine.</span><span class="sxs-lookup"><span data-stu-id="9df27-153">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="9df27-154">Si noti che questa operazione è all' **indietro** rispetto al `latitude, longitude` formato in cui vengono in genere visualizzati questi valori.</span><span class="sxs-lookup"><span data-stu-id="9df27-154">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

## <a name="querying-data"></a><span data-ttu-id="9df27-155">Esecuzione di query su dati</span><span class="sxs-lookup"><span data-stu-id="9df27-155">Querying Data</span></span>

<span data-ttu-id="9df27-156">Per eseguire il mapping alle tabelle nel [database di esempio Wide World Importers](https://go.microsoft.com/fwlink/?LinkID=800630), è possibile usare le classi di entità seguenti.</span><span class="sxs-lookup"><span data-stu-id="9df27-156">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

<span data-ttu-id="9df27-157">In LINQ, i metodi e le proprietà NTS disponibili come funzioni di database verranno convertiti in SQL.</span><span class="sxs-lookup"><span data-stu-id="9df27-157">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="9df27-158">Ad esempio, i metodi Distance e Contains vengono convertiti nelle query seguenti.</span><span class="sxs-lookup"><span data-stu-id="9df27-158">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="9df27-159">Per informazioni sui metodi supportati, vedere la documentazione del provider.</span><span class="sxs-lookup"><span data-stu-id="9df27-159">See your provider's documentation for which methods are supported.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a><span data-ttu-id="9df27-160">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="9df27-160">Reverse engineering</span></span>

<span data-ttu-id="9df27-161">I pacchetti NuGet spaziali abilitano anche [Reverse Engineering](xref:core/managing-schemas/scaffolding) modelli con proprietà spaziali, ma è necessario installare il pacchetto ***prima*** di eseguire `Scaffold-DbContext` o `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="9df27-161">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="9df27-162">In caso contrario, si riceveranno avvisi relativi alla mancata individuazione dei mapping dei tipi per le colonne e le colonne verranno ignorate.</span><span class="sxs-lookup"><span data-stu-id="9df27-162">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="9df27-163">SRID ignorato durante le operazioni client</span><span class="sxs-lookup"><span data-stu-id="9df27-163">SRID Ignored during client operations</span></span>

<span data-ttu-id="9df27-164">NTS ignora i valori SRID durante le operazioni.</span><span class="sxs-lookup"><span data-stu-id="9df27-164">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="9df27-165">Si presuppone un sistema di coordinate planare.</span><span class="sxs-lookup"><span data-stu-id="9df27-165">It assumes a planar coordinate system.</span></span> <span data-ttu-id="9df27-166">Ciò significa che se si specificano le coordinate in termini di longitudine e latitudine, alcuni valori valutati dal client, ad esempio distanza, lunghezza e area, saranno in gradi, non in metri.</span><span class="sxs-lookup"><span data-stu-id="9df27-166">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="9df27-167">Per valori più significativi, è necessario innanzitutto proiettare le coordinate in un altro sistema di coordinate usando una libreria come [ProjNet (per GeoAPI)](https://github.com/NetTopologySuite/ProjNet4GeoAPI).</span><span class="sxs-lookup"><span data-stu-id="9df27-167">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet (for GeoAPI)](https://github.com/NetTopologySuite/ProjNet4GeoAPI).</span></span>

> [!NOTE]
> <span data-ttu-id="9df27-168">Usare il [pacchetto NuGet ProjNet](https://www.nuget.org/packages/ProjNet/)più recente, **non** quello meno recente denominato ProjNet4GeoAPI.</span><span class="sxs-lookup"><span data-stu-id="9df27-168">Use the newer [ProjNet NuGet package](https://www.nuget.org/packages/ProjNet/), **not** the older package called ProjNet4GeoAPI.</span></span>

<span data-ttu-id="9df27-169">Se un'operazione viene valutata dal server EF Core tramite SQL, l'unità risultante sarà determinata dal database.</span><span class="sxs-lookup"><span data-stu-id="9df27-169">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="9df27-170">Di seguito è riportato un esempio di utilizzo di ProjNet per calcolare la distanza tra due città.</span><span class="sxs-lookup"><span data-stu-id="9df27-170">Here is an example of using ProjNet to calculate the distance between two cities.</span></span>

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> <span data-ttu-id="9df27-171">4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.</span><span class="sxs-lookup"><span data-stu-id="9df27-171">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9df27-172">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9df27-172">Additional resources</span></span>

### <a name="database-specific-information"></a><span data-ttu-id="9df27-173">Informazioni specifiche del database</span><span class="sxs-lookup"><span data-stu-id="9df27-173">Database-specific information</span></span>

<span data-ttu-id="9df27-174">Assicurarsi di leggere la documentazione del provider per informazioni aggiuntive sull'utilizzo dei dati spaziali.</span><span class="sxs-lookup"><span data-stu-id="9df27-174">Be sure to read your provider's documentation for additional information on working with spatial data.</span></span>

* [<span data-ttu-id="9df27-175">Dati spaziali nel provider di SQL Server</span><span class="sxs-lookup"><span data-stu-id="9df27-175">Spatial Data in the SQL Server Provider</span></span>](xref:core/providers/sql-server/spatial)
* [<span data-ttu-id="9df27-176">Dati spaziali nel provider SQLite</span><span class="sxs-lookup"><span data-stu-id="9df27-176">Spatial Data in the SQLite Provider</span></span>](xref:core/providers/sqlite/spatial)
* [<span data-ttu-id="9df27-177">Dati spaziali nel provider npgsql</span><span class="sxs-lookup"><span data-stu-id="9df27-177">Spatial Data in the Npgsql Provider</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a><span data-ttu-id="9df27-178">Altre risorse</span><span class="sxs-lookup"><span data-stu-id="9df27-178">Other resources</span></span>

* [<span data-ttu-id="9df27-179">Documentazione di NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="9df27-179">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
* <span data-ttu-id="9df27-180">[EF Core sessione standup della community](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15), con particolare attenzione ai dati spaziali e a NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="9df27-180">[EF Core Community Standup session](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15), focusing on spatial data and NetTopologySuite.</span></span>
