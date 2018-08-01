---
title: Relazioni - finestra di progettazione di Entity Framework - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
caps.latest.revision: 3
ms.openlocfilehash: f924945b19dd6d73847ff3ec52c0b5a286c591bb
ms.sourcegitcommit: 9ae4473425c5e76337c9d032b0e5dbfedf1fcf57
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "39121448"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="04d40-102">Relazioni - finestra di progettazione di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="04d40-102">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="04d40-103">Questa pagina fornisce informazioni sulla configurazione di relazioni nel modello utilizzando la finestra di progettazione di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="04d40-103">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="04d40-104">Per informazioni generali sulle relazioni in Entity Framework e su come accedere e modificare dati tramite relazioni, vedere [relazioni di & proprietà di navigazione](~/ef6/fundamentals/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="04d40-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>

<span data-ttu-id="04d40-105">Le associazioni definiscono le relazioni tra i tipi di entità in un modello.</span><span class="sxs-lookup"><span data-stu-id="04d40-105">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="04d40-106">Questo argomento illustra come eseguire il mapping delle associazioni con Entity Framework Designer (Entity Framework Designer).</span><span class="sxs-lookup"><span data-stu-id="04d40-106">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="04d40-107">L'immagine seguente mostra le finestre principali che vengono usate quando si lavora con la finestra di progettazione di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="04d40-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EFDesigner](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="04d40-109">Quando si compila il modello concettuale, avvisi relativi a entità non mappata e associazioni vengano visualizzati nell'elenco errori.</span><span class="sxs-lookup"><span data-stu-id="04d40-109">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="04d40-110">È possibile ignorare questi avvisi perché dopo aver scelto di generare il database dal modello, gli errori non verranno più visualizzato.</span><span class="sxs-lookup"><span data-stu-id="04d40-110">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="04d40-111">Panoramica delle associazioni</span><span class="sxs-lookup"><span data-stu-id="04d40-111">Associations Overview</span></span>

<span data-ttu-id="04d40-112">Quando si progetta il modello utilizzando la finestra di progettazione di Entity Framework, un file con estensione edmx rappresenta il modello.</span><span class="sxs-lookup"><span data-stu-id="04d40-112">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="04d40-113">Nel file con estensione edmx, un' **Association** elemento definisce una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="04d40-113">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="04d40-114">Un'associazione deve specificare i tipi di entità coinvolti nella relazione e il possibile numero di tipi di entità a ogni entità finale della relazione, che è noto come molteplicità.</span><span class="sxs-lookup"><span data-stu-id="04d40-114">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="04d40-115">La molteplicità di un'entità finale dell'associazione può avere un valore di uno (1), zero o uno (0..1) o molti (\*).</span><span class="sxs-lookup"><span data-stu-id="04d40-115">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="04d40-116">Queste informazioni vengono specificate in due figlio **End** elementi.</span><span class="sxs-lookup"><span data-stu-id="04d40-116">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="04d40-117">In fase di esecuzione, istanze del tipo di entità in un'entità finale di un'associazione accessibili attraverso proprietà di navigazione o chiavi esterne (se si sceglie di esporre le chiavi esterne nelle entità).</span><span class="sxs-lookup"><span data-stu-id="04d40-117">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="04d40-118">Con le chiavi esterne esposte, la relazione tra l'entità viene gestita con un **ReferentialConstraint** elemento (un elemento figlio delle **Association** elemento).</span><span class="sxs-lookup"><span data-stu-id="04d40-118">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="04d40-119">È consigliabile esporre sempre le chiavi esterne per le relazioni nelle entità.</span><span class="sxs-lookup"><span data-stu-id="04d40-119">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="04d40-120">In molti-a-molti (\*:\*) non è possibile aggiungere chiavi esterne alle entità.</span><span class="sxs-lookup"><span data-stu-id="04d40-120">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="04d40-121">In un \*:\* relazione, le informazioni di associazione viene gestito con un oggetto indipendente.</span><span class="sxs-lookup"><span data-stu-id="04d40-121">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="04d40-122">Per informazioni sugli elementi CSDL (**ReferentialConstraint**, **Association**e così via) vedere il [specifica CSDL](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span><span class="sxs-lookup"><span data-stu-id="04d40-122">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="04d40-123">Creare ed eliminare le associazioni</span><span class="sxs-lookup"><span data-stu-id="04d40-123">Create and Delete Associations</span></span>

<span data-ttu-id="04d40-124">Creazione di un'associazione con gli aggiornamenti di Entity Framework Designer il contenuto del modello del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="04d40-124">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="04d40-125">Dopo aver creato un'associazione, è necessario creare i mapping per l'associazione (descritti più avanti in questo argomento).</span><span class="sxs-lookup"><span data-stu-id="04d40-125">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="04d40-126">In questa sezione si presuppone che già stato aggiunto l'entità che si desidera creare un'associazione tra il modello.</span><span class="sxs-lookup"><span data-stu-id="04d40-126">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="04d40-127">Per creare un'associazione</span><span class="sxs-lookup"><span data-stu-id="04d40-127">To create an association</span></span>

1.  <span data-ttu-id="04d40-128">Fare doppio clic su un'area vuota dell'area di progettazione, scegliere **Aggiungi nuovo**e selezionare **Association...** .</span><span class="sxs-lookup"><span data-stu-id="04d40-128">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="04d40-129">Specificare le impostazioni per l'associazione nel **Aggiungi associazione** finestra di dialogo.</span><span class="sxs-lookup"><span data-stu-id="04d40-129">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![AddAssociation](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="04d40-131">È possibile scegliere di non aggiungere le proprietà di navigazione o proprietà di chiave esterna per le entità finali dell'associazione deselezionando le * * proprietà di navigazione * * e * * aggiungere proprietà di chiave esterna per il &lt;nome tipo di entità&gt; entità * * caselle di controllo.</span><span class="sxs-lookup"><span data-stu-id="04d40-131">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="04d40-132">Se si aggiunge una sola proprietà di navigazione, l'associazione sarà attraversabile in una sola direzione.</span><span class="sxs-lookup"><span data-stu-id="04d40-132">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="04d40-133">Se non si aggiungono proprietà di navigazione, è necessario scegliere di aggiungere proprietà di chiave esterna per accedere alle entità finali dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="04d40-133">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="04d40-134">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="04d40-134">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="04d40-135">Per eliminare un'associazione</span><span class="sxs-lookup"><span data-stu-id="04d40-135">To delete an association</span></span>

<span data-ttu-id="04d40-136">Per eliminare, eseguire un associazione una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="04d40-136">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="04d40-137">Fare doppio clic sull'associazione in Entity Framework Designer e selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="04d40-137">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="04d40-138">OR:</span><span class="sxs-lookup"><span data-stu-id="04d40-138">OR -</span></span>

-   <span data-ttu-id="04d40-139">Selezionare una o più associazioni e premere il tasto CANC.</span><span class="sxs-lookup"><span data-stu-id="04d40-139">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="04d40-140">Includere proprietà di chiave esterna nelle entità (vincoli referenziali)</span><span class="sxs-lookup"><span data-stu-id="04d40-140">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="04d40-141">È consigliabile esporre sempre le chiavi esterne per le relazioni nelle entità.</span><span class="sxs-lookup"><span data-stu-id="04d40-141">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="04d40-142">Entity Framework Usa un vincolo referenziale per identificare una proprietà funge da chiave esterna per una relazione.</span><span class="sxs-lookup"><span data-stu-id="04d40-142">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="04d40-143">Se è stata selezionata la ***aggiungere le proprietà di chiave esterna per il &lt;nome tipo di entità&gt; entità*** casella di controllo quando si crea una relazione, il vincolo referenziale è stato aggiunto automaticamente.</span><span class="sxs-lookup"><span data-stu-id="04d40-143">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="04d40-144">Quando si usa Entity Framework Designer per aggiungere o modificare un vincolo referenziale, la finestra di progettazione di Entity Framework aggiunge o modifica una **ReferentialConstraint** elemento nel contenuto CSDL del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="04d40-144">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="04d40-145">Fare doppio clic sull'associazione che si desidera modificare.</span><span class="sxs-lookup"><span data-stu-id="04d40-145">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="04d40-146">Il **vincolo referenziale** verrà visualizzata la finestra di dialogo.</span><span class="sxs-lookup"><span data-stu-id="04d40-146">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="04d40-147">Dal **Principal** elenco a discesa selezionare l'entità principale nel vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="04d40-147">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="04d40-148">Le proprietà di chiave dell'entità vengono aggiunti per il **chiave dell'entità** elenco nella finestra di dialogo.</span><span class="sxs-lookup"><span data-stu-id="04d40-148">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="04d40-149">Dal **dipendenti** elenco a discesa selezionare l'entità dipendente nel vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="04d40-149">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="04d40-150">Per ogni chiave dell'entità che dispone di una chiave dipendente, selezionare una chiave dipendente corrispondente dagli elenchi a discesa scegliere il **chiave dipendente** colonna.</span><span class="sxs-lookup"><span data-stu-id="04d40-150">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![RefConstraint](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="04d40-152">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="04d40-152">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="04d40-153">Creare e modificare i mapping di associazione</span><span class="sxs-lookup"><span data-stu-id="04d40-153">Create and Edit Association Mappings</span></span>

<span data-ttu-id="04d40-154">È possibile specificare come un'associazione esegue il mapping al database di **Dettagli Mapping** finestra della finestra di progettazione di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="04d40-154">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="04d40-155">È possibile mappare solo i dettagli per le associazioni che non dispongono di un vincolo referenziale specificato.</span><span class="sxs-lookup"><span data-stu-id="04d40-155">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="04d40-156">Se viene specificato un vincolo referenziale una proprietà di chiave esterna è incluso nell'entità e i dettagli di Mapping è possibile usare per l'entità al quale colonna chiave esterna viene eseguito il mapping al controllo.</span><span class="sxs-lookup"><span data-stu-id="04d40-156">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="04d40-157">Creare un mapping di associazione</span><span class="sxs-lookup"><span data-stu-id="04d40-157">Create an association mapping</span></span>

-   <span data-ttu-id="04d40-158">Fare doppio clic su un'associazione nell'area di progettazione e seleziona **Mapping tabella**.</span><span class="sxs-lookup"><span data-stu-id="04d40-158">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="04d40-159">Ciò consente di visualizzare il mapping di associazione nel **ulteriori dettagli sul Mapping** finestra.</span><span class="sxs-lookup"><span data-stu-id="04d40-159">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="04d40-160">Fare clic su **aggiungere una tabella o vista**.</span><span class="sxs-lookup"><span data-stu-id="04d40-160">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="04d40-161">Verrà visualizzato un elenco a discesa che include tutte le tabelle presenti nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="04d40-161">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="04d40-162">Selezionare la tabella alla quale l'associazione verrà mappata.</span><span class="sxs-lookup"><span data-stu-id="04d40-162">Select the table to which the association will map.</span></span>
    <span data-ttu-id="04d40-163">Il **Dettagli Mapping** finestra vengono visualizzate entrambe le estremità dell'associazione e le proprietà chiave per il tipo di entità in ogni **End**.</span><span class="sxs-lookup"><span data-stu-id="04d40-163">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="04d40-164">Per ogni proprietà chiave, scegliere il **colonna** campo e selezionare la colonna a cui verrà eseguito il mapping della proprietà.</span><span class="sxs-lookup"><span data-stu-id="04d40-164">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![MappingDetails4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="04d40-166">Modifica un mapping di associazione</span><span class="sxs-lookup"><span data-stu-id="04d40-166">Edit an association mapping</span></span>

-   <span data-ttu-id="04d40-167">Fare doppio clic su un'associazione nell'area di progettazione e seleziona **Mapping tabella**.</span><span class="sxs-lookup"><span data-stu-id="04d40-167">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="04d40-168">Ciò consente di visualizzare il mapping di associazione nel **ulteriori dettagli sul Mapping** finestra.</span><span class="sxs-lookup"><span data-stu-id="04d40-168">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="04d40-169">Fare clic su **esegue il mapping a &lt;nome tabella&gt;**.</span><span class="sxs-lookup"><span data-stu-id="04d40-169">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="04d40-170">Verrà visualizzato un elenco a discesa che include tutte le tabelle presenti nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="04d40-170">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="04d40-171">Selezionare la tabella alla quale l'associazione verrà mappata.</span><span class="sxs-lookup"><span data-stu-id="04d40-171">Select the table to which the association will map.</span></span>
    <span data-ttu-id="04d40-172">Il **Dettagli Mapping** finestra vengono visualizzate entrambe le estremità dell'associazione e le proprietà chiave per il tipo di entità in ciascuna estremità.</span><span class="sxs-lookup"><span data-stu-id="04d40-172">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="04d40-173">Per ogni proprietà chiave, scegliere il **colonna** campo e selezionare la colonna a cui verrà eseguito il mapping della proprietà.</span><span class="sxs-lookup"><span data-stu-id="04d40-173">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="04d40-174">Modifica e le proprietà di navigazione Delete</span><span class="sxs-lookup"><span data-stu-id="04d40-174">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="04d40-175">Proprietà di navigazione sono proprietà di collegamento utilizzate per individuare le entità finali di un'associazione in un modello.</span><span class="sxs-lookup"><span data-stu-id="04d40-175">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="04d40-176">È possibile creare le proprietà di navigazione quando si crea un'associazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="04d40-176">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="04d40-177">Per modificare le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="04d40-177">To edit navigation properties</span></span>

-   <span data-ttu-id="04d40-178">Selezionare una proprietà di navigazione nell'area di progettazione di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="04d40-178">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="04d40-179">In Visual Studio vengono visualizzate informazioni sulla proprietà di navigazione **proprietà** finestra.</span><span class="sxs-lookup"><span data-stu-id="04d40-179">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="04d40-180">Modificare le impostazioni delle proprietà nel **proprietà** finestra.</span><span class="sxs-lookup"><span data-stu-id="04d40-180">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="04d40-181">Per eliminare le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="04d40-181">To delete navigation properties</span></span>

-   <span data-ttu-id="04d40-182">Se le chiavi esterne non sono esposte nei tipi di entità nel modello concettuale, è possibile che l'eliminazione di una proprietà di navigazione renda l'associazione corrispondente attraversabile in un'unica direzione o non attraversabile.</span><span class="sxs-lookup"><span data-stu-id="04d40-182">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="04d40-183">Fare doppio clic su una proprietà di navigazione della finestra di progettazione di Entity Framework e selezionare **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="04d40-183">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>