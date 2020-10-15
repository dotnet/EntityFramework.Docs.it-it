---
title: Ereditarietà TPH della finestra di progettazione-EF6
description: Ereditarietà TPH della finestra di progettazione in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: bf689e02a76e46e5eee5a64cb9825e667c8cd65c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064848"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="fca07-103">Ereditarietà TPH della finestra di progettazione</span><span class="sxs-lookup"><span data-stu-id="fca07-103">Designer TPH Inheritance</span></span>
<span data-ttu-id="fca07-104">Questa procedura dettagliata illustra come implementare l'ereditarietà tabella per gerarchia (TPH) nel modello concettuale con il Entity Framework Designer (EF designer).</span><span class="sxs-lookup"><span data-stu-id="fca07-104">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="fca07-105">L'ereditarietà TPH utilizza una tabella di database per gestire i dati per tutti i tipi di entità in una gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="fca07-105">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="fca07-106">In questa procedura dettagliata si eseguirà il mapping della tabella Person a tre tipi di entità: Person (il tipo di base), Student (deriva da Person) e Instructor (deriva da Person).</span><span class="sxs-lookup"><span data-stu-id="fca07-106">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="fca07-107">Si creerà un modello concettuale dal database (Database First), quindi si modificherà il modello per implementare l'ereditarietà TPH usando la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="fca07-107">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="fca07-108">È possibile eseguire il mapping a un'ereditarietà TPH usando Model First ma è necessario scrivere un flusso di lavoro di generazione del database personalizzato, che è complesso.</span><span class="sxs-lookup"><span data-stu-id="fca07-108">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="fca07-109">Il flusso di lavoro verrà quindi assegnato alla proprietà del **flusso di lavoro di generazione del database** nella finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="fca07-109">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="fca07-110">Un'alternativa più semplice consiste nell'usare Code First.</span><span class="sxs-lookup"><span data-stu-id="fca07-110">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="fca07-111">Altre opzioni di ereditarietà</span><span class="sxs-lookup"><span data-stu-id="fca07-111">Other Inheritance Options</span></span>

<span data-ttu-id="fca07-112">Table-for-Type (TPT) è un altro tipo di ereditarietà in cui viene eseguito il mapping delle tabelle separate del database a entità che partecipano all'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="fca07-112">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="fca07-113">Per informazioni su come eseguire il mapping dell'ereditarietà tabella per tipo con EF designer, vedere [EF designer TPT ereditarietà](xref:ef6/modeling/designer/inheritance/tpt).</span><span class="sxs-lookup"><span data-stu-id="fca07-113"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](xref:ef6/modeling/designer/inheritance/tpt).</span></span>

<span data-ttu-id="fca07-114">I modelli di ereditarietà della tabella per tipo concreto (TPC) e di ereditarietà mista sono supportati dal runtime di Entity Framework ma non sono supportati dalla finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="fca07-114">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="fca07-115">Se si vuole usare TPC o l'ereditarietà mista, sono disponibili due opzioni: usare Code First o modificare manualmente il file EDMX.</span><span class="sxs-lookup"><span data-stu-id="fca07-115">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="fca07-116">Se si sceglie di utilizzare il file EDMX, la finestra Dettagli mapping verrà inserita in "modalità provvisoria" e non sarà possibile utilizzare la finestra di progettazione per modificare i mapping.</span><span class="sxs-lookup"><span data-stu-id="fca07-116">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fca07-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="fca07-117">Prerequisites</span></span>

<span data-ttu-id="fca07-118">Per completare questa procedura dettagliata, è necessario disporre di:</span><span class="sxs-lookup"><span data-stu-id="fca07-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="fca07-119">Una versione recente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fca07-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="fca07-120">[Database di esempio School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="fca07-120">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="fca07-121">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="fca07-121">Set up the Project</span></span>

-   <span data-ttu-id="fca07-122">Aprire Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="fca07-122">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="fca07-123">Selezionare **file- &gt; nuovo- &gt; progetto**</span><span class="sxs-lookup"><span data-stu-id="fca07-123">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="fca07-124">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="fca07-124">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="fca07-125">Immettere **TPHDBFirstSample**   come nome.</span><span class="sxs-lookup"><span data-stu-id="fca07-125">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="fca07-126">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="fca07-126">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="fca07-127">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="fca07-127">Create a Model</span></span>

-   <span data-ttu-id="fca07-128">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="fca07-128">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="fca07-129">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="fca07-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="fca07-130">Immettere **TPHModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="fca07-130">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="fca07-131">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="fca07-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="fca07-132">Fare clic su **nuova connessione**.</span><span class="sxs-lookup"><span data-stu-id="fca07-132">Click **New Connection**.</span></span>
    <span data-ttu-id="fca07-133">Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="fca07-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="fca07-134">La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="fca07-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="fca07-135">Nella finestra di dialogo Scegli oggetti di database, nel nodo tabelle, selezionare la tabella **Person** .</span><span class="sxs-lookup"><span data-stu-id="fca07-135">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="fca07-136">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="fca07-136">Click **Finish**.</span></span>

<span data-ttu-id="fca07-137">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="fca07-137">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="fca07-138">Tutti gli oggetti selezionati nella finestra di dialogo Scegli oggetti di database vengono aggiunti al modello.</span><span class="sxs-lookup"><span data-stu-id="fca07-138">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="fca07-139">Questo è il modo in cui la tabella **Person** Cerca nel database.</span><span class="sxs-lookup"><span data-stu-id="fca07-139">That is how the **Person** table looks in the database.</span></span>

![Tabella Person](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="fca07-141">Implementare l'ereditarietà tabella per gerarchia</span><span class="sxs-lookup"><span data-stu-id="fca07-141">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="fca07-142">La tabella **Person** contiene la colonna **Discriminator** , che può avere uno dei due valori seguenti: "Student" e "Instructor".</span><span class="sxs-lookup"><span data-stu-id="fca07-142">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="fca07-143">A seconda del valore della tabella **Person** verrà eseguito il mapping all'entità **Student** o all'entità **Instructor** .</span><span class="sxs-lookup"><span data-stu-id="fca07-143">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="fca07-144">La tabella **Person** dispone anche di due colonne, **hirete**   e **EnrollmentDate**, che devono **ammettere i valori null** perché una persona non può essere uno studente e un insegnante nello stesso momento, almeno non in questa procedura dettagliata.</span><span class="sxs-lookup"><span data-stu-id="fca07-144">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="fca07-145">Aggiungi nuove entità</span><span class="sxs-lookup"><span data-stu-id="fca07-145">Add new Entities</span></span>

-   <span data-ttu-id="fca07-146">Aggiungere una nuova entità.</span><span class="sxs-lookup"><span data-stu-id="fca07-146">Add a new entity.</span></span>
    <span data-ttu-id="fca07-147">A tale scopo, fare clic con il pulsante destro del mouse su uno spazio vuoto dell'area di progettazione dell'Entity Framework Designer e scegliere **Aggiungi- &gt; entità**.</span><span class="sxs-lookup"><span data-stu-id="fca07-147">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="fca07-148">Digitare **Instructor**   per **nome entità**   e selezionare **Person**nell'   elenco a discesa per il **tipo di base**.</span><span class="sxs-lookup"><span data-stu-id="fca07-148">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="fca07-149">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="fca07-149">Click **OK**.</span></span>
-   <span data-ttu-id="fca07-150">Aggiungere un'altra nuova entità.</span><span class="sxs-lookup"><span data-stu-id="fca07-150">Add another new entity.</span></span> <span data-ttu-id="fca07-151">Digitare **Student**   per **nome entità**   e selezionare **Person**nell'   elenco a discesa per il **tipo di base**.</span><span class="sxs-lookup"><span data-stu-id="fca07-151">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="fca07-152">Due nuovi tipi di entità sono stati aggiunti all'area di progettazione.</span><span class="sxs-lookup"><span data-stu-id="fca07-152">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="fca07-153">Una freccia punta dai nuovi tipi di entità al tipo di entità **Person**   . indica che **Person**   è il tipo base per i nuovi tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="fca07-153">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="fca07-154">Fare clic con il pulsante destro del mouse sulla proprietà **assunte**   dell'entità **Person**   .</span><span class="sxs-lookup"><span data-stu-id="fca07-154">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="fca07-155">Selezionare **taglia** (oppure usare il tasto CTRL + X).</span><span class="sxs-lookup"><span data-stu-id="fca07-155">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="fca07-156">Fare clic con il pulsante destro del mouse sull'entità **Instructor**   e selezionare **Incolla** (oppure utilizzare il tasto CTRL + V).</span><span class="sxs-lookup"><span data-stu-id="fca07-156">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="fca07-157">Fare clic con il pulsante destro del mouse sulla proprietà **assunte**   e scegliere **proprietà**.</span><span class="sxs-lookup"><span data-stu-id="fca07-157">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="fca07-158">Nella finestra **Proprietà**   impostare la proprietà **Nullable**   su **false**.</span><span class="sxs-lookup"><span data-stu-id="fca07-158">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="fca07-159">Fare clic con il pulsante destro del mouse sulla proprietà **EnrollmentDate**   dell'entità **Person**   .</span><span class="sxs-lookup"><span data-stu-id="fca07-159">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="fca07-160">Selezionare **taglia** (oppure usare il tasto CTRL + X).</span><span class="sxs-lookup"><span data-stu-id="fca07-160">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="fca07-161">Fare clic con il pulsante destro del mouse sull'entità **Student** e selezionare **Incolla (oppure utilizzare il tasto CTRL + V).**</span><span class="sxs-lookup"><span data-stu-id="fca07-161">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="fca07-162">Selezionare la proprietà **EnrollmentDate**   e impostare la proprietà **Nullable**   su **false**.</span><span class="sxs-lookup"><span data-stu-id="fca07-162">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="fca07-163">Selezionare il **Person**   tipo di entità Person.</span><span class="sxs-lookup"><span data-stu-id="fca07-163">Select the **Person** entity type.</span></span> <span data-ttu-id="fca07-164">Nella finestra **Proprietà**   impostare la relativa proprietà **abstract**   su **true**.</span><span class="sxs-lookup"><span data-stu-id="fca07-164">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="fca07-165">Elimina la proprietà **Discriminator** da **Person**.</span><span class="sxs-lookup"><span data-stu-id="fca07-165">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="fca07-166">Il motivo per cui deve essere eliminato è illustrato nella sezione seguente.</span><span class="sxs-lookup"><span data-stu-id="fca07-166">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="fca07-167">Eseguire il mapping delle entità</span><span class="sxs-lookup"><span data-stu-id="fca07-167">Map the entities</span></span>

-   <span data-ttu-id="fca07-168">Fare clic con il pulsante destro del mouse sull' **insegnante** e scegliere **mapping tabella.**</span><span class="sxs-lookup"><span data-stu-id="fca07-168">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="fca07-169">L'entità Instructor è selezionata nella finestra Dettagli mapping.</span><span class="sxs-lookup"><span data-stu-id="fca07-169">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="fca07-170">Fare clic su \*\* &lt; Aggiungi tabella o &gt; vista\*\*   nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="fca07-170">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="fca07-171">Il campo \*\* &lt; Aggiungi tabella o vista &gt; \*\*   diventa un elenco a discesa di tabelle o viste a cui è possibile eseguire il mapping dell'entità selezionata.</span><span class="sxs-lookup"><span data-stu-id="fca07-171">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="fca07-172">Selezionare **Person**   dall'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="fca07-172">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="fca07-173">La finestra **Dettagli mapping**   viene aggiornata con i mapping di colonna predefiniti e con un'opzione per l'aggiunta di una condizione.</span><span class="sxs-lookup"><span data-stu-id="fca07-173">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="fca07-174">Fare clic su \*\* &lt; Aggiungi una &gt; condizione\*\*.</span><span class="sxs-lookup"><span data-stu-id="fca07-174">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="fca07-175">Il campo \*\* &lt; Aggiungi una &gt; condizione\*\*   diventa un elenco a discesa di colonne per le quali è possibile impostare le condizioni.</span><span class="sxs-lookup"><span data-stu-id="fca07-175">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="fca07-176">Selezionare **discriminatore**   dall'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="fca07-176">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="fca07-177">Nella colonna **operatore**   della finestra **Dettagli mapping**   selezionare = nell'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="fca07-177">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="fca07-178">Nella colonna **valore/proprietà** digitare **Instructor**.</span><span class="sxs-lookup"><span data-stu-id="fca07-178">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="fca07-179">Il risultato finale dovrebbe essere simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="fca07-179">The end result should look like this:</span></span>

    ![Dettagli mapping](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="fca07-181">Ripetere questi passaggi per il **Student**   tipo di entità Student, ma rendere la condizione uguale al valore **Student** .</span><span class="sxs-lookup"><span data-stu-id="fca07-181">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="fca07-182">*Il motivo per cui si voleva rimuovere la proprietà **Discriminator** è che non è possibile eseguire il mapping di una colonna di tabella più di una volta. Questa colonna verrà usata per il mapping condizionale, pertanto non può essere usata anche per il mapping delle proprietà. L'unico modo in cui può essere usato per entrambi, se una condizione usa un oggetto \*\*è null*\*   o **non è**un   confronto null.\*</span><span class="sxs-lookup"><span data-stu-id="fca07-182">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="fca07-183">A questo punto l'ereditarietà tabella per gerarchia risulta implementata.</span><span class="sxs-lookup"><span data-stu-id="fca07-183">Table-per-hierarchy inheritance is now implemented.</span></span>

![TPH finale](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="fca07-185">Usare il modello</span><span class="sxs-lookup"><span data-stu-id="fca07-185">Use the Model</span></span>

<span data-ttu-id="fca07-186">Aprire il file **Program.cs** in cui è definito il metodo **Main** .</span><span class="sxs-lookup"><span data-stu-id="fca07-186">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="fca07-187">Incollare il codice seguente nella funzione **Main** .</span><span class="sxs-lookup"><span data-stu-id="fca07-187">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="fca07-188">Il codice esegue tre query.</span><span class="sxs-lookup"><span data-stu-id="fca07-188">The code executes three queries.</span></span> <span data-ttu-id="fca07-189">La prima query riporta tutti gli oggetti **Person** .</span><span class="sxs-lookup"><span data-stu-id="fca07-189">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="fca07-190">La seconda query usa il metodo **OfType** per restituire gli oggetti **Instructor** .</span><span class="sxs-lookup"><span data-stu-id="fca07-190">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="fca07-191">La terza query usa il metodo **OfType** per restituire gli oggetti **Student** .</span><span class="sxs-lookup"><span data-stu-id="fca07-191">The third query uses the **OfType** method to return **Student** objects.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
