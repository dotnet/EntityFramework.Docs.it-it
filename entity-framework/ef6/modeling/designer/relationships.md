---
title: Relazioni-EF designer-EF6
description: Relazioni-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 65960dcfcafc239b5ecd8b396a52dababbb4ab08
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073292"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="c8b69-103">Relazioni-EF designer</span><span class="sxs-lookup"><span data-stu-id="c8b69-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="c8b69-104">In questa pagina vengono fornite informazioni sulla configurazione delle relazioni nel modello mediante la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="c8b69-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="c8b69-105">Per informazioni generali sulle relazioni in EF e su come accedere e modificare i dati usando le relazioni, vedere [relazioni & proprietà di navigazione](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="c8b69-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="c8b69-106">Le associazioni definiscono le relazioni tra i tipi di entità in un modello.</span><span class="sxs-lookup"><span data-stu-id="c8b69-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="c8b69-107">In questo argomento viene illustrato come eseguire il mapping delle associazioni con la Entity Framework Designer (progettazione EF).</span><span class="sxs-lookup"><span data-stu-id="c8b69-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="c8b69-108">Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="c8b69-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="c8b69-110">Quando si compila il modello concettuale, è possibile che in Elenco errori vengano visualizzati avvisi riguardanti entità e associazioni non mappate.</span><span class="sxs-lookup"><span data-stu-id="c8b69-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="c8b69-111">È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.</span><span class="sxs-lookup"><span data-stu-id="c8b69-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="c8b69-112">Cenni preliminari sulle associazioni</span><span class="sxs-lookup"><span data-stu-id="c8b69-112">Associations Overview</span></span>

<span data-ttu-id="c8b69-113">Quando si progetta il modello utilizzando la finestra di progettazione EF, un file con estensione edmx rappresenta il modello.</span><span class="sxs-lookup"><span data-stu-id="c8b69-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="c8b69-114">Nel file con estensione edmx, un elemento **Association** definisce una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="c8b69-115">Un'associazione deve specificare i tipi di entità coinvolti nella relazione e il possibile numero di tipi di entità a ogni entità finale della relazione, che è noto come molteplicità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="c8b69-116">La molteplicità di un'entità finale di un'associazione può avere un valore pari a uno (1), zero o uno (0.. 1) o molti ( \* ).</span><span class="sxs-lookup"><span data-stu-id="c8b69-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="c8b69-117">Queste informazioni vengono specificate in due elementi **end** figlio.</span><span class="sxs-lookup"><span data-stu-id="c8b69-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="c8b69-118">In fase di esecuzione, è possibile accedere alle istanze del tipo di entità in un'entità finale di un'associazione tramite proprietà di navigazione o chiavi esterne (se si sceglie di esporre chiavi esterne nelle entità).</span><span class="sxs-lookup"><span data-stu-id="c8b69-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="c8b69-119">Con le chiavi esterne esposte, la relazione tra le entità viene gestita con un elemento **ReferentialConstraint** (un elemento figlio dell'elemento **Association** ).</span><span class="sxs-lookup"><span data-stu-id="c8b69-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="c8b69-120">È consigliabile esporre sempre chiavi esterne per le relazioni nelle entità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="c8b69-121">In molti-a-molti ( \* : \* ) non è possibile aggiungere chiavi esterne alle entità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="c8b69-122">In una \* \* relazione: le informazioni di associazione vengono gestite con un oggetto indipendente.</span><span class="sxs-lookup"><span data-stu-id="c8b69-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="c8b69-123">Per informazioni sugli elementi CSDL (**ReferentialConstraint**, **Association**e così via), vedere la [specifica CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="c8b69-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="c8b69-124">Creazione ed eliminazione di associazioni</span><span class="sxs-lookup"><span data-stu-id="c8b69-124">Create and Delete Associations</span></span>

<span data-ttu-id="c8b69-125">La creazione di un'associazione con la finestra di progettazione EF aggiorna il contenuto del modello del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="c8b69-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="c8b69-126">Dopo aver creato un'associazione, è necessario creare i mapping per l'associazione, descritti più avanti in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c8b69-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="c8b69-127">In questa sezione si presuppone che siano già state aggiunte le entità per le quali si desidera creare un'associazione al modello.</span><span class="sxs-lookup"><span data-stu-id="c8b69-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="c8b69-128">Per creare un'associazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-128">To create an association</span></span>

1.  <span data-ttu-id="c8b69-129">Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi nuovo**e selezionare **associazione.**</span><span class="sxs-lookup"><span data-stu-id="c8b69-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="c8b69-130">Specificare le impostazioni per l'associazione nella finestra di dialogo **Aggiungi associazione** .</span><span class="sxs-lookup"><span data-stu-id="c8b69-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![Aggiungi associazione](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="c8b69-132">È possibile scegliere di non aggiungere proprietà di navigazione o proprietà di chiave esterna alle entità finali dell'associazione deselezionando le caselle di controllo **proprietà di navigazione **e **Aggiungi proprietà di chiave esterna all' &lt; &gt; entità nome tipo di entità **.</span><span class="sxs-lookup"><span data-stu-id="c8b69-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="c8b69-133">Se si aggiunge una sola proprietà di navigazione, l'associazione sarà attraversabile in una sola direzione.</span><span class="sxs-lookup"><span data-stu-id="c8b69-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="c8b69-134">Se non si aggiungono proprietà di navigazione, è necessario scegliere di aggiungere proprietà di chiave esterna per accedere alle entità finali dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c8b69-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="c8b69-135">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="c8b69-136">Per eliminare un'associazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-136">To delete an association</span></span>

<span data-ttu-id="c8b69-137">Per eliminare un'associazione, effettuare una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c8b69-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="c8b69-138">Fare clic con il pulsante destro del mouse sull'associazione nell'area di progettazione EF e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="c8b69-139">oppure -</span><span class="sxs-lookup"><span data-stu-id="c8b69-139">OR -</span></span>

-   <span data-ttu-id="c8b69-140">Selezionare una o più associazioni e premere il tasto CANC.</span><span class="sxs-lookup"><span data-stu-id="c8b69-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="c8b69-141">Includere proprietà di chiave esterna nelle entità (vincoli referenziali)</span><span class="sxs-lookup"><span data-stu-id="c8b69-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="c8b69-142">È consigliabile esporre sempre chiavi esterne per le relazioni nelle entità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="c8b69-143">Entity Framework utilizza un vincolo referenziale per verificare che una proprietà funga da chiave esterna per una relazione.</span><span class="sxs-lookup"><span data-stu-id="c8b69-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="c8b69-144">Se durante la creazione di una relazione è stata selezionata la casella di controllo ***Aggiungi proprietà chiave esterna all' &lt; &gt; entità nome tipo di entità*** , questo vincolo referenziale è stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="c8b69-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="c8b69-145">Quando si usa Entity Framework Designer per aggiungere o modificare un vincolo referenziale, progettazione EF aggiunge o modifica un elemento **ReferentialConstraint**   nel contenuto CSDL del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="c8b69-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="c8b69-146">Fare doppio clic sull'associazione che si desidera modificare.</span><span class="sxs-lookup"><span data-stu-id="c8b69-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="c8b69-147">Verrà visualizzata la finestra di dialogo **vincolo referenziale**   .</span><span class="sxs-lookup"><span data-stu-id="c8b69-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="c8b69-148">Dall' **Principal**   elenco a discesa principale selezionare l'entità principale nel vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c8b69-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="c8b69-149">Le proprietà chiave dell'entità vengono aggiunte all'elenco **chiavi principali**   nella finestra di dialogo.</span><span class="sxs-lookup"><span data-stu-id="c8b69-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="c8b69-150">Dall' **Dependent**   elenco a discesa dipendente selezionare l'entità dipendente nel vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c8b69-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="c8b69-151">Per ogni chiave principale con una chiave dipendente, selezionare una chiave dipendente corrispondente dagli elenchi a discesa nella colonna **chiave dipendente**   .</span><span class="sxs-lookup"><span data-stu-id="c8b69-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Vincolo Ref](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="c8b69-153">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="c8b69-154">Creare e modificare i mapping di associazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="c8b69-155">È possibile specificare il modo in cui un'associazione esegue il mapping al database nella finestra **Dettagli mapping**   di EF designer.</span><span class="sxs-lookup"><span data-stu-id="c8b69-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="c8b69-156">È possibile eseguire il mapping solo dei dettagli per le associazioni per le quali non è specificato un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c8b69-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="c8b69-157">Se viene specificato un vincolo referenziale, viene inclusa una proprietà di chiave esterna nell'entità ed è possibile utilizzare i dettagli di mapping per l'entità per controllare la colonna a cui viene eseguito il mapping della chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="c8b69-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="c8b69-158">Creazione di un mapping di associazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-158">Create an association mapping</span></span>

-   <span data-ttu-id="c8b69-159">Fare clic con il pulsante destro del mouse su un'associazione nell'area di progettazione e scegliere **mapping tabella**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="c8b69-160">Viene visualizzato il mapping di associazione nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="c8b69-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="c8b69-161">Fare clic su **Aggiungi tabella o vista**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="c8b69-162">Verrà visualizzato un elenco a discesa che include tutte le tabelle presenti nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c8b69-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="c8b69-163">Selezionare la tabella alla quale l'associazione verrà mappata.</span><span class="sxs-lookup"><span data-stu-id="c8b69-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="c8b69-164">La finestra **Dettagli mapping**   Visualizza entrambe le estremità dell'associazione e le proprietà chiave per il tipo di entità a ogni **estremità**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="c8b69-165">Per ogni proprietà chiave, fare clic sul campo **colonna**   e selezionare la colonna a cui viene mappata la proprietà.</span><span class="sxs-lookup"><span data-stu-id="c8b69-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![Dettagli mapping 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="c8b69-167">Modificare un mapping di associazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-167">Edit an association mapping</span></span>

-   <span data-ttu-id="c8b69-168">Fare clic con il pulsante destro del mouse su un'associazione nell'area di progettazione e scegliere **mapping tabella**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="c8b69-169">Viene visualizzato il mapping di associazione nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="c8b69-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="c8b69-170">Fare clic su **mapping a &lt; nome &gt; tabella**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="c8b69-171">Verrà visualizzato un elenco a discesa che include tutte le tabelle presenti nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="c8b69-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="c8b69-172">Selezionare la tabella alla quale l'associazione verrà mappata.</span><span class="sxs-lookup"><span data-stu-id="c8b69-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="c8b69-173">La finestra **Dettagli mapping**   Visualizza entrambe le estremità dell'associazione e le proprietà chiave per il tipo di entità a ogni estremità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="c8b69-174">Per ogni proprietà chiave, fare clic sul campo **colonna**   e selezionare la colonna a cui viene mappata la proprietà.</span><span class="sxs-lookup"><span data-stu-id="c8b69-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="c8b69-175">Modificare ed eliminare le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="c8b69-176">Le proprietà di navigazione sono proprietà di collegamento utilizzate per individuare le entità finali di un'associazione in un modello.</span><span class="sxs-lookup"><span data-stu-id="c8b69-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="c8b69-177">È possibile creare le proprietà di navigazione quando si crea un'associazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c8b69-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="c8b69-178">Per modificare le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-178">To edit navigation properties</span></span>

-   <span data-ttu-id="c8b69-179">Selezionare una proprietà di navigazione nell'area di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="c8b69-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="c8b69-180">Le informazioni sulla proprietà di navigazione vengono visualizzate nella finestra **Proprietà**di Visual Studio   .</span><span class="sxs-lookup"><span data-stu-id="c8b69-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="c8b69-181">Modificare le impostazioni delle proprietà nella finestra **Proprietà**   .</span><span class="sxs-lookup"><span data-stu-id="c8b69-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="c8b69-182">Per eliminare le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="c8b69-182">To delete navigation properties</span></span>

-   <span data-ttu-id="c8b69-183">Se le chiavi esterne non sono esposte nei tipi di entità nel modello concettuale, è possibile che l'eliminazione di una proprietà di navigazione renda l'associazione corrispondente attraversabile in un'unica direzione o non attraversabile.</span><span class="sxs-lookup"><span data-stu-id="c8b69-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="c8b69-184">Fare clic con il pulsante destro del mouse su una proprietà di navigazione nell'area di progettazione EF e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="c8b69-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
