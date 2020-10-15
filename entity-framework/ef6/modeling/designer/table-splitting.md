---
title: Suddivisione di tabelle della finestra di progettazione-EF6
description: Suddivisione di tabelle della finestra di progettazione in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 8f6db22913fb3ac8a4254c29b5f986eb6e14f18e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066044"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="34f54-103">Suddivisione di tabelle della finestra di progettazione</span><span class="sxs-lookup"><span data-stu-id="34f54-103">Designer Table Splitting</span></span>
<span data-ttu-id="34f54-104">In questa procedura dettagliata viene illustrato come eseguire il mapping di più tipi di entità a una singola tabella modificando un modello con il Entity Framework Designer (EF designer).</span><span class="sxs-lookup"><span data-stu-id="34f54-104">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="34f54-105">Uno dei motivi per cui è consigliabile utilizzare la suddivisione delle tabelle è ritardare il caricamento di alcune proprietà quando si utilizza il caricamento lazy per caricare gli oggetti.</span><span class="sxs-lookup"><span data-stu-id="34f54-105">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="34f54-106">È possibile separare le proprietà che potrebbero contenere quantità molto elevate di dati in un'entità distinta e caricarla solo quando necessario.</span><span class="sxs-lookup"><span data-stu-id="34f54-106"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="34f54-107">Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="34f54-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="34f54-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="34f54-109">Prerequisites</span></span>

<span data-ttu-id="34f54-110">Per completare questa procedura dettagliata, è necessario disporre di:</span><span class="sxs-lookup"><span data-stu-id="34f54-110">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="34f54-111">Una versione recente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="34f54-111">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="34f54-112">[Database di esempio School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="34f54-112">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="34f54-113">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="34f54-113">Set up the Project</span></span>

<span data-ttu-id="34f54-114">Questa procedura dettagliata usa Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="34f54-114">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="34f54-115">Aprire Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="34f54-115">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="34f54-116">Scegliere **Nuovo** dal menu **File**e quindi fare clic su **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="34f54-116">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="34f54-117">Nel riquadro sinistro fare clic su Visual C \# e quindi selezionare il modello applicazione console.</span><span class="sxs-lookup"><span data-stu-id="34f54-117">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="34f54-118">Immettere **TableSplittingSample** come nome del progetto e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="34f54-118">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="34f54-119">Creare un modello basato sul database School</span><span class="sxs-lookup"><span data-stu-id="34f54-119">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="34f54-120">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="34f54-120">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="34f54-121">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="34f54-121">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="34f54-122">Immettere **TableSplittingModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="34f54-122">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="34f54-123">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti.**</span><span class="sxs-lookup"><span data-stu-id="34f54-123">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="34f54-124">Fare clic su nuova connessione.</span><span class="sxs-lookup"><span data-stu-id="34f54-124">Click New Connection.</span></span> <span data-ttu-id="34f54-125">Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="34f54-125">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="34f54-126">La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="34f54-126">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="34f54-127">Nella finestra di dialogo Seleziona oggetti di database espandere il nodo **tabelle**   e controllare la tabella **Person** .</span><span class="sxs-lookup"><span data-stu-id="34f54-127">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="34f54-128">La tabella specificata verrà aggiunta al modello **School** .</span><span class="sxs-lookup"><span data-stu-id="34f54-128">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="34f54-129">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="34f54-129">Click **Finish**.</span></span>

<span data-ttu-id="34f54-130">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="34f54-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="34f54-131">Tutti gli oggetti selezionati nella finestra di dialogo **Scegli oggetti di database**   vengono aggiunti al modello.</span><span class="sxs-lookup"><span data-stu-id="34f54-131">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="34f54-132">Eseguire il mapping di due entità a una singola tabella</span><span class="sxs-lookup"><span data-stu-id="34f54-132">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="34f54-133">In questa sezione si suddividerà l'entità **Person** in due entità, quindi la si eseguirà il mapping a una singola tabella.</span><span class="sxs-lookup"><span data-stu-id="34f54-133">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="34f54-134">L'entità **Person** non contiene proprietà che possono contenere grandi quantità di dati; viene usato solo come esempio.</span><span class="sxs-lookup"><span data-stu-id="34f54-134">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="34f54-135">Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi nuovo**e fare clic su **entità**.</span><span class="sxs-lookup"><span data-stu-id="34f54-135">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="34f54-136">Verrà visualizzata la finestra di dialogo **nuova entità**   .</span><span class="sxs-lookup"><span data-stu-id="34f54-136">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="34f54-137">Digitare **HireInfo**   per il **nome dell'entità** e **PersonID** per il nome della **proprietà della chiave** .</span><span class="sxs-lookup"><span data-stu-id="34f54-137">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="34f54-138">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="34f54-138">Click **OK**.</span></span>
-   <span data-ttu-id="34f54-139">Nell'area di progettazione verrà creato e visualizzato un nuovo tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="34f54-139">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="34f54-140">Selezionare la proprietà **hiree**   del tipo di entità **Person**   e premere i tasti **CTRL + X** .</span><span class="sxs-lookup"><span data-stu-id="34f54-140">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="34f54-141">Selezionare l'entità **HireInfo**   e premere **CTRL + V** .</span><span class="sxs-lookup"><span data-stu-id="34f54-141">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="34f54-142">Creare un'associazione tra **Person** e **HireInfo**.</span><span class="sxs-lookup"><span data-stu-id="34f54-142">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="34f54-143">A tale scopo, fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi nuovo**e fare clic su **associazione**.</span><span class="sxs-lookup"><span data-stu-id="34f54-143">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="34f54-144">Verrà visualizzata la finestra di dialogo **Aggiungi associazione**   .</span><span class="sxs-lookup"><span data-stu-id="34f54-144">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="34f54-145">Per impostazione predefinita, viene fornito il nome **PersonHireInfo** .</span><span class="sxs-lookup"><span data-stu-id="34f54-145">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="34f54-146">Specificare la molteplicità **1 (una)** su entrambe le estremità della relazione.</span><span class="sxs-lookup"><span data-stu-id="34f54-146">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="34f54-147">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="34f54-147">Press **OK**.</span></span>

<span data-ttu-id="34f54-148">Il passaggio successivo richiede la finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="34f54-148">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="34f54-149">Se questa finestra non è visibile, fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Dettagli mapping**.</span><span class="sxs-lookup"><span data-stu-id="34f54-149">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="34f54-150">Selezionare il **HireInfo**   tipo di entità HireInfo e fare clic su \*\* &lt; Aggiungi tabella &gt; o vista\*\*   nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="34f54-150">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="34f54-151">Selezionare **Person** nell'elenco a discesa \*\* &lt; aggiungere una tabella &gt; o un\*\*campo di visualizzazione   .</span><span class="sxs-lookup"><span data-stu-id="34f54-151">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="34f54-152">L'elenco contiene le tabelle o le viste a cui è possibile eseguire il mapping dell'entità selezionata.</span><span class="sxs-lookup"><span data-stu-id="34f54-152">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="34f54-153">Per impostazione predefinita, è necessario eseguire il mapping delle proprietà appropriate.</span><span class="sxs-lookup"><span data-stu-id="34f54-153">The appropriate properties should be mapped by default.</span></span>

    ![Proprietà mapping](~/ef6/media/mapping.png)

-   <span data-ttu-id="34f54-155">Selezionare l'associazione **PersonHireInfo** nell'area di progettazione.</span><span class="sxs-lookup"><span data-stu-id="34f54-155">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="34f54-156">Fare clic con il pulsante destro del mouse sull'associazione nell'area di progettazione e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="34f54-156">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="34f54-157">Nella finestra **Proprietà** selezionare la proprietà **vincoli referenziali** , quindi fare clic sul pulsante con i puntini di sospensione.</span><span class="sxs-lookup"><span data-stu-id="34f54-157">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="34f54-158">Selezionare **Person** dall'elenco a discesa **principale** .</span><span class="sxs-lookup"><span data-stu-id="34f54-158">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="34f54-159">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="34f54-159">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="34f54-160">Usare il modello</span><span class="sxs-lookup"><span data-stu-id="34f54-160">Use the Model</span></span>

-   <span data-ttu-id="34f54-161">Incollare il codice seguente nel metodo Main.</span><span class="sxs-lookup"><span data-stu-id="34f54-161">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="34f54-162">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="34f54-162">Compile and run the application.</span></span>

<span data-ttu-id="34f54-163">Le istruzioni T-SQL seguenti sono state eseguite nel database **School** in seguito all'esecuzione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="34f54-163">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="34f54-164">L' **inserimento** seguente è stato eseguito in seguito all'esecuzione del contesto. SaveChanges () e combina i dati delle entità **Person** e **HireInfo**</span><span class="sxs-lookup"><span data-stu-id="34f54-164">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![Inserisci dati Person e HireInfo di combinazione](~/ef6/media/insert.png)

-   <span data-ttu-id="34f54-166">L'istruzione **Select** seguente è stata eseguita in seguito all'esecuzione del contesto. People. FirstOrDefault () e seleziona solo le colonne di cui è stato eseguito il mapping a **Person**</span><span class="sxs-lookup"><span data-stu-id="34f54-166">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![Selezionare 1](~/ef6/media/select1.png)

-   <span data-ttu-id="34f54-168">La seguente **selezione** è stata eseguita in seguito all'accesso alla proprietà di navigazione ExistingPerson. Instructor e seleziona solo le colonne mappate a **HireInfo**</span><span class="sxs-lookup"><span data-stu-id="34f54-168">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![Selezionare 2](~/ef6/media/select2.png)
