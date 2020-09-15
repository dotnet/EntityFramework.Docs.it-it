---
title: Definizione di query-EF designer-EF6
description: Definizione di query-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: b5d24b9f6c047eb8681a0343c768d1ffa70ca6b8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070341"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="52f16-103">Definizione di query-EF designer</span><span class="sxs-lookup"><span data-stu-id="52f16-103">Defining Query - EF Designer</span></span>
<span data-ttu-id="52f16-104">Questa procedura dettagliata illustra come aggiungere una query di definizione e un tipo di entità corrispondente a un modello usando la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="52f16-104">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="52f16-105">Una query di definizione viene in genere utilizzata per fornire funzionalità simili a quelle fornite da una vista di database, ma la vista è definita nel modello e non nel database.</span><span class="sxs-lookup"><span data-stu-id="52f16-105">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="52f16-106">Una query di definizione consente di eseguire un'istruzione SQL specificata nell'elemento **DefiningQuery**   di un file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="52f16-106">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="52f16-107">Per ulteriori informazioni, vedere **DefiningQuery** nella [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="52f16-107">For more information, see **DefiningQuery** in the [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="52f16-108">Quando si usa la definizione di query, è anche necessario definire un tipo di entità nel modello.</span><span class="sxs-lookup"><span data-stu-id="52f16-108">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="52f16-109">Il tipo di entità viene utilizzato per la superficie dei dati esposti dalla query di definizione.</span><span class="sxs-lookup"><span data-stu-id="52f16-109">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="52f16-110">Si noti che i dati esposti tramite questo tipo di entità sono di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="52f16-110">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="52f16-111">Le query con parametri non possono essere eseguite come query di definizione.</span><span class="sxs-lookup"><span data-stu-id="52f16-111">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="52f16-112">Tuttavia, è possibile aggiornare i dati eseguendo il mapping delle funzioni di inserimento, aggiornamento ed eliminazione del tipo di entità che fa emergere i dati nelle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="52f16-112">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="52f16-113">Per ulteriori informazioni, vedere [inserimento, aggiornamento ed eliminazione con le stored procedure](xref:ef6/modeling/designer/stored-procedures/cud).</span><span class="sxs-lookup"><span data-stu-id="52f16-113">For more information, see [Insert, Update, and Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span></span>

<span data-ttu-id="52f16-114">In questo argomento viene illustrato come eseguire le attività seguenti.</span><span class="sxs-lookup"><span data-stu-id="52f16-114">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="52f16-115">Aggiungere una query di definizione</span><span class="sxs-lookup"><span data-stu-id="52f16-115">Add a Defining Query</span></span>
-   <span data-ttu-id="52f16-116">Aggiungere un tipo di entità al modello</span><span class="sxs-lookup"><span data-stu-id="52f16-116">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="52f16-117">Mappare la query di definizione al tipo di entità</span><span class="sxs-lookup"><span data-stu-id="52f16-117">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="52f16-118">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="52f16-118">Prerequisites</span></span>

<span data-ttu-id="52f16-119">Per completare questa procedura dettagliata, è necessario disporre di:</span><span class="sxs-lookup"><span data-stu-id="52f16-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="52f16-120">Una versione recente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="52f16-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="52f16-121">[Database di esempio School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="52f16-121">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="52f16-122">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="52f16-122">Set up the Project</span></span>

<span data-ttu-id="52f16-123">Questa procedura dettagliata usa Visual Studio 2012 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="52f16-123">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="52f16-124">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="52f16-124">Open Visual Studio.</span></span>
-   <span data-ttu-id="52f16-125">Scegliere **Nuovo** dal menu **File**e quindi fare clic su **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="52f16-125">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="52f16-126">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **applicazione console** .</span><span class="sxs-lookup"><span data-stu-id="52f16-126">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="52f16-127">Immettere **DefiningQuerySample** come nome del progetto e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="52f16-127">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="52f16-128">Creare un modello basato sul database School</span><span class="sxs-lookup"><span data-stu-id="52f16-128">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="52f16-129">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="52f16-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="52f16-130">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="52f16-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="52f16-131">Immettere **DefiningQueryModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="52f16-131">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="52f16-132">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="52f16-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="52f16-133">Fare clic su nuova connessione.</span><span class="sxs-lookup"><span data-stu-id="52f16-133">Click New Connection.</span></span> <span data-ttu-id="52f16-134">Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="52f16-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="52f16-135">La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="52f16-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="52f16-136">Nella finestra di dialogo Scegli oggetti di database selezionare il nodo **tabelle**   .</span><span class="sxs-lookup"><span data-stu-id="52f16-136">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="52f16-137">In questo modo, tutte le tabelle vengono aggiunte al modello **School** .</span><span class="sxs-lookup"><span data-stu-id="52f16-137">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="52f16-138">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="52f16-138">Click **Finish**.</span></span>
-   <span data-ttu-id="52f16-139">In Esplora soluzioni fare clic con il pulsante destro del mouse sul file **DefiningQueryModel. edmx** e scegliere **Apri con...**.</span><span class="sxs-lookup"><span data-stu-id="52f16-139">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="52f16-140">Seleziona **editor XML (testo)**.</span><span class="sxs-lookup"><span data-stu-id="52f16-140">Select **XML (Text) Editor**.</span></span>

    ![Editor XML](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="52f16-142">Fare clic su **Sì** se richiesto con il messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="52f16-142">Click **Yes** if prompted with the following message:</span></span>

    ![Avviso 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="52f16-144">Aggiungere una query di definizione</span><span class="sxs-lookup"><span data-stu-id="52f16-144">Add a Defining Query</span></span>

<span data-ttu-id="52f16-145">In questo passaggio verrà usato l'editor XML per aggiungere una query di definizione e un tipo di entità alla sezione SSDL del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="52f16-145">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="52f16-146">Aggiungere un elemento **EntitySet**   alla sezione SSDL del file con estensione edmx (riga 5 fino a 13).</span><span class="sxs-lookup"><span data-stu-id="52f16-146">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="52f16-147">Specificare le opzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="52f16-147">Specify the following:</span></span>
    -   <span data-ttu-id="52f16-148">Vengono specificati solo gli attributi **Name**   e **EntityType**   dell'elemento **EntitySet**   .</span><span class="sxs-lookup"><span data-stu-id="52f16-148">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="52f16-149">Il nome completo del tipo di entità viene utilizzato nell'attributo **EntityType**   .</span><span class="sxs-lookup"><span data-stu-id="52f16-149">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="52f16-150">L'istruzione SQL da eseguire è specificata nell'elemento **DefiningQuery**   .</span><span class="sxs-lookup"><span data-stu-id="52f16-150">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   <span data-ttu-id="52f16-151">Aggiungere l'elemento **EntityType** alla sezione SSDL di edmx.</span><span class="sxs-lookup"><span data-stu-id="52f16-151">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="52f16-152">file come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="52f16-152">file as shown below.</span></span> <span data-ttu-id="52f16-153">Tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="52f16-153">Note the following:</span></span>
    -   <span data-ttu-id="52f16-154">Il valore dell'attributo **Name** corrisponde al valore dell'attributo **EntityType** nell'elemento **EntitySet** precedente, anche se il nome completo del tipo di entità viene utilizzato nell'attributo **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="52f16-154">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="52f16-155">I nomi delle proprietà corrispondono ai nomi di colonna restituiti dall'istruzione SQL nell'elemento **DefiningQuery** (sopra).</span><span class="sxs-lookup"><span data-stu-id="52f16-155">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="52f16-156">In questo esempio la chiave di entità è composta da tre proprietà, per assicurare un valore di chiave univoco.</span><span class="sxs-lookup"><span data-stu-id="52f16-156">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> <span data-ttu-id="52f16-157">Se successivamente si esegue la finestra di dialogo della **procedura guidata Aggiorna modello** , tutte le modifiche apportate al modello di archiviazione, inclusa la definizione di query, verranno sovrascritte.</span><span class="sxs-lookup"><span data-stu-id="52f16-157">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="52f16-158">Aggiungere un tipo di entità al modello</span><span class="sxs-lookup"><span data-stu-id="52f16-158">Add an Entity Type to the Model</span></span>

<span data-ttu-id="52f16-159">In questo passaggio si aggiungerà il tipo di entità al modello concettuale usando la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="52f16-159">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="52f16-160">Tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="52f16-160"> Note the following:</span></span>

-   <span data-ttu-id="52f16-161">Il **nome** dell'entità corrisponde al valore dell'attributo **EntityType** nell'elemento **EntitySet** riportato sopra.</span><span class="sxs-lookup"><span data-stu-id="52f16-161">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="52f16-162">I nomi delle proprietà corrispondono ai nomi di colonna restituiti dall'istruzione SQL nell'elemento **DefiningQuery** precedente.</span><span class="sxs-lookup"><span data-stu-id="52f16-162">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="52f16-163">In questo esempio la chiave di entità è composta da tre proprietà, per assicurare un valore di chiave univoco.</span><span class="sxs-lookup"><span data-stu-id="52f16-163">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="52f16-164">Aprire il modello nella finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="52f16-164">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="52f16-165">Fare doppio clic su DefiningQueryModel. edmx.</span><span class="sxs-lookup"><span data-stu-id="52f16-165">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="52f16-166">Pronunciare **Yes** sul messaggio seguente:</span><span class="sxs-lookup"><span data-stu-id="52f16-166">Say **Yes** to the following message:</span></span>

    ![Avviso 2](~/ef6/media/warning2.png)

 

<span data-ttu-id="52f16-168">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="52f16-168">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="52f16-169">Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Aggiungi nuova**entità... - &gt; **Entity…**.</span><span class="sxs-lookup"><span data-stu-id="52f16-169">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="52f16-170">Specificare **GradeReport** per il nome dell'entità e **CourseID** per la **proprietà chiave**.</span><span class="sxs-lookup"><span data-stu-id="52f16-170">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="52f16-171">Fare clic con il pulsante destro del mouse sull'entità **GradeReport** e scegliere **Aggiungi nuova** - &gt; **proprietà scalare**.</span><span class="sxs-lookup"><span data-stu-id="52f16-171">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="52f16-172">Modificare il nome predefinito della proprietà in **FirstName**.</span><span class="sxs-lookup"><span data-stu-id="52f16-172">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="52f16-173">Aggiungere un'altra proprietà scalare e specificare **LastName** come nome.</span><span class="sxs-lookup"><span data-stu-id="52f16-173">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="52f16-174">Aggiungere un'altra proprietà scalare e specificare **Grade** per il nome.</span><span class="sxs-lookup"><span data-stu-id="52f16-174">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="52f16-175">Nella finestra **Proprietà** modificare la proprietà **Type** del **livello**in **Decimal**.</span><span class="sxs-lookup"><span data-stu-id="52f16-175">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="52f16-176">Selezionare le proprietà **FirstName** e **LastName** .</span><span class="sxs-lookup"><span data-stu-id="52f16-176">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="52f16-177">Nella finestra **Proprietà** impostare il valore della proprietà **EntityKey** su **true**.</span><span class="sxs-lookup"><span data-stu-id="52f16-177">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="52f16-178">Di conseguenza, gli elementi seguenti sono stati aggiunti alla sezione **CSDL** del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="52f16-178">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="52f16-179">Mappare la query di definizione al tipo di entità</span><span class="sxs-lookup"><span data-stu-id="52f16-179">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="52f16-180">In questo passaggio verrà usata la finestra Dettagli mapping per eseguire il mapping dei tipi di entità concettuali e di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="52f16-180">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="52f16-181">Fare clic con il pulsante destro del mouse sull'entità **GradeReport** nell'area di progettazione e scegliere **mapping tabella**.</span><span class="sxs-lookup"><span data-stu-id="52f16-181">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="52f16-182">Verrà visualizzata la finestra **Dettagli mapping** .</span><span class="sxs-lookup"><span data-stu-id="52f16-182">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="52f16-183">Selezionare **GradeReport** nell'elenco \*\* &lt; a discesa Aggiungi tabella o &gt; vista\*\* (disponibile in **tabella**s).</span><span class="sxs-lookup"><span data-stu-id="52f16-183">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="52f16-184">Vengono visualizzati i mapping predefiniti tra il tipo di entità **GradeReport** concettuale e di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="52f16-184">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="52f16-185">![Mapping di Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="52f16-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="52f16-186">Di conseguenza, l'elemento **EntitySetMapping**   viene aggiunto alla sezione di mapping del file con estensione edmx.</span><span class="sxs-lookup"><span data-stu-id="52f16-186">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   <span data-ttu-id="52f16-187">Compilare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52f16-187">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="52f16-188">Chiamare la query di definizione nel codice</span><span class="sxs-lookup"><span data-stu-id="52f16-188">Call the Defining Query in your Code</span></span>

<span data-ttu-id="52f16-189">È ora possibile eseguire la query di definizione usando il tipo di entità **GradeReport** .</span><span class="sxs-lookup"><span data-stu-id="52f16-189">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
