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
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="4ef0d-103">Dati spaziali nel provider di EF Core SQLite</span><span class="sxs-lookup"><span data-stu-id="4ef0d-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="4ef0d-104">Questa pagina include informazioni aggiuntive sull'utilizzo dei dati spaziali con il provider di database SQLite.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="4ef0d-105">Per informazioni generali sull'utilizzo dei dati spaziali in EF Core, vedere la documentazione principale sui [dati spaziali](xref:core/modeling/spatial) .</span><span class="sxs-lookup"><span data-stu-id="4ef0d-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="4ef0d-106">Installazione di SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="4ef0d-106">Installing SpatiaLite</span></span>

<span data-ttu-id="4ef0d-107">In Windows la libreria mod_spatialite nativa viene distribuita come dipendenza del pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="4ef0d-108">Altre piattaforme devono essere installate separatamente.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="4ef0d-109">Questa operazione viene in genere eseguita utilizzando una gestione pacchetti software.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="4ef0d-110">Ad esempio, è possibile utilizzare APT in Debian e Ubuntu; e homebrew in MacOS.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="4ef0d-111">Sfortunatamente, le versioni più recenti di PROJ (una dipendenza da SpatiaLite) non sono compatibili con il [bundle SQLitePCLRaw](/dotnet/standard/data/sqlite/custom-versions#bundles)predefinito di EF.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="4ef0d-112">È possibile ovviare a questo problema usando la libreria SQLite di sistema.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="4ef0d-113">In **MacOS**sarà anche necessario impostare una variabile di ambiente prima di eseguire l'app in modo che usi la versione Homebrew di SQLite.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="4ef0d-114">In Visual Studio per Mac, è possibile impostare le opzioni di progetto **> progetto > esegui > configurazioni > impostazione predefinita**</span><span class="sxs-lookup"><span data-stu-id="4ef0d-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="4ef0d-115">Configurazione di SRID</span><span class="sxs-lookup"><span data-stu-id="4ef0d-115">Configuring SRID</span></span>

<span data-ttu-id="4ef0d-116">In SpatiaLite le colonne devono specificare un identificatore SRID per colonna.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="4ef0d-117">L'identificatore SRID predefinito è `0` .</span><span class="sxs-lookup"><span data-stu-id="4ef0d-117">The default SRID is `0`.</span></span> <span data-ttu-id="4ef0d-118">Specificare un identificatore SRID diverso usando il metodo ForSqliteHasSrid.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-118">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="4ef0d-119">4326 si riferisce a WGS 84, uno standard usato in GPS e in altri sistemi geografici.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="4ef0d-120">Dimensione</span><span class="sxs-lookup"><span data-stu-id="4ef0d-120">Dimension</span></span>

<span data-ttu-id="4ef0d-121">La dimensione o le ordinate predefinite di una colonna sono X e Y. Per abilitare ordinamenti aggiuntivi come Z o M, configurare il tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="4ef0d-122">Mapping di funzioni spaziali</span><span class="sxs-lookup"><span data-stu-id="4ef0d-122">Spatial function mappings</span></span>

<span data-ttu-id="4ef0d-123">Questa tabella mostra i membri [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) che vengono tradotti in funzioni SQL.</span><span class="sxs-lookup"><span data-stu-id="4ef0d-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="4ef0d-124">.NET</span><span class="sxs-lookup"><span data-stu-id="4ef0d-124">.NET</span></span>                                        | <span data-ttu-id="4ef0d-125">SQL</span><span class="sxs-lookup"><span data-stu-id="4ef0d-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="4ef0d-126">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="4ef0d-126">geometry.Area</span></span>                               | <span data-ttu-id="4ef0d-127">Area ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-127">Area(@geometry)</span></span>
<span data-ttu-id="4ef0d-128">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="4ef0d-129">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="4ef0d-130">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-130">geometry.AsText()</span></span>                           | <span data-ttu-id="4ef0d-131">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-131">AsText(@geometry)</span></span>
<span data-ttu-id="4ef0d-132">Geometry. Confine</span><span class="sxs-lookup"><span data-stu-id="4ef0d-132">geometry.Boundary</span></span>                           | <span data-ttu-id="4ef0d-133">Limite ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-133">Boundary(@geometry)</span></span>
<span data-ttu-id="4ef0d-134">Geometry. Buffer (distanza)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="4ef0d-135">Buffer ( @geometry , @distance )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="4ef0d-136">Geometry. Buffer (distance, quadrantSegments)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="4ef0d-137">Buffer ( @geometry , @distance , @quadrantSegments )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="4ef0d-138">Geometry. Centro</span><span class="sxs-lookup"><span data-stu-id="4ef0d-138">geometry.Centroid</span></span>                           | <span data-ttu-id="4ef0d-139">Baricentro ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-139">Centroid(@geometry)</span></span>
<span data-ttu-id="4ef0d-140">Geometry. Contains (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="4ef0d-141">Contains ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-142">Geometry. ConvexHull()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="4ef0d-143">ConvexHull ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="4ef0d-144">Geometry. CoveredBy (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="4ef0d-145">CoveredBy ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-146">Geometry. Coperture (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="4ef0d-147">Copre ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-148">Geometry. Crosses (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="4ef0d-149">Crosses ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-150">Geometry. Differenza (altro)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="4ef0d-151">Differenza ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="4ef0d-152">Geometry. Dimensione</span><span class="sxs-lookup"><span data-stu-id="4ef0d-152">geometry.Dimension</span></span>                          | <span data-ttu-id="4ef0d-153">Dimension ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-153">Dimension(@geometry)</span></span>
<span data-ttu-id="4ef0d-154">Geometry. Non contiguo (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="4ef0d-155">Non contiguo ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-156">Geometry. Distanza (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="4ef0d-157">Distance ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-158">Geometry. Busta</span><span class="sxs-lookup"><span data-stu-id="4ef0d-158">geometry.Envelope</span></span>                           | <span data-ttu-id="4ef0d-159">Busta ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-159">Envelope(@geometry)</span></span>
<span data-ttu-id="4ef0d-160">Geometry. EqualsTopologically (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="4ef0d-161">Uguale a ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-162">Geometry. GeometryType</span><span class="sxs-lookup"><span data-stu-id="4ef0d-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="4ef0d-163">GeometryType ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="4ef0d-164">Geometry. Getgeometryn (n)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="4ef0d-165">Geometryn ( @geometry , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="4ef0d-166">Geometry. InteriorPoint</span><span class="sxs-lookup"><span data-stu-id="4ef0d-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="4ef0d-167">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="4ef0d-168">Geometry. Intersezione (altro)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="4ef0d-169">Intersezione ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="4ef0d-170">Geometry. Interseca (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="4ef0d-171">Interseca ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-172">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="4ef0d-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="4ef0d-173">IsEmpty ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="4ef0d-174">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="4ef0d-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="4ef0d-175">Semplice ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="4ef0d-176">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="4ef0d-176">geometry.IsValid</span></span>                            | <span data-ttu-id="4ef0d-177">IsValid ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-177">IsValid(@geometry)</span></span>
<span data-ttu-id="4ef0d-178">Geometry. IsWithinDistance (Geom, distance)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="4ef0d-179">Distance ( @geometry , @geom ) <= @distance</span><span class="sxs-lookup"><span data-stu-id="4ef0d-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="4ef0d-180">Geometry. Lunghezza</span><span class="sxs-lookup"><span data-stu-id="4ef0d-180">geometry.Length</span></span>                             | <span data-ttu-id="4ef0d-181">GLength ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-181">GLength(@geometry)</span></span>
<span data-ttu-id="4ef0d-182">Geometry. NumGeometries</span><span class="sxs-lookup"><span data-stu-id="4ef0d-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="4ef0d-183">NumGeometries ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="4ef0d-184">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="4ef0d-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="4ef0d-185">NumPoints ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="4ef0d-186">Geometry. OgcGeometryType</span><span class="sxs-lookup"><span data-stu-id="4ef0d-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="4ef0d-187">CASE GeometryType ( @geometry ) quando ' Point ' then 1... FINE</span><span class="sxs-lookup"><span data-stu-id="4ef0d-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="4ef0d-188">Geometry. Sovrapposizioni (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="4ef0d-189">Sovrapposizioni ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-190">Geometry. PointOnSurface</span><span class="sxs-lookup"><span data-stu-id="4ef0d-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="4ef0d-191">PointOnSurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="4ef0d-192">Geometry. Correlazione (g, intersectionPattern)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="4ef0d-193">Correlazione ( @geometry , @g , @intersectionPattern )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="4ef0d-194">Geometry. Inverti ()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="4ef0d-195">ST_Reverse ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="4ef0d-196">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="4ef0d-196">geometry.SRID</span></span>                               | <span data-ttu-id="4ef0d-197">SRID ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-197">SRID(@geometry)</span></span>
<span data-ttu-id="4ef0d-198">Geometry. SymmetricDifference (altro)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="4ef0d-199">SymDifference ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="4ef0d-200">Geometry. ToBinary ()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="4ef0d-201">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="4ef0d-202">Geometry. ToText ()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-202">geometry.ToText()</span></span>                           | <span data-ttu-id="4ef0d-203">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-203">AsText(@geometry)</span></span>
<span data-ttu-id="4ef0d-204">Geometry. Tocchi (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="4ef0d-205">Tocchi ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-206">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="4ef0d-206">geometry.Union()</span></span>                            | <span data-ttu-id="4ef0d-207">UnaryUnion ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="4ef0d-208">Geometry. Union (altro)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="4ef0d-209">GUnion ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="4ef0d-210">Geometry. All'interno di (g)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="4ef0d-211">All'interno @geometry di (, @g )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="4ef0d-212">GeometryCollection [i]</span><span class="sxs-lookup"><span data-stu-id="4ef0d-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="4ef0d-213">Geometryn ( @geometryCollection , @i + 1)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="4ef0d-214">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="4ef0d-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="4ef0d-215">NumGeometries ( @geometryCollection )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="4ef0d-216">lineString. Count</span><span class="sxs-lookup"><span data-stu-id="4ef0d-216">lineString.Count</span></span>                            | <span data-ttu-id="4ef0d-217">NumPoints ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="4ef0d-218">lineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="4ef0d-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="4ef0d-219">EndPoint ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="4ef0d-220">lineString. getpointn (n)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="4ef0d-221">Pointn ( @lineString , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="4ef0d-222">lineString. di chiusura</span><span class="sxs-lookup"><span data-stu-id="4ef0d-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="4ef0d-223">Chiuso ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="4ef0d-224">lineString. Ring</span><span class="sxs-lookup"><span data-stu-id="4ef0d-224">lineString.IsRing</span></span>                           | <span data-ttu-id="4ef0d-225">Anello ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-225">IsRing(@lineString)</span></span>
<span data-ttu-id="4ef0d-226">lineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="4ef0d-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="4ef0d-227">StartPoint ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="4ef0d-228">MultiLineString. noclosed</span><span class="sxs-lookup"><span data-stu-id="4ef0d-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="4ef0d-229">Chiuso ( @multiLineString )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="4ef0d-230">punto. M</span><span class="sxs-lookup"><span data-stu-id="4ef0d-230">point.M</span></span>                                     | <span data-ttu-id="4ef0d-231">M ( @point )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-231">M(@point)</span></span>
<span data-ttu-id="4ef0d-232">punto. X</span><span class="sxs-lookup"><span data-stu-id="4ef0d-232">point.X</span></span>                                     | <span data-ttu-id="4ef0d-233">X ( @point )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-233">X(@point)</span></span>
<span data-ttu-id="4ef0d-234">punto. Y</span><span class="sxs-lookup"><span data-stu-id="4ef0d-234">point.Y</span></span>                                     | <span data-ttu-id="4ef0d-235">Y ( @point )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-235">Y(@point)</span></span>
<span data-ttu-id="4ef0d-236">punto. Z</span><span class="sxs-lookup"><span data-stu-id="4ef0d-236">point.Z</span></span>                                     | <span data-ttu-id="4ef0d-237">Z ( @point )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-237">Z(@point)</span></span>
<span data-ttu-id="4ef0d-238">poligono. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="4ef0d-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="4ef0d-239">ExteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="4ef0d-240">poligono. GetInteriorRingN (n)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="4ef0d-241">InteriorRingN ( @polygon , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="4ef0d-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="4ef0d-242">poligono. NumInteriorRings</span><span class="sxs-lookup"><span data-stu-id="4ef0d-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="4ef0d-243">NumInteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="4ef0d-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ef0d-244">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="4ef0d-244">Additional resources</span></span>

* [<span data-ttu-id="4ef0d-245">Home page di SpatiaLite</span><span class="sxs-lookup"><span data-stu-id="4ef0d-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="4ef0d-246">Documentazione di NetTopologySuite</span><span class="sxs-lookup"><span data-stu-id="4ef0d-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
