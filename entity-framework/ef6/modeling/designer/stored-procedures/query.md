---
title: Stored procedure per query della finestra di progettazione-EF6
description: Stored procedure di query della finestra di progettazione in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: d88bf70e232f5245023ab6683290503822d07463
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066018"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="c143b-103">Stored procedure di query della finestra di progettazione</span><span class="sxs-lookup"><span data-stu-id="c143b-103">Designer Query Stored Procedures</span></span>
<span data-ttu-id="c143b-104">In questa procedura dettagliata viene illustrato come utilizzare il Entity Framework Designer (Entity Designer) per importare stored procedure in un modello e quindi chiamare le stored procedure importate per recuperare i risultati.</span><span class="sxs-lookup"><span data-stu-id="c143b-104">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="c143b-105">Si noti che Code First non supporta il mapping a stored procedure o funzioni.</span><span class="sxs-lookup"><span data-stu-id="c143b-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="c143b-106">Tuttavia, è possibile chiamare stored procedure o funzioni utilizzando il metodo System. Data. Entity. DbSet. sqlQuery.</span><span class="sxs-lookup"><span data-stu-id="c143b-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="c143b-107">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="c143b-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="c143b-108">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="c143b-108">Prerequisites</span></span>

<span data-ttu-id="c143b-109">Per completare questa procedura dettagliata, è necessario disporre di:</span><span class="sxs-lookup"><span data-stu-id="c143b-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="c143b-110">Una versione recente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c143b-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="c143b-111">[Database di esempio School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="c143b-111">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="c143b-112">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="c143b-112">Set up the Project</span></span>

-   <span data-ttu-id="c143b-113">Aprire Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="c143b-113">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="c143b-114">Selezionare **file- &gt; nuovo- &gt; progetto**</span><span class="sxs-lookup"><span data-stu-id="c143b-114">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="c143b-115">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="c143b-115">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="c143b-116">Immettere **EFwithSProcsSample**   come nome.</span><span class="sxs-lookup"><span data-stu-id="c143b-116">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="c143b-117">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="c143b-117">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="c143b-118">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="c143b-118">Create a Model</span></span>

-   <span data-ttu-id="c143b-119">Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="c143b-119">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="c143b-120">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="c143b-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="c143b-121">Immettere **EFwithSProcsModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="c143b-121">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="c143b-122">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="c143b-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="c143b-123">Fare clic su **nuova connessione**.</span><span class="sxs-lookup"><span data-stu-id="c143b-123">Click **New Connection**.</span></span>  
    <span data-ttu-id="c143b-124">Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c143b-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="c143b-125">La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="c143b-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="c143b-126">Nella finestra di dialogo Scegli oggetti di database selezionare la casella di controllo **tabelle**   per selezionare tutte le tabelle.</span><span class="sxs-lookup"><span data-stu-id="c143b-126">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="c143b-127">Inoltre, selezionare le stored procedure seguenti nel nodo **stored procedure e funzioni** : **GetStudentGrades** e **GetDepartmentName**.</span><span class="sxs-lookup"><span data-stu-id="c143b-127">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![Importazione di stored procedure](~/ef6/media/import.jpg)

    <span data-ttu-id="c143b-129">*A partire da Visual Studio 2012, EF Designer supporta l'importazione bulk di stored procedure. Per impostazione predefinita, l' **importazione delle stored procedure e delle funzioni selezionate nel modello set** è selezionata.*</span><span class="sxs-lookup"><span data-stu-id="c143b-129">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="c143b-130">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="c143b-130">Click **Finish**.</span></span>

<span data-ttu-id="c143b-131">Per impostazione predefinita, la forma risultante di ogni stored procedure o funzione importata che restituisce più colonne diventerà automaticamente un nuovo tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="c143b-131">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="c143b-132">In questo esempio si vuole eseguire il mapping dei risultati della funzione **GetStudentGrades** all'entità **StudentGrade** e i risultati di **GetDepartmentName** su **None** (**nessuno** è il valore predefinito).</span><span class="sxs-lookup"><span data-stu-id="c143b-132">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="c143b-133">Affinché un'importazione di funzioni restituisca un tipo di entità, le colonne restituite dalla stored procedure corrispondente devono corrispondere esattamente alle proprietà scalari del tipo di entità restituito.</span><span class="sxs-lookup"><span data-stu-id="c143b-133">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="c143b-134">Un'importazione di funzioni può inoltre restituire raccolte di tipi semplici, tipi complessi o nessun valore.</span><span class="sxs-lookup"><span data-stu-id="c143b-134">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="c143b-135">Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **browser modello**.</span><span class="sxs-lookup"><span data-stu-id="c143b-135">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="c143b-136">In **browser modello**selezionare **importazioni di funzioni**, quindi fare doppio clic sulla funzione **GetStudentGrades** .</span><span class="sxs-lookup"><span data-stu-id="c143b-136">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="c143b-137">Nella finestra di dialogo modifica importazione funzione selezionare **entità**   e scegliere **StudentGrade**.</span><span class="sxs-lookup"><span data-stu-id="c143b-137">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="c143b-138">*La casella di controllo **importazione funzioni è componibile** nella parte superiore della finestra di dialogo **importazioni** di funzioni che consente di eseguire il mapping a funzioni componibili. Se si seleziona questa casella, solo le funzioni componibili (funzioni con valori di tabella) verranno visualizzate nell'elenco a discesa **stored procedure/nome funzione** . Se non si seleziona questa casella, nell'elenco verranno visualizzate solo le funzioni non componibili.*</span><span class="sxs-lookup"><span data-stu-id="c143b-138">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="c143b-139">Usare il modello</span><span class="sxs-lookup"><span data-stu-id="c143b-139">Use the Model</span></span>

<span data-ttu-id="c143b-140">Aprire il file **Program.cs** in cui è definito il metodo **Main** .</span><span class="sxs-lookup"><span data-stu-id="c143b-140">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="c143b-141">Aggiungere il codice seguente nella funzione Main.</span><span class="sxs-lookup"><span data-stu-id="c143b-141">Add the following code into the Main function.</span></span>

<span data-ttu-id="c143b-142">Il codice chiama due stored procedure: **GetStudentGrades** (restituisce **StudentGrades** per il *StudentID*specificato) e **GetDepartmentName** (restituisce il nome del reparto nel parametro di output).</span><span class="sxs-lookup"><span data-stu-id="c143b-142">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

<span data-ttu-id="c143b-143">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="c143b-143">Compile and run the application.</span></span> <span data-ttu-id="c143b-144">Il programma produce l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="c143b-144">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="c143b-145">Parametri di output</span><span class="sxs-lookup"><span data-stu-id="c143b-145">Output Parameters</span></span>
-----------------

<span data-ttu-id="c143b-146">Se vengono utilizzati parametri di output, i relativi valori non saranno disponibili fino a quando i risultati non sono stati letti completamente.</span><span class="sxs-lookup"><span data-stu-id="c143b-146">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="c143b-147">A causa del comportamento sottostante di DbDataReader, vedere Recupero di [dati tramite un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) per altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="c143b-147">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
