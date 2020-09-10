---
title: Supporto enum-EF designer-EF6
description: Supporto enum-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620507"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="e5dbf-103">Supporto enum-EF designer</span><span class="sxs-lookup"><span data-stu-id="e5dbf-103">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="e5dbf-104">**EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="e5dbf-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="e5dbf-106">Questo video e la procedura dettagliata illustrano come usare i tipi enum con la Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-106">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="e5dbf-107">Viene inoltre illustrato come utilizzare le enumerazioni in una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="e5dbf-108">Questa procedura dettagliata utilizzerà Model First per creare un nuovo database, ma è anche possibile usare la finestra di progettazione EF con il flusso di lavoro [database First](xref:ef6/modeling/designer/workflows/database-first) per eseguire il mapping a un database esistente.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="e5dbf-109">Il supporto enum è stato introdotto in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="e5dbf-110">Per utilizzare le nuove funzionalità come enum, i tipi di dati spaziali e le funzioni con valori di tabella, è necessario specificare come destinazione .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="e5dbf-111">Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="e5dbf-112">In Entity Framework, un'enumerazione può includere i seguenti tipi sottostanti: **byte**, **Int16**, **Int32**, **Int64** o **SByte**.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="e5dbf-113">Guarda il video</span><span class="sxs-lookup"><span data-stu-id="e5dbf-113">Watch the Video</span></span>
<span data-ttu-id="e5dbf-114">In questo video viene illustrato come utilizzare i tipi enum con la Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-114">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="e5dbf-115">Viene inoltre illustrato come utilizzare le enumerazioni in una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="e5dbf-116">**Presentato da**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="e5dbf-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="e5dbf-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="e5dbf-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="e5dbf-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e5dbf-118">Pre-Requisites</span></span>

<span data-ttu-id="e5dbf-119">Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e5dbf-120">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="e5dbf-120">Set up the Project</span></span>

1.  <span data-ttu-id="e5dbf-121">Aprire Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="e5dbf-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="e5dbf-122">Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="e5dbf-123">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="e5dbf-124">Immettere **EnumEFDesigner** come nome del progetto e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-124">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="e5dbf-125">Creare un nuovo modello usando EF designer</span><span class="sxs-lookup"><span data-stu-id="e5dbf-125">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="e5dbf-126">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-126">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="e5dbf-127">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="e5dbf-128">Immettere **EnumTestModel. edmx** per il nome del file e quindi fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-128">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="e5dbf-129">Nella pagina Entity Data Model procedura guidata selezionare **modello vuoto** nella finestra di dialogo Scegli contenuto Model</span><span class="sxs-lookup"><span data-stu-id="e5dbf-129">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="e5dbf-130">Fare clic su **Fine**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-130">Click **Finish**</span></span>

<span data-ttu-id="e5dbf-131">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-131">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="e5dbf-132">La procedura guidata consente di effettuare le azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="e5dbf-132">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="e5dbf-133">Genera il file EnumTestModel. edmx che definisce il modello concettuale, il modello di archiviazione e il mapping tra di essi.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-133">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="e5dbf-134">Imposta la proprietà di elaborazione degli artefatti dei metadati del file con estensione edmx da incorporare nell'assembly di output in modo che i file di metadati generati vengano incorporati nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-134">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="e5dbf-135">Aggiunge un riferimento agli assembly seguenti: EntityFramework, System. ComponentModel. DataAnnotations e System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-135">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="e5dbf-136">Crea i file EnumTestModel.tt e EnumTestModel.Context.tt e li aggiunge al file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-136">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="e5dbf-137">Questi file di modello T4 generano il codice che definisce il tipo derivato DbContext e i tipi POCO mappati alle entità nel modello con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-137">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="e5dbf-138">Aggiungere un nuovo tipo di entità</span><span class="sxs-lookup"><span data-stu-id="e5dbf-138">Add a New Entity Type</span></span>

1.  <span data-ttu-id="e5dbf-139">Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi &gt; entità**. verrà visualizzata la finestra di dialogo nuova entità.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-139">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="e5dbf-140">Specificare **Department** per il nome del tipo e specificare **DepartmentID** per il nome della proprietà chiave, lasciare il tipo come **Int32**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-140">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="e5dbf-141">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-141">Click **OK**</span></span>
4.  <span data-ttu-id="e5dbf-142">Fare clic con il pulsante destro del mouse sull'entità e scegliere **Aggiungi nuova- &gt; scala proprietà**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-142">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="e5dbf-143">Rinominare la nuova proprietà in **nome**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-143">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="e5dbf-144">Modificare il tipo della nuova proprietà in **Int32** (per impostazione predefinita, la nuova proprietà è di tipo stringa) per modificare il tipo, aprire il finestra Proprietà e modificare la proprietà Type in **Int32** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-144">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="e5dbf-145">Aggiungere un'altra proprietà scalare e rinominarla in **budget**, modificare il tipo in **Decimal**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-145">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="e5dbf-146">Aggiungere un tipo enum</span><span class="sxs-lookup"><span data-stu-id="e5dbf-146">Add an Enum Type</span></span>

1.  <span data-ttu-id="e5dbf-147">Nella Entity Framework Designer fare clic con il pulsante destro del mouse sulla proprietà nome, quindi scegliere **Converti in enum**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-147">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![Converti in enum](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="e5dbf-149">Nella finestra di dialogo **Aggiungi enum** digitare **DepartmentNames** per il nome del tipo enum, modificare il tipo sottostante in **Int32**, quindi aggiungere i membri seguenti al tipo: English, Math ed Economics</span><span class="sxs-lookup"><span data-stu-id="e5dbf-149">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![Aggiungi tipo enum](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="e5dbf-151">Premi **OK**</span><span class="sxs-lookup"><span data-stu-id="e5dbf-151">Press **OK**</span></span>
4.  <span data-ttu-id="e5dbf-152">Salvare il modello e compilare il progetto</span><span class="sxs-lookup"><span data-stu-id="e5dbf-152">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="e5dbf-153">Quando si compila, i messaggi di avviso sulle entità e sulle associazioni non mappate possono essere visualizzati nel Elenco errori.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-153">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="e5dbf-154">È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-154">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="e5dbf-155">Se si esaminano le Finestra Proprietà, si noterà che il tipo della proprietà Name è stato modificato in **DepartmentNames** e che il tipo enum appena aggiunto è stato aggiunto all'elenco di tipi.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-155">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="e5dbf-156">Se si passa alla finestra browser modello, si noterà che il tipo è stato aggiunto anche al nodo tipi enum.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-156">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![Browser modello](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="e5dbf-158">È anche possibile aggiungere nuovi tipi enum da questa finestra facendo clic con il pulsante destro del mouse e selezionando **Aggiungi tipo enum**.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-158">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="e5dbf-159">Una volta creato, il tipo verrà visualizzato nell'elenco dei tipi e sarà possibile associarlo a una proprietà</span><span class="sxs-lookup"><span data-stu-id="e5dbf-159">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="e5dbf-160">Genera database dal modello</span><span class="sxs-lookup"><span data-stu-id="e5dbf-160">Generate Database from Model</span></span>

<span data-ttu-id="e5dbf-161">A questo punto è possibile generare un database basato sul modello.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-161">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="e5dbf-162">Fare clic con il pulsante destro del mouse su uno spazio vuoto nell'area di Entity Designer e selezionare **genera database da modello** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-162">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="e5dbf-163">Viene visualizzata la finestra di dialogo scegliere la connessione dati della procedura guidata genera database. fare clic sul pulsante **nuova connessione** specificare (local DB **) \\ mssqllocaldb** per il nome del server e **EnumTest** per il database e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-163">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="e5dbf-164">Viene visualizzata una finestra di dialogo in cui viene chiesto se si desidera creare un nuovo database, quindi fare clic su **Sì**.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-164">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="e5dbf-165">Fare clic su **Avanti** . la procedura guidata Crea database genera Data Definition Language (DDL) per la creazione di un database. la DDL generata viene visualizzata nella finestra di dialogo Riepilogo e impostazioni nota che il DDL non contiene una definizione per una tabella che esegue il mapping al tipo di enumerazione</span><span class="sxs-lookup"><span data-stu-id="e5dbf-165">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="e5dbf-166">Fare **clic su fine per** non eseguire lo script DDL.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-166">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="e5dbf-167">La procedura guidata Crea database esegue le operazioni seguenti: apre **EnumTest. edmx. SQL** nell'editor T-SQL genera le sezioni schema e mapping dell'archivio del file edmx aggiunge le informazioni sulla stringa di connessione al file di App.config</span><span class="sxs-lookup"><span data-stu-id="e5dbf-167">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="e5dbf-168">Fare clic con il pulsante destro del mouse nell'editor T-SQL e selezionare **Esegui** la finestra di dialogo Connetti al server, immettere le informazioni di connessione nel passaggio 2 e fare clic su **Connetti** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-168">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="e5dbf-169">Per visualizzare lo schema generato, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna** .</span><span class="sxs-lookup"><span data-stu-id="e5dbf-169">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="e5dbf-170">Mantieni e recupera dati</span><span class="sxs-lookup"><span data-stu-id="e5dbf-170">Persist and Retrieve Data</span></span>

<span data-ttu-id="e5dbf-171">Aprire il file Program.cs in cui è definito il metodo Main.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-171">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="e5dbf-172">Aggiungere il codice seguente nella funzione Main.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-172">Add the following code into the Main function.</span></span> <span data-ttu-id="e5dbf-173">Il codice aggiunge un nuovo oggetto Department al contesto.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-173">The code adds a new Department object to the context.</span></span> <span data-ttu-id="e5dbf-174">Quindi Salva i dati.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-174">It then saves the data.</span></span> <span data-ttu-id="e5dbf-175">Il codice esegue anche una query LINQ che restituisce un reparto in cui il nome è DepartmentNames. English.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-175">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="e5dbf-176">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-176">Compile and run the application.</span></span> <span data-ttu-id="e5dbf-177">Il programma produce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="e5dbf-177">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="e5dbf-178">Per visualizzare i dati nel database, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna**.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-178">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="e5dbf-179">Quindi, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati**.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-179">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="e5dbf-180">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="e5dbf-180">Summary</span></span>

<span data-ttu-id="e5dbf-181">In questa procedura dettagliata è stato illustrato come eseguire il mapping dei tipi enum usando il Entity Framework Designer e come usare le enumerazioni nel codice.</span><span class="sxs-lookup"><span data-stu-id="e5dbf-181">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
