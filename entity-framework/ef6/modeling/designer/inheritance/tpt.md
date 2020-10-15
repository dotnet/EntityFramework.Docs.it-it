---
title: Ereditarietà TPT della finestra di progettazione-EF6
description: Ereditarietà TPT della finestra di progettazione in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066174"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="e41d1-103">Ereditarietà TPT della finestra di progettazione</span><span class="sxs-lookup"><span data-stu-id="e41d1-103">Designer TPT Inheritance</span></span>
<span data-ttu-id="e41d1-104">Questa procedura dettagliata illustra come implementare l'ereditarietà tabella per tipo (TPT) nel modello usando il Entity Framework Designer (EF designer).</span><span class="sxs-lookup"><span data-stu-id="e41d1-104">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e41d1-105">L'ereditarietà tabella per tipo prevede l'utilizzo di una tabella separata nel database per la gestione dei dati relativi alle proprietà non ereditate e alle proprietà chiave di ogni tipo della gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="e41d1-105">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="e41d1-106">In questa procedura dettagliata verrà mappato il **corso** (tipo di base), **OnlineCourse** (deriva dal corso) e **OnsiteCourse**   (deriva dal **corso**) entità alle tabelle con gli stessi nomi.</span><span class="sxs-lookup"><span data-stu-id="e41d1-106">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="e41d1-107">Verrà creato un modello dal database e quindi verrà modificato il modello per implementare l'ereditarietà TPT.</span><span class="sxs-lookup"><span data-stu-id="e41d1-107">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="e41d1-108">È anche possibile iniziare con il Model First e quindi generare il database dal modello.</span><span class="sxs-lookup"><span data-stu-id="e41d1-108">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="e41d1-109">Per impostazione predefinita, in Entity Framework Designer viene utilizzata la strategia TPT, pertanto viene eseguito il mapping di qualsiasi ereditarietà nel modello a tabelle separate.</span><span class="sxs-lookup"><span data-stu-id="e41d1-109">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="e41d1-110">Altre opzioni di ereditarietà</span><span class="sxs-lookup"><span data-stu-id="e41d1-110">Other Inheritance Options</span></span>

<span data-ttu-id="e41d1-111">Tabella per gerarchia (TPH) è un altro tipo di ereditarietà in cui una tabella di database viene utilizzata per gestire i dati per tutti i tipi di entità in una gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="e41d1-111">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span><span data-ttu-id="e41d1-112">Per informazioni su come eseguire il mapping dell'ereditarietà tabella per gerarchia con la Entity Designer, vedere l' [ereditarietà TPH](xref:ef6/modeling/designer/inheritance/tph)di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e41d1-112">  For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](xref:ef6/modeling/designer/inheritance/tph).</span></span> 

<span data-ttu-id="e41d1-113">Si noti che i modelli di ereditarietà della tabella per tipo concreto (TPC) e di ereditarietà mista sono supportati dal runtime di Entity Framework ma non sono supportati dalla finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="e41d1-113">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="e41d1-114">Se si vuole usare TPC o l'ereditarietà mista, sono disponibili due opzioni: usare Code First o modificare manualmente il file EDMX.</span><span class="sxs-lookup"><span data-stu-id="e41d1-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="e41d1-115">Se si sceglie di utilizzare il file EDMX, la finestra Dettagli mapping verrà inserita in "modalità provvisoria" e non sarà possibile utilizzare la finestra di progettazione per modificare i mapping.</span><span class="sxs-lookup"><span data-stu-id="e41d1-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e41d1-116">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e41d1-116">Prerequisites</span></span>

<span data-ttu-id="e41d1-117">Per completare questa procedura dettagliata, è necessario disporre di:</span><span class="sxs-lookup"><span data-stu-id="e41d1-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="e41d1-118">Una versione recente di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e41d1-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="e41d1-119">[Database di esempio School](xref:ef6/resources/school-database).</span><span class="sxs-lookup"><span data-stu-id="e41d1-119">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e41d1-120">Configurare il progetto</span><span class="sxs-lookup"><span data-stu-id="e41d1-120">Set up the Project</span></span>

-   <span data-ttu-id="e41d1-121">Aprire Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="e41d1-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="e41d1-122">Selezionare **file- &gt; nuovo- &gt; progetto**</span><span class="sxs-lookup"><span data-stu-id="e41d1-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="e41d1-123">Nel riquadro sinistro fare clic su \*\*Visual C \# \*\*e quindi selezionare il modello **console** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="e41d1-124">Immettere **TPTDBFirstSample**   come nome.</span><span class="sxs-lookup"><span data-stu-id="e41d1-124">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="e41d1-125">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="e41d1-126">Creare il modello</span><span class="sxs-lookup"><span data-stu-id="e41d1-126">Create a Model</span></span>

-   <span data-ttu-id="e41d1-127">Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-127">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="e41d1-128">Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.</span><span class="sxs-lookup"><span data-stu-id="e41d1-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="e41d1-129">Immettere **TPTModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-129">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="e41d1-130">Nella finestra di dialogo Scegli contenuto Model selezionare\*\*   genera da database**, quindi fare clic su **Avanti\*\*.</span><span class="sxs-lookup"><span data-stu-id="e41d1-130">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="e41d1-131">Fare clic su **nuova connessione**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-131">Click **New Connection**.</span></span>
    <span data-ttu-id="e41d1-132">Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="e41d1-133">La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.</span><span class="sxs-lookup"><span data-stu-id="e41d1-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="e41d1-134">Nella finestra di dialogo Scegli oggetti di database, nel nodo tabelle, selezionare le tabelle **Department**, **Course, OnlineCourse e OnsiteCourse** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="e41d1-135">Fare clic su **fine**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-135">Click **Finish**.</span></span>

<span data-ttu-id="e41d1-136">Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.</span><span class="sxs-lookup"><span data-stu-id="e41d1-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="e41d1-137">Tutti gli oggetti selezionati nella finestra di dialogo Scegli oggetti di database vengono aggiunti al modello.</span><span class="sxs-lookup"><span data-stu-id="e41d1-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="e41d1-138">Implementare l'ereditarietà tabella per tipo</span><span class="sxs-lookup"><span data-stu-id="e41d1-138">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="e41d1-139">Nell'area di progettazione fare clic con il pulsante destro del mouse sul tipo di entità **OnlineCourse** e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-139">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e41d1-140">Nella finestra **Proprietà** impostare la proprietà tipo di base su **Course**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-140">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e41d1-141">Fare clic con il pulsante destro del mouse sul tipo di entità **OnsiteCourse** e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-141">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e41d1-142">Nella finestra **Proprietà** impostare la proprietà tipo di base su **Course**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-142">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e41d1-143">Fare clic con il pulsante destro del mouse sull'associazione (la riga) tra i tipi di entità **OnlineCourse** e **Course** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-143">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e41d1-144">Selezionare **Elimina dal modello**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-144">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="e41d1-145">Fare clic con il pulsante destro del mouse sull'associazione tra i tipi di entità **OnsiteCourse** e **Course** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-145">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e41d1-146">Selezionare **Elimina dal modello**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-146">Select **Delete from Model**.</span></span>

<span data-ttu-id="e41d1-147">A questo punto si eliminerà la proprietà **CourseID** da **OnlineCourse** e **OnsiteCourse** , perché queste classi ereditano **CourseID** dal tipo di base **Course** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-147">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="e41d1-148">Fare clic con il pulsante destro del mouse sulla proprietà **CourseID** del tipo di entità **OnlineCourse** e quindi scegliere **Elimina dal modello**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-148">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="e41d1-149">Fare clic con il pulsante destro del mouse sulla proprietà **CourseID** del tipo di entità **OnsiteCourse** e quindi scegliere **Elimina dal modello** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-149">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="e41d1-150">A questo punto l'ereditarietà tabella per tipo risulta implementata.</span><span class="sxs-lookup"><span data-stu-id="e41d1-150">Table-per-type inheritance is now implemented.</span></span>

![Tabella per tipo](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="e41d1-152">Usare il modello</span><span class="sxs-lookup"><span data-stu-id="e41d1-152">Use the Model</span></span>

<span data-ttu-id="e41d1-153">Aprire il file **Program.cs** in cui è definito il metodo **Main** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-153">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="e41d1-154">Incollare il codice seguente nella funzione **Main** .</span><span class="sxs-lookup"><span data-stu-id="e41d1-154">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="e41d1-155">Il codice esegue tre query.</span><span class="sxs-lookup"><span data-stu-id="e41d1-155">The code executes three queries.</span></span> <span data-ttu-id="e41d1-156">La prima query riporta tutti i **corsi** correlati al reparto specificato.</span><span class="sxs-lookup"><span data-stu-id="e41d1-156">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="e41d1-157">La seconda query usa il metodo **OfType** per restituire **OnlineCourses** correlati al reparto specificato.</span><span class="sxs-lookup"><span data-stu-id="e41d1-157">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="e41d1-158">La terza query restituisce **OnsiteCourses**.</span><span class="sxs-lookup"><span data-stu-id="e41d1-158">The third query returns **OnsiteCourses**.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
