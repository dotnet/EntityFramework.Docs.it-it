---
title: Stored procedure CUD della finestra di progettazione-EF6
description: Stored procedure CUD della finestra di progettazione in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: fae795922980452cf062a62e3cbb47de49b49c74
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073214"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="06df4-103">Stored procedure CUD della finestra di progettazione</span><span class="sxs-lookup"><span data-stu-id="06df4-103">Designer CUD Stored Procedures</span></span>

<span data-ttu-id="06df4-104">Questa procedura dettagliata illustra come eseguire il mapping delle \\ operazioni di creazione di istruzioni INSERT, Update e Delete (CUD) di un tipo di entità alle stored procedure usando il Entity Framework Designer (Entity Framework Designer).</span><span class="sxs-lookup"><span data-stu-id="06df4-104">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="06df4-105">Per impostazione predefinita, il Entity Framework genera automaticamente le istruzioni SQL per le operazioni CUD, ma è anche possibile eseguire il mapping delle stored procedure a queste operazioni.</span><span class="sxs-lookup"><span data-stu-id="06df4-105"> By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="06df4-106">Si noti che Code First non supporta il mapping a stored procedure o funzioni.</span><span class="sxs-lookup"><span data-stu-id="06df4-106">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="06df4-107">Tuttavia, è possibile chiamare stored procedure o funzioni utilizzando il metodo System. Data. Entity. DbSet. sqlQuery.</span><span class="sxs-lookup"><span data-stu-id="06df4-107">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="06df4-108">Esempio:</span><span class="sxs-lookup"><span data-stu-id="06df4-108">For example:</span></span>

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="06df4-109">Considerazioni relative al mapping delle operazioni CUD alle stored procedure</span><span class="sxs-lookup"><span data-stu-id="06df4-109">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="06df4-110">Quando si esegue il mapping delle operazioni CUD alle stored procedure, si applicano le considerazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="06df4-110">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span>

- <span data-ttu-id="06df4-111">Se si esegue il mapping di una delle operazioni CUD a una stored procedure, eseguirne il mapping.</span><span class="sxs-lookup"><span data-stu-id="06df4-111">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="06df4-112">Se non si esegue il mapping di tutti e tre, le operazioni non mappate avranno esito negativo se eseguite e verrà generata un'eccezione **UpdateException**   .</span><span class="sxs-lookup"><span data-stu-id="06df4-112">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
- <span data-ttu-id="06df4-113">È necessario eseguire il mapping di ogni parametro del stored procedure alle proprietà dell'entità.</span><span class="sxs-lookup"><span data-stu-id="06df4-113">You must map every parameter of the stored procedure to entity properties.</span></span>
- <span data-ttu-id="06df4-114">Se il server genera il valore della chiave primaria per la riga inserita, è necessario eseguire il mapping di questo valore alla proprietà della chiave dell'entità.</span><span class="sxs-lookup"><span data-stu-id="06df4-114">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="06df4-115">Nell'esempio seguente il **InsertPerson**   stored procedure restituisce la chiave primaria appena creata come parte del set di risultati della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="06df4-115">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="06df4-116">Viene eseguito il mapping della chiave primaria alla chiave di entità (**PersonID**) usando la funzionalità di \*\* &lt; aggiunta dei binding &gt; di risultato\*\*   di EF designer.</span><span class="sxs-lookup"><span data-stu-id="06df4-116">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
- <span data-ttu-id="06df4-117">Viene eseguito il mapping delle chiamate stored procedure 1:1 con le entità del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="06df4-117">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="06df4-118">Se, ad esempio, si implementa una gerarchia di ereditarietà nel modello concettuale ed è quindi necessario eseguire il mapping delle stored procedure CUD per le entità **padre** (base) e **figlio** (derivata), il salvataggio delle modifiche **figlio** chiamerà solo le stored procedure del **figlio**, non attiverà le chiamate alle stored procedure del **padre**.</span><span class="sxs-lookup"><span data-stu-id="06df4-118">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06df4-119">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="06df4-119">Prerequisites</span></span>

<span data-ttu-id="06df4-120">Per completare questa procedura dettagliata, è necessario disporre di:</span><span class="sxs-lookup"><span data-stu-id="06df4-120">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="06df4-121">Una versione recente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="06df4-121">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="06df4-122">[Database di esempio School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="06df4-122">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="06df4-123">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="06df4-123">Set up the Project</span></span>

- <span data-ttu-id="06df4-124">Aprire Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="06df4-124">Open Visual Studio 2012.</span></span>
- <span data-ttu-id="06df4-125">Selezionare **file- &gt; nuovo- &gt; progetto**</span><span class="sxs-lookup"><span data-stu-id="06df4-125">Select **File-&gt; New -&gt; Project**</span></span>
- <span data-ttu-id="06df4-126">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="06df4-126">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
- <span data-ttu-id="06df4-127">Immettere **CUDSProcsSample**   come nome.</span><span class="sxs-lookup"><span data-stu-id="06df4-127">Enter **CUDSProcsSample** as the name.</span></span>
- <span data-ttu-id="06df4-128">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="06df4-128">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="06df4-129">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="06df4-129">Create a Model</span></span>

- <span data-ttu-id="06df4-130">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="06df4-130">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
- <span data-ttu-id="06df4-131">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="06df4-131">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
- <span data-ttu-id="06df4-132">Immettere **CUDSProcs. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="06df4-132">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
- <span data-ttu-id="06df4-133">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="06df4-133">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
- <span data-ttu-id="06df4-134">Fare clic su **nuova connessione**.</span><span class="sxs-lookup"><span data-stu-id="06df4-134">Click **New Connection**.</span></span> <span data-ttu-id="06df4-135">Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="06df4-135">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="06df4-136">La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="06df4-136">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
- <span data-ttu-id="06df4-137">Nella finestra di dialogo Scegli oggetti di database, nel nodo **tabelle**   , selezionare la tabella **Person** .</span><span class="sxs-lookup"><span data-stu-id="06df4-137">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
- <span data-ttu-id="06df4-138">Inoltre, selezionare le stored procedure seguenti nel nodo **stored procedure e funzioni** : **DeletePerson**, **InsertPerson**e **UpdatePerson**.</span><span class="sxs-lookup"><span data-stu-id="06df4-138">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span>
- <span data-ttu-id="06df4-139">A partire da Visual Studio 2012, EF Designer supporta l'importazione bulk di stored procedure.</span><span class="sxs-lookup"><span data-stu-id="06df4-139">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="06df4-140">Per impostazione predefinita, l' **importazione delle stored procedure e delle funzioni selezionate nel modello di entità** è selezionata.</span><span class="sxs-lookup"><span data-stu-id="06df4-140">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="06df4-141">Poiché in questo esempio sono presenti stored procedure che inseriscono, aggiornano ed eliminano i tipi di entità, non è necessario importarli e deselezionare questa casella di controllo.</span><span class="sxs-lookup"><span data-stu-id="06df4-141">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span>

    ![Procedure Import S](~/ef6/media/importsprocs.jpg)

- <span data-ttu-id="06df4-143">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="06df4-143">Click **Finish**.</span></span>
    <span data-ttu-id="06df4-144">Viene visualizzata la finestra di progettazione EF, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="06df4-144">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="06df4-145">Eseguire il mapping dell'entità Person alle stored procedure</span><span class="sxs-lookup"><span data-stu-id="06df4-145">Map the Person Entity to Stored Procedures</span></span>

- <span data-ttu-id="06df4-146">Fare clic con il **Person**pulsante destro del mouse sul   tipo di entità Person e selezionare **stored procedure mapping**.</span><span class="sxs-lookup"><span data-stu-id="06df4-146">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
- <span data-ttu-id="06df4-147">I mapping di stored procedure vengono visualizzati nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="06df4-147">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
- <span data-ttu-id="06df4-148">Fare clic su \*\* &lt; Seleziona &gt; Inserisci funzione\*\*.</span><span class="sxs-lookup"><span data-stu-id="06df4-148">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="06df4-149">Questo campo diventa l'elenco a discesa delle stored procedure contenute nel modello di archiviazione che possono essere mappate ai tipi di entità del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="06df4-149">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="06df4-150">Selezionare **InsertPerson**   nell'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="06df4-150">Select **InsertPerson** from the drop-down list.</span></span>
- <span data-ttu-id="06df4-151">Vengono visualizzati i mapping predefiniti tra i parametri delle stored procedure e le proprietà dell'entità.</span><span class="sxs-lookup"><span data-stu-id="06df4-151">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="06df4-152">Come indicato dalle frecce, che mostrano la direzione del mapping, per i parametri delle stored procedure vengono specificati i valori delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="06df4-152">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
- <span data-ttu-id="06df4-153">Fare clic su \*\* &lt; Aggiungi &gt; associazione di risultati\*\*.</span><span class="sxs-lookup"><span data-stu-id="06df4-153">Click **&lt;Add Result Binding&gt;**.</span></span>
- <span data-ttu-id="06df4-154">Digitare **NewPersonID**, il nome del parametro restituito dal stored procedure **InsertPerson**   .</span><span class="sxs-lookup"><span data-stu-id="06df4-154">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="06df4-155">Assicurarsi di non digitare spazi iniziali o finali.</span><span class="sxs-lookup"><span data-stu-id="06df4-155">Make sure not to type leading or trailing spaces.</span></span>
- <span data-ttu-id="06df4-156">Premere **invio**.</span><span class="sxs-lookup"><span data-stu-id="06df4-156">Press **Enter**.</span></span>
- <span data-ttu-id="06df4-157">Per impostazione predefinita **NewPersonID**,   viene eseguito il mapping di NewPersonID alla chiave di entità **PersonID**.</span><span class="sxs-lookup"><span data-stu-id="06df4-157">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="06df4-158">Come indicato dalle frecce, che mostrano la direzione del mapping, per la proprietà viene specificato il valore della colonna dei risultati.</span><span class="sxs-lookup"><span data-stu-id="06df4-158">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![Dettagli mapping](~/ef6/media/mappingdetails.png)

- <span data-ttu-id="06df4-160">Fare clic su \*\* &lt; Seleziona &gt; funzione di aggiornamento\*\*   e selezionare **UpdatePerson**   nell'elenco a discesa risultante.</span><span class="sxs-lookup"><span data-stu-id="06df4-160">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="06df4-161">Vengono visualizzati i mapping predefiniti tra i parametri delle stored procedure e le proprietà dell'entità.</span><span class="sxs-lookup"><span data-stu-id="06df4-161">Default mappings between stored procedure parameters and entity properties appear.</span></span>
- <span data-ttu-id="06df4-162">Fare clic su \*\* &lt; Seleziona &gt; funzione di eliminazione\*\*   e selezionare **DeletePerson**   nell'elenco a discesa risultante.</span><span class="sxs-lookup"><span data-stu-id="06df4-162">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
- <span data-ttu-id="06df4-163">Vengono visualizzati i mapping predefiniti tra i parametri delle stored procedure e le proprietà dell'entità.</span><span class="sxs-lookup"><span data-stu-id="06df4-163">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="06df4-164">Le operazioni di inserimento, aggiornamento ed eliminazione del tipo di entità **Person**   sono ora mappate alle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="06df4-164">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="06df4-165">Per abilitare il controllo della concorrenza durante l'aggiornamento o l'eliminazione di un'entità con stored procedure, utilizzare una delle opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="06df4-165">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

- <span data-ttu-id="06df4-166">Utilizzare un parametro di **output** per restituire il numero di righe interessate dalla stored procedure e selezionare la casella di controllo **righe interessate parametro**   accanto al nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="06df4-166">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="06df4-167">Se il valore restituito è zero quando viene chiamata l'operazione,  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)   verrà generata un'eccezione OptimisticConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="06df4-167">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
- <span data-ttu-id="06df4-168">Selezionare la casella di controllo **Usa valore originale** accanto a una proprietà che si desidera utilizzare per il controllo della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="06df4-168">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="06df4-169">Quando si tenta di eseguire un aggiornamento, il valore della proprietà originariamente letta dal database verrà utilizzato per la scrittura di dati nel database.</span><span class="sxs-lookup"><span data-stu-id="06df4-169">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="06df4-170">Se il valore non corrisponde al valore nel database, **OptimisticConcurrencyException**   verrà generata un'eccezione OptimisticConcurrencyException.</span><span class="sxs-lookup"><span data-stu-id="06df4-170">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="06df4-171">Usare il modello</span><span class="sxs-lookup"><span data-stu-id="06df4-171">Use the Model</span></span>

<span data-ttu-id="06df4-172">Aprire il file **Program.cs** in cui è definito il metodo **Main** .</span><span class="sxs-lookup"><span data-stu-id="06df4-172">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="06df4-173">Aggiungere il codice seguente nella funzione Main.</span><span class="sxs-lookup"><span data-stu-id="06df4-173">Add the following code into the Main function.</span></span>

<span data-ttu-id="06df4-174">Il codice crea un nuovo oggetto **Person** , quindi aggiorna l'oggetto e infine elimina l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="06df4-174">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- <span data-ttu-id="06df4-175">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="06df4-175">Compile and run the application.</span></span> <span data-ttu-id="06df4-176">Il programma produce l'output seguente \*</span><span class="sxs-lookup"><span data-stu-id="06df4-176">The program produces the following output \*</span></span>

> [!NOTE]
> <span data-ttu-id="06df4-177">PersonID viene generato automaticamente dal server, pertanto è probabile che venga visualizzato un numero diverso \*</span><span class="sxs-lookup"><span data-stu-id="06df4-177">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="06df4-178">Se si utilizza la versione finale di Visual Studio, è possibile utilizzare IntelliTrace con il debugger per visualizzare le istruzioni SQL che vengono eseguite.</span><span class="sxs-lookup"><span data-stu-id="06df4-178">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![Eseguire il debug con IntelliTrace](~/ef6/media/intellitrace.png)
