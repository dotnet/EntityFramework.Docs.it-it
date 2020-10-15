---
title: Spatial-Code First-EF6
description: Code First spaziali in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 6942829809e37b5d62f61488ff6a190c8a38febb
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065238"
---
# <a name="spatial---code-first"></a><span data-ttu-id="a451c-103">Code First spaziali</span><span class="sxs-lookup"><span data-stu-id="a451c-103">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="a451c-104">**EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="a451c-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="a451c-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="a451c-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="a451c-106">Il video e la procedura dettagliata illustrano come eseguire il mapping di tipi spaziali con Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="a451c-106">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="a451c-107">Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.</span><span class="sxs-lookup"><span data-stu-id="a451c-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="a451c-108">In questa procedura dettagliata verrà utilizzato Code First per creare un nuovo database, ma è anche possibile utilizzare [Code First a un database esistente](xref:ef6/modeling/code-first/workflows/existing-database).</span><span class="sxs-lookup"><span data-stu-id="a451c-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="a451c-109">Il supporto del tipo spaziale è stato introdotto in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="a451c-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="a451c-110">Si noti che per utilizzare le nuove funzionalità, ad esempio il tipo spaziale, le enumerazioni e le funzioni con valori di tabella, è necessario definire come destinazione .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="a451c-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="a451c-111">Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="a451c-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="a451c-112">Per utilizzare i tipi di dati spaziali, è necessario utilizzare anche un provider Entity Framework con supporto spaziale.</span><span class="sxs-lookup"><span data-stu-id="a451c-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="a451c-113">Per ulteriori informazioni, vedere [supporto del provider per i tipi spaziali](xref:ef6/fundamentals/providers/spatial-support) .</span><span class="sxs-lookup"><span data-stu-id="a451c-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="a451c-114">Esistono due tipi di dati spaziali principali: geography e Geometry.</span><span class="sxs-lookup"><span data-stu-id="a451c-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="a451c-115">Il tipo di dati geography archivia dati ellissoidali, ad esempio coordinate di latitudine e longitudine GPS.</span><span class="sxs-lookup"><span data-stu-id="a451c-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="a451c-116">Il tipo di dati geometry rappresenta un sistema di coordinate euclideo (piano).</span><span class="sxs-lookup"><span data-stu-id="a451c-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="a451c-117">Video</span><span class="sxs-lookup"><span data-stu-id="a451c-117">Watch the video</span></span>
<span data-ttu-id="a451c-118">In questo video viene illustrato come eseguire il mapping di tipi spaziali con Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="a451c-118">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="a451c-119">Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.</span><span class="sxs-lookup"><span data-stu-id="a451c-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="a451c-120">**Presentato da**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="a451c-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="a451c-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="a451c-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="a451c-122">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="a451c-122">Pre-Requisites</span></span>

<span data-ttu-id="a451c-123">Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.</span><span class="sxs-lookup"><span data-stu-id="a451c-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="a451c-124">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="a451c-124">Set up the Project</span></span>

1.  <span data-ttu-id="a451c-125">Aprire Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="a451c-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="a451c-126">Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .</span><span class="sxs-lookup"><span data-stu-id="a451c-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="a451c-127">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="a451c-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="a451c-128">Immettere **SpatialCodeFirst** come nome del progetto e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="a451c-128">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="a451c-129">Definire un nuovo modello utilizzando Code First</span><span class="sxs-lookup"><span data-stu-id="a451c-129">Define a New Model using Code First</span></span>

<span data-ttu-id="a451c-130">Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio).</span><span class="sxs-lookup"><span data-stu-id="a451c-130">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="a451c-131">Il codice seguente definisce la classe University.</span><span class="sxs-lookup"><span data-stu-id="a451c-131">The code below defines the University class.</span></span>

<span data-ttu-id="a451c-132">L'Università ha la proprietà Location del tipo DbGeography.</span><span class="sxs-lookup"><span data-stu-id="a451c-132">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="a451c-133">Per usare il tipo DbGeography, è necessario aggiungere un riferimento all'assembly System. Data. Entity e aggiungere anche l'istruzione System. Data. Spatial using.</span><span class="sxs-lookup"><span data-stu-id="a451c-133">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="a451c-134">Aprire il file Program.cs e incollare le istruzioni using seguenti all'inizio del file:</span><span class="sxs-lookup"><span data-stu-id="a451c-134">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="a451c-135">Aggiungere la definizione della classe University seguente al file Program.cs.</span><span class="sxs-lookup"><span data-stu-id="a451c-135">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="a451c-136">Definire il tipo derivato DbContext</span><span class="sxs-lookup"><span data-stu-id="a451c-136">Define the DbContext Derived Type</span></span>

<span data-ttu-id="a451c-137">Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; .</span><span class="sxs-lookup"><span data-stu-id="a451c-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="a451c-138">Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.</span><span class="sxs-lookup"><span data-stu-id="a451c-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="a451c-139">Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.</span><span class="sxs-lookup"><span data-stu-id="a451c-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="a451c-140">I tipi DbContext e DbSet sono definiti nell'assembly EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="a451c-140">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="a451c-141">Si aggiungerà un riferimento a questa DLL usando il pacchetto NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="a451c-141">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="a451c-142">In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto.</span><span class="sxs-lookup"><span data-stu-id="a451c-142">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="a451c-143">Selezionare **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="a451c-143">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="a451c-144">Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="a451c-144">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="a451c-145">Fai clic su **Install** (Installa).</span><span class="sxs-lookup"><span data-stu-id="a451c-145">Click **Install**</span></span>

<span data-ttu-id="a451c-146">Si noti che, oltre all'assembly EntityFramework, viene aggiunto anche un riferimento all'assembly System. ComponentModel. DataAnnotations.</span><span class="sxs-lookup"><span data-stu-id="a451c-146">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="a451c-147">Nella parte superiore del file Program.cs aggiungere l'istruzione using seguente:</span><span class="sxs-lookup"><span data-stu-id="a451c-147">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="a451c-148">In Program.cs aggiungere la definizione del contesto.</span><span class="sxs-lookup"><span data-stu-id="a451c-148">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="a451c-149">Mantieni e recupera dati</span><span class="sxs-lookup"><span data-stu-id="a451c-149">Persist and Retrieve Data</span></span>

<span data-ttu-id="a451c-150">Aprire il file Program.cs in cui è definito il metodo Main.</span><span class="sxs-lookup"><span data-stu-id="a451c-150">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="a451c-151">Aggiungere il codice seguente nella funzione Main.</span><span class="sxs-lookup"><span data-stu-id="a451c-151">Add the following code into the Main function.</span></span>

<span data-ttu-id="a451c-152">Il codice aggiunge due nuovi oggetti University al contesto.</span><span class="sxs-lookup"><span data-stu-id="a451c-152">The code adds two new University objects to the context.</span></span> <span data-ttu-id="a451c-153">Le proprietà spaziali vengono inizializzate tramite il Metodo DbGeography. FromText.</span><span class="sxs-lookup"><span data-stu-id="a451c-153">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="a451c-154">Il punto geografico rappresentato come WellKnownText viene passato al metodo.</span><span class="sxs-lookup"><span data-stu-id="a451c-154">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="a451c-155">Il codice salva quindi i dati.</span><span class="sxs-lookup"><span data-stu-id="a451c-155">The code then saves the data.</span></span> <span data-ttu-id="a451c-156">Quindi, la query LINQ che restituisce un oggetto University in cui la posizione è più vicina alla posizione specificata viene costruita ed eseguita.</span><span class="sxs-lookup"><span data-stu-id="a451c-156">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
        {
            Name = "School of Fine Art",
            Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
        });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                        orderby u.Location.Distance(myLocation)
                        select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="a451c-157">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="a451c-157">Compile and run the application.</span></span> <span data-ttu-id="a451c-158">Il programma produce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="a451c-158">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="a451c-159">Visualizzare il database generato</span><span class="sxs-lookup"><span data-stu-id="a451c-159">View the Generated Database</span></span>

<span data-ttu-id="a451c-160">Quando si esegue l'applicazione per la prima volta, il Entity Framework crea automaticamente un database.</span><span class="sxs-lookup"><span data-stu-id="a451c-160">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="a451c-161">Poiché è installato Visual Studio 2012, il database verrà creato nell'istanza del database locale.</span><span class="sxs-lookup"><span data-stu-id="a451c-161">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="a451c-162">Per impostazione predefinita, il Entity Framework denomina il database dopo il nome completo del contesto derivato (in questo esempio **SpatialCodeFirst. UniversityContext**).</span><span class="sxs-lookup"><span data-stu-id="a451c-162">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="a451c-163">Le volte successive verrà utilizzato il database esistente.</span><span class="sxs-lookup"><span data-stu-id="a451c-163">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="a451c-164">Si noti che, se si apportano modifiche al modello dopo che è stato creato il database, è necessario utilizzare Migrazioni Code First per aggiornare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="a451c-164">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="a451c-165">Per un esempio di utilizzo delle migrazioni, vedere [Code First a un nuovo database](xref:ef6/modeling/code-first/workflows/new-database) .</span><span class="sxs-lookup"><span data-stu-id="a451c-165">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="a451c-166">Per visualizzare il database e i dati, eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="a451c-166">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="a451c-167">Nel menu principale di Visual Studio 2012 selezionare **Visualizza**  - &gt; **Esplora oggetti di SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="a451c-167">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="a451c-168">Se il database locale non è presente nell'elenco dei server, fare clic con il pulsante destro del mouse su **SQL Server** e selezionare **Aggiungi SQL Server** utilizzare l' **autenticazione di Windows** predefinita per connettersi all'istanza del database locale.</span><span class="sxs-lookup"><span data-stu-id="a451c-168">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="a451c-169">Espandere il nodo del database locale</span><span class="sxs-lookup"><span data-stu-id="a451c-169">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="a451c-170">Espandere la cartella **database** per visualizzare il nuovo database e passare alla tabella delle **Università**</span><span class="sxs-lookup"><span data-stu-id="a451c-170">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="a451c-171">Per visualizzare i dati, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati** .</span><span class="sxs-lookup"><span data-stu-id="a451c-171">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="a451c-172">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="a451c-172">Summary</span></span>

<span data-ttu-id="a451c-173">In questa procedura dettagliata è stato esaminato come usare i tipi spaziali con Entity Framework Code First.</span><span class="sxs-lookup"><span data-stu-id="a451c-173">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
