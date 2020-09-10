---
title: Spatial-EF designer-EF6
description: Spatial-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 10430d6a043182e5536dfad148dc4158cf4c96c9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620488"
---
# <a name="spatial---ef-designer"></a><span data-ttu-id="96db6-103">Finestra di progettazione spaziale-EF</span><span class="sxs-lookup"><span data-stu-id="96db6-103">Spatial - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="96db6-104">**EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="96db6-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="96db6-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="96db6-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="96db6-106">Il video e la procedura dettagliata illustrano come eseguire il mapping di tipi spaziali con la Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="96db6-106">The video and step-by-step walkthrough shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="96db6-107">Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.</span><span class="sxs-lookup"><span data-stu-id="96db6-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="96db6-108">Questa procedura dettagliata utilizzerà Model First per creare un nuovo database, ma è anche possibile usare la finestra di progettazione EF con il flusso di lavoro [database First](xref:ef6/modeling/designer/workflows/database-first) per eseguire il mapping a un database esistente.</span><span class="sxs-lookup"><span data-stu-id="96db6-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="96db6-109">Il supporto del tipo spaziale è stato introdotto in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="96db6-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="96db6-110">Si noti che per utilizzare le nuove funzionalità, ad esempio il tipo spaziale, le enumerazioni e le funzioni con valori di tabella, è necessario definire come destinazione .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="96db6-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="96db6-111">Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="96db6-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="96db6-112">Per utilizzare i tipi di dati spaziali, è necessario utilizzare anche un provider Entity Framework con supporto spaziale.</span><span class="sxs-lookup"><span data-stu-id="96db6-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="96db6-113">Per ulteriori informazioni, vedere [supporto del provider per i tipi spaziali](xref:ef6/fundamentals/providers/spatial-support) .</span><span class="sxs-lookup"><span data-stu-id="96db6-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="96db6-114">Esistono due tipi di dati spaziali principali: geography e Geometry.</span><span class="sxs-lookup"><span data-stu-id="96db6-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="96db6-115">Il tipo di dati geography archivia dati ellissoidali, ad esempio coordinate di latitudine e longitudine GPS.</span><span class="sxs-lookup"><span data-stu-id="96db6-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="96db6-116">Il tipo di dati geometry rappresenta un sistema di coordinate euclideo (piano).</span><span class="sxs-lookup"><span data-stu-id="96db6-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="96db6-117">Video</span><span class="sxs-lookup"><span data-stu-id="96db6-117">Watch the video</span></span>
<span data-ttu-id="96db6-118">In questo video viene illustrato come eseguire il mapping di tipi spaziali con la Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="96db6-118">This video shows how to map spatial types with the Entity Framework Designer.</span></span> <span data-ttu-id="96db6-119">Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.</span><span class="sxs-lookup"><span data-stu-id="96db6-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="96db6-120">**Presentato da**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="96db6-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="96db6-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span><span class="sxs-lookup"><span data-stu-id="96db6-121">**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv) | [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="96db6-122">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="96db6-122">Pre-Requisites</span></span>

<span data-ttu-id="96db6-123">Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.</span><span class="sxs-lookup"><span data-stu-id="96db6-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="96db6-124">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="96db6-124">Set up the Project</span></span>

1.  <span data-ttu-id="96db6-125">Aprire Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="96db6-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="96db6-126">Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .</span><span class="sxs-lookup"><span data-stu-id="96db6-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="96db6-127">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="96db6-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="96db6-128">Immettere **SpatialEFDesigner** come nome del progetto e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="96db6-128">Enter **SpatialEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="96db6-129">Creare un nuovo modello usando EF designer</span><span class="sxs-lookup"><span data-stu-id="96db6-129">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="96db6-130">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento** .</span><span class="sxs-lookup"><span data-stu-id="96db6-130">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="96db6-131">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="96db6-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="96db6-132">Immettere **UniversityModel. edmx** per il nome del file e quindi fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="96db6-132">Enter **UniversityModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="96db6-133">Nella pagina Entity Data Model procedura guidata selezionare **modello vuoto** nella finestra di dialogo Scegli contenuto Model</span><span class="sxs-lookup"><span data-stu-id="96db6-133">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="96db6-134">Fare clic su **Fine**</span><span class="sxs-lookup"><span data-stu-id="96db6-134">Click **Finish**</span></span>

<span data-ttu-id="96db6-135">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="96db6-135">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="96db6-136">La procedura guidata consente di effettuare le azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="96db6-136">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="96db6-137">Genera il file EnumTestModel. edmx che definisce il modello concettuale, il modello di archiviazione e il mapping tra di essi.</span><span class="sxs-lookup"><span data-stu-id="96db6-137">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="96db6-138">Imposta la proprietà di elaborazione degli artefatti dei metadati del file con estensione edmx da incorporare nell'assembly di output in modo che i file di metadati generati vengano incorporati nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="96db6-138">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="96db6-139">Aggiunge un riferimento agli assembly seguenti: EntityFramework, System. ComponentModel. DataAnnotations e System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="96db6-139">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="96db6-140">Crea i file UniversityModel.tt e UniversityModel.Context.tt e li aggiunge al file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="96db6-140">Creates UniversityModel.tt and UniversityModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="96db6-141">Questi file di modello T4 generano il codice che definisce il tipo derivato DbContext e i tipi POCO mappati alle entità nel modello con estensione edmx</span><span class="sxs-lookup"><span data-stu-id="96db6-141">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="96db6-142">Aggiungere un nuovo tipo di entità</span><span class="sxs-lookup"><span data-stu-id="96db6-142">Add a New Entity Type</span></span>

1.  <span data-ttu-id="96db6-143">Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi &gt; entità**. verrà visualizzata la finestra di dialogo nuova entità.</span><span class="sxs-lookup"><span data-stu-id="96db6-143">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="96db6-144">Specificare **University** per il nome del tipo e specificare **UniversityID** per il nome della proprietà chiave, lasciare il tipo come **Int32**</span><span class="sxs-lookup"><span data-stu-id="96db6-144">Specify **University** for the type name and specify **UniversityID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="96db6-145">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="96db6-145">Click **OK**</span></span>
4.  <span data-ttu-id="96db6-146">Fare clic con il pulsante destro del mouse sull'entità e scegliere **Aggiungi nuova- &gt; scala proprietà**</span><span class="sxs-lookup"><span data-stu-id="96db6-146">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="96db6-147">Rinominare la nuova proprietà in **nome**</span><span class="sxs-lookup"><span data-stu-id="96db6-147">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="96db6-148">Aggiungere un'altra proprietà scalare e rinominarla in **location** aprire il finestra Proprietà e modificare il tipo della nuova proprietà in **geography**</span><span class="sxs-lookup"><span data-stu-id="96db6-148">Add another scalar property and rename it to **Location** Open the Properties window and change the type of the new property to **Geography**</span></span>
7.  <span data-ttu-id="96db6-149">Salvare il modello e compilare il progetto</span><span class="sxs-lookup"><span data-stu-id="96db6-149">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="96db6-150">Quando si compila, i messaggi di avviso sulle entità e sulle associazioni non mappate possono essere visualizzati nel Elenco errori.</span><span class="sxs-lookup"><span data-stu-id="96db6-150">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="96db6-151">È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.</span><span class="sxs-lookup"><span data-stu-id="96db6-151">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="96db6-152">Genera database dal modello</span><span class="sxs-lookup"><span data-stu-id="96db6-152">Generate Database from Model</span></span>

<span data-ttu-id="96db6-153">A questo punto è possibile generare un database basato sul modello.</span><span class="sxs-lookup"><span data-stu-id="96db6-153">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="96db6-154">Fare clic con il pulsante destro del mouse su uno spazio vuoto nell'area di Entity Designer e selezionare **genera database da modello** .</span><span class="sxs-lookup"><span data-stu-id="96db6-154">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="96db6-155">Viene visualizzata la finestra di dialogo scegliere la connessione dati della procedura guidata genera database. fare clic sul pulsante **nuova connessione** specificare (local DB **) \\ mssqllocaldb** per il nome del server e l' **Università** del database e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="96db6-155">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **University** for the database and click **OK**</span></span>
3.  <span data-ttu-id="96db6-156">Viene visualizzata una finestra di dialogo in cui viene chiesto se si desidera creare un nuovo database, quindi fare clic su **Sì**.</span><span class="sxs-lookup"><span data-stu-id="96db6-156">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="96db6-157">Fare clic su **Avanti** . la procedura guidata Crea database genera Data Definition Language (DDL) per la creazione di un database. la DDL generata viene visualizzata nella finestra di dialogo Riepilogo e impostazioni nota che il DDL non contiene una definizione per una tabella che esegue il mapping al tipo di enumerazione</span><span class="sxs-lookup"><span data-stu-id="96db6-157">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="96db6-158">Fare **clic su fine per** non eseguire lo script DDL.</span><span class="sxs-lookup"><span data-stu-id="96db6-158">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="96db6-159">La procedura guidata Crea database esegue le operazioni seguenti: apre **UniversityModel. edmx. SQL** nell'editor T-SQL genera le sezioni schema e mapping dell'archivio del file edmx aggiunge le informazioni sulla stringa di connessione al file di App.config</span><span class="sxs-lookup"><span data-stu-id="96db6-159">The Create Database Wizard does the following: Opens the **UniversityModel.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="96db6-160">Fare clic con il pulsante destro del mouse nell'editor T-SQL e selezionare **Esegui** la finestra di dialogo Connetti al server, immettere le informazioni di connessione nel passaggio 2 e fare clic su **Connetti** .</span><span class="sxs-lookup"><span data-stu-id="96db6-160">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="96db6-161">Per visualizzare lo schema generato, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna** .</span><span class="sxs-lookup"><span data-stu-id="96db6-161">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="96db6-162">Mantieni e recupera dati</span><span class="sxs-lookup"><span data-stu-id="96db6-162">Persist and Retrieve Data</span></span>

<span data-ttu-id="96db6-163">Aprire il file Program.cs in cui è definito il metodo Main.</span><span class="sxs-lookup"><span data-stu-id="96db6-163">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="96db6-164">Aggiungere il codice seguente nella funzione Main.</span><span class="sxs-lookup"><span data-stu-id="96db6-164">Add the following code into the Main function.</span></span>

<span data-ttu-id="96db6-165">Il codice aggiunge due nuovi oggetti University al contesto.</span><span class="sxs-lookup"><span data-stu-id="96db6-165">The code adds two new University objects to the context.</span></span> <span data-ttu-id="96db6-166">Le proprietà spaziali vengono inizializzate tramite il Metodo DbGeography. FromText.</span><span class="sxs-lookup"><span data-stu-id="96db6-166">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="96db6-167">Il punto geografico rappresentato come WellKnownText viene passato al metodo.</span><span class="sxs-lookup"><span data-stu-id="96db6-167">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="96db6-168">Il codice salva quindi i dati.</span><span class="sxs-lookup"><span data-stu-id="96db6-168">The code then saves the data.</span></span> <span data-ttu-id="96db6-169">Quindi, la query LINQ che restituisce un oggetto University in cui la posizione è più vicina alla posizione specificata viene costruita ed eseguita.</span><span class="sxs-lookup"><span data-stu-id="96db6-169">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
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

<span data-ttu-id="96db6-170">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="96db6-170">Compile and run the application.</span></span> <span data-ttu-id="96db6-171">Il programma produce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="96db6-171">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

<span data-ttu-id="96db6-172">Per visualizzare i dati nel database, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna**.</span><span class="sxs-lookup"><span data-stu-id="96db6-172">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="96db6-173">Quindi, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati**.</span><span class="sxs-lookup"><span data-stu-id="96db6-173">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="96db6-174">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="96db6-174">Summary</span></span>

<span data-ttu-id="96db6-175">In questa procedura dettagliata è stato illustrato come eseguire il mapping dei tipi spaziali usando il Entity Framework Designer e come usare i tipi spaziali nel codice.</span><span class="sxs-lookup"><span data-stu-id="96db6-175">In this walkthrough we looked at how to map spatial types using the Entity Framework Designer and how to use spatial types in code.</span></span> 
