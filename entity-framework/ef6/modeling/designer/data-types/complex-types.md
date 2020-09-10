---
title: Tipi complessi-EF designer-EF6
description: Tipi complessi-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 87b62c119894486cbb54adfc8d3af6208a6618a8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620598"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="526b6-103">Tipi complessi-EF designer</span><span class="sxs-lookup"><span data-stu-id="526b6-103">Complex Types - EF Designer</span></span>
<span data-ttu-id="526b6-104">In questo argomento viene illustrato come eseguire il mapping di tipi complessi con il Entity Framework Designer (Entity Designer) e come eseguire una query per le entità che contengono proprietà di tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="526b6-104">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="526b6-105">Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="526b6-105">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="526b6-107">Quando si compila il modello concettuale, è possibile che in Elenco errori vengano visualizzati avvisi riguardanti entità e associazioni non mappate.</span><span class="sxs-lookup"><span data-stu-id="526b6-107">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="526b6-108">È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.</span><span class="sxs-lookup"><span data-stu-id="526b6-108">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="526b6-109">Che cos'è un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-109">What is a Complex Type</span></span>

<span data-ttu-id="526b6-110">I tipi complessi sono proprietà non scalari di tipi di entità che consentono l'organizzazione delle proprietà scalari nelle entità.</span><span class="sxs-lookup"><span data-stu-id="526b6-110">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="526b6-111">Analogamente alle entità, i tipi complessi sono costituiti da proprietà scalari o da altre proprietà dei tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="526b6-111">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="526b6-112">Quando si utilizzano oggetti che rappresentano tipi complessi, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="526b6-112">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="526b6-113">I tipi complessi non dispongono di chiavi e pertanto non possono esistere in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="526b6-113">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="526b6-114">I tipi complessi possono esistere solo come proprietà di tipi di entità o gli altri tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="526b6-114">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="526b6-115">I tipi complessi non possono partecipare alle associazioni e non possono contenere proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="526b6-115">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="526b6-116">Le proprietà del tipo complesso non possono essere **null**.</span><span class="sxs-lookup"><span data-stu-id="526b6-116">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="526b6-117">Si verifica un' **eccezione InvalidOperationException **quando viene chiamato **DbContext. SaveChanges**   e viene rilevato un oggetto complesso null.</span><span class="sxs-lookup"><span data-stu-id="526b6-117">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="526b6-118">Le proprietà scalari degli oggetti complessi possono essere **null**.</span><span class="sxs-lookup"><span data-stu-id="526b6-118">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="526b6-119">I tipi complessi non possono ereditare da altri tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="526b6-119">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="526b6-120">È necessario definire il tipo complesso come una **classe**.</span><span class="sxs-lookup"><span data-stu-id="526b6-120">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="526b6-121">EF rileva le modifiche apportate ai membri in un oggetto di tipo complesso quando viene chiamato **DbContext. DetectChanges** .</span><span class="sxs-lookup"><span data-stu-id="526b6-121">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="526b6-122">Entity Framework chiama automaticamente **DetectChanges** quando vengono chiamati i membri seguenti: **DbSet. Find**, **DbSet. local**, **DbSet. Remove**, **DbSet. Add**, **DbSet. Connetti**, **DbContext. SaveChanges**, **DbContext. GetValidationErrors**, **DbContext. entry**, DbChangeTracker **. Entries**.</span><span class="sxs-lookup"><span data-stu-id="526b6-122">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="526b6-123">Effettuare il refactoring delle proprietà di un'entità in un nuovo tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-123">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="526b6-124">Se si dispone già di un'entità nel modello concettuale, potrebbe essere necessario effettuare il refactoring di alcune proprietà in una proprietà di tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="526b6-124">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="526b6-125">Nell'area di progettazione selezionare una o più proprietà (escluse le proprietà di navigazione) di un'entità, quindi fare clic con il pulsante destro del mouse e scegliere **refactoring- &gt; Sposta in nuovo tipo complesso**.</span><span class="sxs-lookup"><span data-stu-id="526b6-125">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![Effettua il refactoring nel nuovo tipo complesso](~/ef6/media/refactor.png)

<span data-ttu-id="526b6-127">Un nuovo tipo complesso con le proprietà selezionate viene aggiunto a **browser modello**.</span><span class="sxs-lookup"><span data-stu-id="526b6-127">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="526b6-128">Al tipo complesso viene assegnato un nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="526b6-128">The complex type is given a default name.</span></span>

<span data-ttu-id="526b6-129">Una proprietà complessa del tipo appena creato sostituisce le proprietà selezionate.</span><span class="sxs-lookup"><span data-stu-id="526b6-129">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="526b6-130">Tutti i mapping delle proprietà vengono mantenuti.</span><span class="sxs-lookup"><span data-stu-id="526b6-130">All property mappings are preserved.</span></span>

![Effettua il refactoring nel nuovo tipo complesso 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="526b6-132">Crea nuovo tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-132">Create a New Complex Type</span></span>

<span data-ttu-id="526b6-133">È anche possibile creare un nuovo tipo complesso che non contiene proprietà di un'entità esistente.</span><span class="sxs-lookup"><span data-stu-id="526b6-133">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="526b6-134">Fare clic con il pulsante destro del mouse sulla cartella **tipi complessi** in browser modello, scegliere **tipo complesso AddNew...**.</span><span class="sxs-lookup"><span data-stu-id="526b6-134">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="526b6-135">In alternativa, è possibile selezionare la cartella **tipi complessi** e premere il tasto **ins**   sulla tastiera.</span><span class="sxs-lookup"><span data-stu-id="526b6-135">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![Aggiungi nuovo tipo complesso](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="526b6-137">Un nuovo tipo complesso verrà aggiunto nella cartella con un nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="526b6-137">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="526b6-138">È ora possibile aggiungere proprietà al tipo.</span><span class="sxs-lookup"><span data-stu-id="526b6-138">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="526b6-139">Aggiungere proprietà a un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-139">Add Properties to a Complex Type</span></span>

<span data-ttu-id="526b6-140">Le proprietà di un tipo complesso possono essere tipi scalari o tipi complessi esistenti.</span><span class="sxs-lookup"><span data-stu-id="526b6-140">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="526b6-141">Tuttavia, le proprietà dei tipi complessi non possono avere riferimenti circolari.</span><span class="sxs-lookup"><span data-stu-id="526b6-141">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="526b6-142">Un tipo complesso **OnsiteCourseDetails**, ad esempio,   non può avere una proprietà di tipo complesso **OnsiteCourseDetails**.</span><span class="sxs-lookup"><span data-stu-id="526b6-142">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="526b6-143">È possibile aggiungere una proprietà a un tipo complesso in uno dei modi elencati di seguito.</span><span class="sxs-lookup"><span data-stu-id="526b6-143">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="526b6-144">Fare clic con il pulsante destro del mouse su un tipo complesso in browser modello, scegliere **Aggiungi**, scegliere **proprietà scalare**   o **proprietà complessa**, quindi selezionare il tipo di proprietà desiderato.</span><span class="sxs-lookup"><span data-stu-id="526b6-144">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="526b6-145">In alternativa, è possibile selezionare un tipo complesso e premere il tasto **ins**   sulla tastiera.</span><span class="sxs-lookup"><span data-stu-id="526b6-145">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![Aggiungere proprietà a un tipo complesso](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="526b6-147">Una nuova proprietà verrà aggiunta al tipo complesso con un nome predefinito.</span><span class="sxs-lookup"><span data-stu-id="526b6-147">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="526b6-148">oppure -</span><span class="sxs-lookup"><span data-stu-id="526b6-148">OR -</span></span>

-   <span data-ttu-id="526b6-149">Fare clic con il pulsante destro del mouse su una proprietà dell'entità nell'area di **progettazione EF** e scegliere **copia**, quindi fare clic con il pulsante destro del mouse sul tipo complesso in **browser modello** e scegliere **Incolla**.</span><span class="sxs-lookup"><span data-stu-id="526b6-149">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="526b6-150">Rinominare un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-150">Rename a Complex Type</span></span>

<span data-ttu-id="526b6-151">Quando si rinomina un tipo complesso, tutti i riferimenti al tipo presenti nell'intero progetto vengono aggiornati.</span><span class="sxs-lookup"><span data-stu-id="526b6-151">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="526b6-152">Fare doppio clic su un tipo complesso in **browser modello**.</span><span class="sxs-lookup"><span data-stu-id="526b6-152">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="526b6-153">Il nome verrà selezionato e sarà in modalità di modifica.</span><span class="sxs-lookup"><span data-stu-id="526b6-153">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="526b6-154">oppure -</span><span class="sxs-lookup"><span data-stu-id="526b6-154">OR -</span></span>

-   <span data-ttu-id="526b6-155">Fare clic con il pulsante destro del mouse su un tipo complesso in **browser modello** e scegliere **Rinomina**.</span><span class="sxs-lookup"><span data-stu-id="526b6-155">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="526b6-156">oppure -</span><span class="sxs-lookup"><span data-stu-id="526b6-156">OR -</span></span>

-   <span data-ttu-id="526b6-157">Selezionare un tipo complesso nella finestra Browser modello e premere il tasto F2.</span><span class="sxs-lookup"><span data-stu-id="526b6-157">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="526b6-158">oppure -</span><span class="sxs-lookup"><span data-stu-id="526b6-158">OR -</span></span>

-   <span data-ttu-id="526b6-159">Fare clic con il pulsante destro del mouse su un tipo complesso in **browser modello** e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="526b6-159">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="526b6-160">Modificare il nome nella finestra **Proprietà**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-160">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="526b6-161">Aggiungere un tipo complesso esistente a un'entità ed eseguire il mapping delle proprietà alle colonne della tabella</span><span class="sxs-lookup"><span data-stu-id="526b6-161">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="526b6-162">Fare clic con il pulsante destro del mouse su un'entità, scegliere **Aggiungi nuovo**e selezionare **proprietà complessa**.</span><span class="sxs-lookup"><span data-stu-id="526b6-162">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="526b6-163">Una proprietà del tipo complesso con un nome predefinito verrà aggiunta all'entità.</span><span class="sxs-lookup"><span data-stu-id="526b6-163">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="526b6-164">Un tipo predefinito, scelto tra i tipi complessi esistenti, verrà assegnato alla proprietà.</span><span class="sxs-lookup"><span data-stu-id="526b6-164">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="526b6-165">Assegnare il tipo desiderato alla proprietà nella finestra **Proprietà**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-165">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="526b6-166">Dopo avere aggiunto una proprietà del tipo complesso a un'entità, è necessario eseguire il mapping delle proprietà alle colonne della tabella.</span><span class="sxs-lookup"><span data-stu-id="526b6-166">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="526b6-167">Fare clic con il pulsante destro del mouse su un tipo di entità nell'area di progettazione o in **browser modello**   e selezionare **mapping tabella**.</span><span class="sxs-lookup"><span data-stu-id="526b6-167">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="526b6-168">I mapping delle tabelle vengono visualizzati nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-168">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="526b6-169">Espandere il nodo **mapping a &lt; nome &gt; tabella**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-169">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="526b6-170">Viene visualizzato un nodo **Mapping colonne**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-170">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="526b6-171">Espandere il nodo **Mapping colonne**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-171">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="526b6-172">Verrà visualizzato un elenco di tutte le colonne della tabella.</span><span class="sxs-lookup"><span data-stu-id="526b6-172">A list of all the columns in the table appears.</span></span> <span data-ttu-id="526b6-173">Le proprietà predefinite, se presenti, a cui è associato il mapping delle colonne sono elencate sotto l'intestazione **valore/proprietà**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-173">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="526b6-174">Selezionare la colonna di cui si desidera eseguire il mapping, quindi fare clic con il pulsante destro del mouse sul campo **valore/proprietà**corrispondente   .</span><span class="sxs-lookup"><span data-stu-id="526b6-174">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="526b6-175">Verrà visualizzato un elenco a discesa di tutte le proprietà scalari.</span><span class="sxs-lookup"><span data-stu-id="526b6-175">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="526b6-176">Selezionare la proprietà appropriata.</span><span class="sxs-lookup"><span data-stu-id="526b6-176">Select the appropriate property.</span></span>

    ![Tipo complesso mappa](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="526b6-178">Ripetere i passaggi 6 e 7 per ogni colonna della tabella.</span><span class="sxs-lookup"><span data-stu-id="526b6-178">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="526b6-179">Per eliminare un mapping di colonna, selezionare la colonna di cui si desidera eseguire il mapping, quindi fare clic sul campo **valore/proprietà**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-179">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="526b6-180">Selezionare quindi **Elimina** dall'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="526b6-180">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="526b6-181">Eseguire il mapping di un'importazione di funzioni a un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-181">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="526b6-182">Le importazioni di funzioni sono basate sulle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="526b6-182">Function imports are based on stored procedures.</span></span> <span data-ttu-id="526b6-183">Per mappare un'importazione di funzioni a un tipo complesso, le colonne restituite dalla stored procedure corrispondente devono corrispondere alle proprietà del tipo complesso nel numero e devono disporre di tipi di archiviazione che sono compatibili con i tipi di proprietà.</span><span class="sxs-lookup"><span data-stu-id="526b6-183">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="526b6-184">Fare doppio clic su una funzione importata di cui si desidera eseguire il mapping a un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="526b6-184">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![Importazioni di funzioni](~/ef6/media/functionimports.png)

-   <span data-ttu-id="526b6-186">Specificare le impostazioni per la nuova importazione di funzioni come segue:</span><span class="sxs-lookup"><span data-stu-id="526b6-186">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="526b6-187">Specificare il stored procedure per cui si sta creando un'importazione di funzioni nel campo **Nome stored procedure**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-187">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="526b6-188">Questo campo è un elenco a discesa in cui sono visualizzate tutte le stored procedure contenute nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="526b6-188">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="526b6-189">Specificare il nome dell'importazione di funzioni nel campo **Nome importazione funzione**   .</span><span class="sxs-lookup"><span data-stu-id="526b6-189">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="526b6-190">Selezionare **complesso**   come tipo restituito e quindi specificare il tipo restituito complesso specifico scegliendo il tipo appropriato dall'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="526b6-190">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![Modifica importazione funzione](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="526b6-192">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="526b6-192">Click **OK**.</span></span>
    <span data-ttu-id="526b6-193">L'importazione di funzioni viene creata nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="526b6-193">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="526b6-194">Personalizzare il mapping delle colonne per l'importazione di funzioni</span><span class="sxs-lookup"><span data-stu-id="526b6-194">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="526b6-195">Fare clic con il pulsante destro del mouse sull'importazione di funzioni in browser modello, quindi scegliere **mapping importazione funzioni**.</span><span class="sxs-lookup"><span data-stu-id="526b6-195">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="526b6-196">Viene visualizzata la finestra **Dettagli mapping**   che mostra il mapping predefinito per l'importazione di funzioni.</span><span class="sxs-lookup"><span data-stu-id="526b6-196">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="526b6-197">Le frecce indicano i mapping tra i valori della proprietà e i valori della colonna.</span><span class="sxs-lookup"><span data-stu-id="526b6-197">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="526b6-198">Per impostazione predefinita, i nomi della colonna corrispondono ai nomi delle proprietà di tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="526b6-198">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="526b6-199">I nomi delle colonne predefiniti vengono visualizzati in grigio.</span><span class="sxs-lookup"><span data-stu-id="526b6-199">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="526b6-200">Se necessario, modificare i nomi delle colonne per renderli corrispondenti ai nomi delle colonne restituiti dalla stored procedure che corrisponde all'importazione di funzioni.</span><span class="sxs-lookup"><span data-stu-id="526b6-200">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="526b6-201">Eliminare un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-201">Delete a Complex Type</span></span>

<span data-ttu-id="526b6-202">Quando si elimina un tipo complesso, questo viene eliminato dal modello concettuale; vengono inoltre eliminati i mapping di tutte le istanze del tipo.</span><span class="sxs-lookup"><span data-stu-id="526b6-202">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="526b6-203">I riferimenti al tipo non vengono tuttavia aggiornati.</span><span class="sxs-lookup"><span data-stu-id="526b6-203">However, references to the type are not updated.</span></span> <span data-ttu-id="526b6-204">Se, ad esempio, un'entità ha una proprietà di tipo complesso di tipo Tipocomplesso1 e Tipocomplesso1 viene eliminato in **browser modello**, la proprietà dell'entità corrispondente non viene aggiornata.</span><span class="sxs-lookup"><span data-stu-id="526b6-204">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="526b6-205">Il modello non verrà convalidato perché contiene un'entità che fa riferimento a un tipo complesso eliminato.</span><span class="sxs-lookup"><span data-stu-id="526b6-205">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="526b6-206">Tramite Entity Designer è possibile aggiornare o eliminare i riferimenti ai tipi complessi eliminati.</span><span class="sxs-lookup"><span data-stu-id="526b6-206">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="526b6-207">Fare clic con il pulsante destro del mouse su un tipo complesso in browser modello e scegliere **Elimina**.</span><span class="sxs-lookup"><span data-stu-id="526b6-207">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="526b6-208">oppure -</span><span class="sxs-lookup"><span data-stu-id="526b6-208">OR -</span></span>

-   <span data-ttu-id="526b6-209">Selezionare un tipo complesso nella finestra Browser modello e premere il tasto CANC sulla tastiera.</span><span class="sxs-lookup"><span data-stu-id="526b6-209">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="526b6-210">Query per entità contenenti proprietà di tipo complesso</span><span class="sxs-lookup"><span data-stu-id="526b6-210">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="526b6-211">Nel codice seguente viene illustrato come eseguire una query che restituisce una raccolta di oggetti del tipo di entità che contengono una proprietà di tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="526b6-211">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
