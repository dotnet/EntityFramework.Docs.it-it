---
title: Funzioni con valori di tabella (funzioni con valori)-EF6
description: Funzioni con valori di tabella (funzioni con valori) in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 727c6c7ff5947e78bf8eb489194e355e2fd157c9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070161"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="cd8c5-103">Funzioni con valori di tabella (funzioni con valori)</span><span class="sxs-lookup"><span data-stu-id="cd8c5-103">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="cd8c5-104">**EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="cd8c5-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="cd8c5-106">Il video e la procedura dettagliata illustrano come eseguire il mapping di funzioni con valori di tabella (funzioni con valori) usando il Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-106">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="cd8c5-107">Viene inoltre illustrato come chiamare un TVF da una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-107">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="cd8c5-108">Funzioni con valori sono attualmente supportati solo nel flusso di lavoro Database First.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-108">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="cd8c5-109">Il supporto di TVF è stato introdotto in Entity Framework versione 5.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-109">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="cd8c5-110">Si noti che per utilizzare le nuove funzionalità, ad esempio le funzioni con valori di tabella, le enumerazioni e i tipi spaziali, è necessario definire come destinazione .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-110">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="cd8c5-111">Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="cd8c5-112">Funzioni con valori sono molto simili alle stored procedure con una differenza fondamentale: il risultato di un TVF è componibile.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-112">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="cd8c5-113">Ciò significa che i risultati di un TVF possono essere utilizzati in una query LINQ mentre i risultati di un stored procedure non possono.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-113">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="cd8c5-114">Video</span><span class="sxs-lookup"><span data-stu-id="cd8c5-114">Watch the video</span></span>

<span data-ttu-id="cd8c5-115">**Presentato da**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="cd8c5-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="cd8c5-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  |  [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="cd8c5-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="cd8c5-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="cd8c5-117">Pre-Requisites</span></span>

<span data-ttu-id="cd8c5-118">Per completare questa procedura dettagliata, è necessario:</span><span class="sxs-lookup"><span data-stu-id="cd8c5-118">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="cd8c5-119">Installare il [database School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="cd8c5-119">Install the [School database](xref:ef6/resources/school-database).</span></span>

- <span data-ttu-id="cd8c5-120">Avere una versione recente di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd8c5-120">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="cd8c5-121">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="cd8c5-121">Set up the Project</span></span>

1.  <span data-ttu-id="cd8c5-122">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cd8c5-122">Open Visual Studio</span></span>
2.  <span data-ttu-id="cd8c5-123">Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-123">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="cd8c5-124">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-124">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="cd8c5-125">Immettere **TVF** come nome del progetto e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-125">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="cd8c5-126">Aggiungere un TVF al database</span><span class="sxs-lookup"><span data-stu-id="cd8c5-126">Add a TVF to the Database</span></span>

-   <span data-ttu-id="cd8c5-127">Selezionare **Visualizza- &gt; Esplora oggetti di SQL Server**</span><span class="sxs-lookup"><span data-stu-id="cd8c5-127">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="cd8c5-128">Se il database locale non è presente nell'elenco dei server: fare clic con il pulsante destro del mouse su **SQL Server** e scegliere **Aggiungi SQL Server** utilizzare l' **autenticazione di Windows** predefinita per la connessione al server del database locale.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-128">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="cd8c5-129">Espandere il nodo del database locale</span><span class="sxs-lookup"><span data-stu-id="cd8c5-129">Expand the LocalDB node</span></span>
-   <span data-ttu-id="cd8c5-130">Nel nodo database fare clic con il pulsante destro del mouse sul nodo School database e scegliere **nuova query...**</span><span class="sxs-lookup"><span data-stu-id="cd8c5-130">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="cd8c5-131">Nell'editor T-SQL incollare la definizione TVF seguente</span><span class="sxs-lookup"><span data-stu-id="cd8c5-131">In T-SQL Editor, paste the following TVF definition</span></span>

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   <span data-ttu-id="cd8c5-132">Fare clic con il pulsante destro del mouse sull'editor T-SQL e selezionare **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-132">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="cd8c5-133">La funzione GetStudentGradesForCourse viene aggiunta al database School</span><span class="sxs-lookup"><span data-stu-id="cd8c5-133">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="cd8c5-134">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="cd8c5-134">Create a Model</span></span>

1.  <span data-ttu-id="cd8c5-135">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-135">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="cd8c5-136">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro **modelli** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-136">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="cd8c5-137">Immettere **TVFModel. edmx** per il nome del file e quindi fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-137">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="cd8c5-138">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="cd8c5-138">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="cd8c5-139">Fare clic su **nuova connessione** immettere (local DB **) \\ mssqllocaldb** nella casella di testo nome server immettere **School**   per il nome del database, quindi fare clic su **OK**</span><span class="sxs-lookup"><span data-stu-id="cd8c5-139">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="cd8c5-140">Nella finestra di dialogo Scegli oggetti di database, sotto il nodo **tabelle**   , selezionare le **tabelle Person**, **StudentGrade**e **Course**  </span><span class="sxs-lookup"><span data-stu-id="cd8c5-140">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="cd8c5-141">Selezionare la funzione **GetStudentGradesForCourse** situata sotto la nota relativa al nodo **stored procedure e funzioni**   , che a partire da Visual Studio 2012, il Entity Designer consente di importare in batch le stored procedure e le funzioni</span><span class="sxs-lookup"><span data-stu-id="cd8c5-141">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="cd8c5-142">Fare clic su **fine**</span><span class="sxs-lookup"><span data-stu-id="cd8c5-142">Click **Finish**</span></span>
9.  <span data-ttu-id="cd8c5-143">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-143">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="cd8c5-144">Tutti gli oggetti selezionati nella finestra di dialogo **Scegli oggetti di database**   vengono aggiunti al modello.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-144">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="cd8c5-145">Per impostazione predefinita, la forma risultante di ogni stored procedure o funzione importata diventerà automaticamente un nuovo tipo complesso nel modello di entità.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-145">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="cd8c5-146">Tuttavia, si desidera eseguire il mapping dei risultati della funzione GetStudentGradesForCourse all'entità StudentGrade: fare clic con il pulsante destro del mouse sull'area di progettazione e selezionare **browser modello** in browser modello, selezionare **importazioni di funzioni**, quindi fare doppio clic sulla funzione **GetStudentGradesForCourse** nella finestra di dialogo modifica importazione funzione, selezionare **entità**   e scegliere **StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="cd8c5-146">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="cd8c5-147">Mantieni e recupera dati</span><span class="sxs-lookup"><span data-stu-id="cd8c5-147">Persist and Retrieve Data</span></span>

<span data-ttu-id="cd8c5-148">Aprire il file in cui è definito il metodo Main.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-148">Open the file where the Main method is defined.</span></span> <span data-ttu-id="cd8c5-149">Aggiungere il codice seguente nella funzione Main.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-149">Add the following code into the Main function.</span></span>

<span data-ttu-id="cd8c5-150">Nel codice seguente viene illustrato come compilare una query che utilizza una funzione con valori di tabella.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-150">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="cd8c5-151">La query proietta i risultati in un tipo anonimo che contiene il titolo del corso correlato e gli studenti correlati con un grado maggiore o uguale a 3,5.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-151">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

<span data-ttu-id="cd8c5-152">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-152">Compile and run the application.</span></span> <span data-ttu-id="cd8c5-153">Il programma produce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="cd8c5-153">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="cd8c5-154">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="cd8c5-154">Summary</span></span>

<span data-ttu-id="cd8c5-155">In questa procedura dettagliata è stato illustrato come eseguire il mapping di funzioni con valori di tabella (funzioni con valori) utilizzando il Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-155">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="cd8c5-156">È stato inoltre illustrato come chiamare un TVF da una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="cd8c5-156">It also demonstrated how to call a TVF from a LINQ query.</span></span>
