---
title: Suddivisione delle entità della finestra di progettazione-EF6
description: Suddivisione delle entità della finestra di progettazione in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: fa25629bae1a35b2d792e2b890e87db6c99c37ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073136"
---
# <a name="designer-entity-splitting"></a><span data-ttu-id="95175-103">Suddivisione delle entità della finestra di progettazione</span><span class="sxs-lookup"><span data-stu-id="95175-103">Designer Entity Splitting</span></span>
<span data-ttu-id="95175-104">Questa procedura dettagliata illustra come eseguire il mapping di un tipo di entità a due tabelle modificando un modello con il Entity Framework Designer (EF designer).</span><span class="sxs-lookup"><span data-stu-id="95175-104">This walkthrough shows how to map an entity type to two tables by modifying a model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="95175-105">È possibile eseguire il mapping di un'entità a più tabelle quando le tabelle in questione condividono una chiave comune.</span><span class="sxs-lookup"><span data-stu-id="95175-105">You can map an entity to multiple tables when the tables share a common key.</span></span> <span data-ttu-id="95175-106">I concetti relativi all'esecuzione del mapping di un tipo di entità a due tabelle possono essere facilmente estesi anche all'esecuzione del mapping a più di due tabelle.</span><span class="sxs-lookup"><span data-stu-id="95175-106">The concepts that apply to mapping an entity type to two tables are easily extended to mapping an entity type to more than two tables.</span></span>

<span data-ttu-id="95175-107">Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="95175-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="95175-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="95175-109">Prerequisites</span></span>

<span data-ttu-id="95175-110">Visual Studio 2012 o Visual Studio 2010, Ultimate, Premium, Professional o Web Express Edition.</span><span class="sxs-lookup"><span data-stu-id="95175-110">Visual Studio 2012 or Visual Studio 2010, Ultimate, Premium, Professional, or Web Express edition.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="95175-111">Creare il database</span><span class="sxs-lookup"><span data-stu-id="95175-111">Create the Database</span></span>

<span data-ttu-id="95175-112">Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:</span><span class="sxs-lookup"><span data-stu-id="95175-112">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="95175-113">Se si usa Visual Studio 2012, si creerà un database del database locale.</span><span class="sxs-lookup"><span data-stu-id="95175-113">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="95175-114">Se si usa Visual Studio 2010 verrà creato un database di SQL Express.</span><span class="sxs-lookup"><span data-stu-id="95175-114">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="95175-115">Verrà innanzitutto creato un database con due tabelle che verranno combinate in una singola entità.</span><span class="sxs-lookup"><span data-stu-id="95175-115">First we'll create a database with two tables that we are going to combine into a single entity.</span></span>

-   <span data-ttu-id="95175-116">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95175-116">Open Visual Studio</span></span>
-   <span data-ttu-id="95175-117">**Visualizza- &gt; Esplora server**</span><span class="sxs-lookup"><span data-stu-id="95175-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="95175-118">Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**</span><span class="sxs-lookup"><span data-stu-id="95175-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="95175-119">Se non si è connessi a un database da Esplora server prima di selezionare **Microsoft SQL Server** come origine dati</span><span class="sxs-lookup"><span data-stu-id="95175-119">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="95175-120">Connettersi a un database locale o a SQL Express, a seconda di quale installato</span><span class="sxs-lookup"><span data-stu-id="95175-120">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="95175-121">Immettere **EntitySplitting** come nome del database</span><span class="sxs-lookup"><span data-stu-id="95175-121">Enter **EntitySplitting** as the database name</span></span>
-   <span data-ttu-id="95175-122">Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="95175-122">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="95175-123">Il nuovo database verrà ora visualizzato in Esplora server</span><span class="sxs-lookup"><span data-stu-id="95175-123">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="95175-124">Se si usa Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="95175-124">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="95175-125">Fare clic con il pulsante destro del mouse sul database in Esplora server e scegliere **Nuova query**</span><span class="sxs-lookup"><span data-stu-id="95175-125">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="95175-126">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="95175-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="95175-127">Se si usa Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="95175-127">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="95175-128">Selezione **dati- &gt; Editor Transact-SQL- &gt; nuova connessione query...**</span><span class="sxs-lookup"><span data-stu-id="95175-128">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="95175-129">Immettere **. \\ SQLEXPRESS** come nome del server e fare clic su **OK**</span><span class="sxs-lookup"><span data-stu-id="95175-129">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="95175-130">Selezionare il database **EntitySplitting** dall'elenco a discesa nella parte superiore dell'editor di query</span><span class="sxs-lookup"><span data-stu-id="95175-130">Select the **EntitySplitting** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="95175-131">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui SQL**</span><span class="sxs-lookup"><span data-stu-id="95175-131">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a><span data-ttu-id="95175-132">Creare il progetto</span><span class="sxs-lookup"><span data-stu-id="95175-132">Create the Project</span></span>

-   <span data-ttu-id="95175-133">Scegliere **Nuovo** dal menu **File**e quindi fare clic su **Progetto**.</span><span class="sxs-lookup"><span data-stu-id="95175-133">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="95175-134">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **applicazione console** .</span><span class="sxs-lookup"><span data-stu-id="95175-134">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="95175-135">Immettere **MapEntityToTablesSample** come nome del progetto e fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="95175-135">Enter **MapEntityToTablesSample** as the name of the project and click **OK**.</span></span>
-   <span data-ttu-id="95175-136">Fare clic su **No** se viene richiesto di salvare la query SQL creata nella prima sezione.</span><span class="sxs-lookup"><span data-stu-id="95175-136">Click **No** if prompted to save the SQL query created in the first section.</span></span>

## <a name="create-a-model-based-on-the-database"></a><span data-ttu-id="95175-137">Creazione di un modello basato sul database</span><span class="sxs-lookup"><span data-stu-id="95175-137">Create a Model based on the Database</span></span>

-   <span data-ttu-id="95175-138">Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="95175-138">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="95175-139">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="95175-139">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="95175-140">Immettere **MapEntityToTablesModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="95175-140">Enter **MapEntityToTablesModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="95175-141">Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti.**</span><span class="sxs-lookup"><span data-stu-id="95175-141">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="95175-142">Selezionare la connessione **EntitySplitting** dall'elenco a discesa e fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="95175-142">Select the **EntitySplitting** connection from the drop down and click **Next**.</span></span>
-   <span data-ttu-id="95175-143">Nella finestra di dialogo Scegli oggetti di database selezionare la casella accanto al nodo **tabelle**   .</span><span class="sxs-lookup"><span data-stu-id="95175-143">In the Choose Your Database Objects dialog box, check the box next to the **Tables** node.</span></span>
    <span data-ttu-id="95175-144">In questo modo, tutte le tabelle del database **EntitySplitting** vengono aggiunte al modello.</span><span class="sxs-lookup"><span data-stu-id="95175-144">This will add all the tables from the **EntitySplitting** database to the model.</span></span>
-   <span data-ttu-id="95175-145">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="95175-145">Click **Finish**.</span></span>

<span data-ttu-id="95175-146">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="95175-146">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-an-entity-to-two-tables"></a><span data-ttu-id="95175-147">Eseguire il mapping di un'entità a due tabelle</span><span class="sxs-lookup"><span data-stu-id="95175-147">Map an Entity to Two Tables</span></span>

<span data-ttu-id="95175-148">In questo passaggio verrà aggiornato il tipo di entità **Person** per combinare i dati delle tabelle **Person** e **PersonInfo** .</span><span class="sxs-lookup"><span data-stu-id="95175-148">In this step we will update the **Person** entity type to combine data from the **Person** and **PersonInfo** tables.</span></span>

-   <span data-ttu-id="95175-149">Selezionare le proprietà di **posta elettronica**   e **telefono** dell'entità **PersonInfo **e premere **CTRL + X** .</span><span class="sxs-lookup"><span data-stu-id="95175-149">Select the **Email** and **Phone** properties of the **PersonInfo **entity and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="95175-150">Selezionare l'entità **Person **e premere **CTRL + V** .</span><span class="sxs-lookup"><span data-stu-id="95175-150">Select the **Person **entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="95175-151">Nell'area di progettazione selezionare l'entità **PersonInfo**   e premere il pulsante **Elimina** sulla tastiera.</span><span class="sxs-lookup"><span data-stu-id="95175-151">On the design surface, select the **PersonInfo** entity and press **Delete** button on the keyboard.</span></span>
-   <span data-ttu-id="95175-152">Fare clic su **No** quando viene richiesto se si desidera rimuovere la tabella **PersonInfo** dal modello, si sta per eseguirne il mapping all'entità **Person** .</span><span class="sxs-lookup"><span data-stu-id="95175-152">Click **No** when asked if you want to remove the **PersonInfo** table from the model, we are about to map it to the **Person** entity.</span></span>

    ![Elimina tabelle](~/ef6/media/deletetables.png)

<span data-ttu-id="95175-154">Per i passaggi successivi è necessaria la finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="95175-154">The next steps require the **Mapping Details** window.</span></span> <span data-ttu-id="95175-155">Se questa finestra non è visibile, fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Dettagli mapping**.</span><span class="sxs-lookup"><span data-stu-id="95175-155">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="95175-156">Selezionare il **Person**   tipo di entità Person e fare clic su \*\* &lt; Aggiungi tabella &gt; o vista\*\*   nella finestra **Dettagli mapping**   .</span><span class="sxs-lookup"><span data-stu-id="95175-156">Select the **Person** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="95175-157">Selezionare **PersonInfo **   nell'elenco a discesa.</span><span class="sxs-lookup"><span data-stu-id="95175-157">Select **PersonInfo ** from the drop-down list.</span></span>
    <span data-ttu-id="95175-158">La finestra **Dettagli mapping**   viene aggiornata con i mapping predefiniti delle colonne, per questo scenario.</span><span class="sxs-lookup"><span data-stu-id="95175-158">The **Mapping Details** window is updated with default column mappings, these are fine for our scenario.</span></span>

<span data-ttu-id="95175-159">Il **Person**   tipo di entità Person viene ora mappato alle tabelle **Person**   e **PersonInfo**   .</span><span class="sxs-lookup"><span data-stu-id="95175-159">The **Person** entity type is now mapped to the **Person** and **PersonInfo** tables.</span></span>

![Mapping 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a><span data-ttu-id="95175-161">Usare il modello</span><span class="sxs-lookup"><span data-stu-id="95175-161">Use the Model</span></span>

-   <span data-ttu-id="95175-162">Incollare il codice seguente nel metodo Main.</span><span class="sxs-lookup"><span data-stu-id="95175-162">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   <span data-ttu-id="95175-163">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="95175-163">Compile and run the application.</span></span>

<span data-ttu-id="95175-164">Le istruzioni T-SQL seguenti sono state eseguite sul database in seguito all'esecuzione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="95175-164">The following T-SQL statements were executed against the database as a result of running this application.</span></span> 

-   <span data-ttu-id="95175-165">Le due istruzioni **Insert** seguenti sono state eseguite in seguito all'esecuzione del contesto. SaveChanges ().</span><span class="sxs-lookup"><span data-stu-id="95175-165">The following two **INSERT** statements were executed as a result of executing context.SaveChanges().</span></span> <span data-ttu-id="95175-166">I dati vengono presi dall'entità **Person** e suddivisi tra le tabelle **Person** e **PersonInfo** .</span><span class="sxs-lookup"><span data-stu-id="95175-166">They take the data from the **Person** entity and split it between the **Person** and **PersonInfo** tables.</span></span>

    ![Inserisci 1](~/ef6/media/insert1.png)

    ![Inserisci 2](~/ef6/media/insert2.png)
-   <span data-ttu-id="95175-169">La seguente **selezione** è stata eseguita in seguito all'enumerazione degli utenti nel database.</span><span class="sxs-lookup"><span data-stu-id="95175-169">The following **SELECT** was executed as a result of enumerating the people in the database.</span></span> <span data-ttu-id="95175-170">Combina i dati della tabella **Person** e **PersonInfo** .</span><span class="sxs-lookup"><span data-stu-id="95175-170">It combines the data from the **Person** and **PersonInfo** table.</span></span>

    ![Selezionare la combinazione di dati Person e PersonInfo](~/ef6/media/select.png)
