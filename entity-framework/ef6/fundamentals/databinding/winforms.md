---
title: DataBinding con WinForms-EF6
description: DataBinding con WinForms in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 80fc5062-2f1c-4dbd-ab6e-b99496784b36
uid: ef6/fundamentals/databinding/winforms
ms.openlocfilehash: 934ea2ae488a7df128746a0607037d85e9b23771
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616346"
---
# <a name="databinding-with-winforms"></a><span data-ttu-id="bf546-103">Data binding con WinForms</span><span class="sxs-lookup"><span data-stu-id="bf546-103">Databinding with WinForms</span></span>
<span data-ttu-id="bf546-104">Questa procedura dettagliata illustra come associare tipi POCO a controlli Windows Form (WinForms) in un modulo "Master-Detail".</span><span class="sxs-lookup"><span data-stu-id="bf546-104">This step-by-step walkthrough shows how to bind POCO types to Window Forms (WinForms) controls in a “master-detail" form.</span></span> <span data-ttu-id="bf546-105">L'applicazione utilizza Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="bf546-105">The application uses Entity Framework to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="bf546-106">Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: categoria ( \\ master principale) e prodotto ( \\ Dettagli dipendenti).</span><span class="sxs-lookup"><span data-stu-id="bf546-106">The model defines two types that participate in one-to-many relationship: Category (principal\\master) and Product (dependent\\detail).</span></span> <span data-ttu-id="bf546-107">Gli strumenti di Visual Studio vengono quindi utilizzati per associare i tipi definiti nel modello ai controlli WinForms.</span><span class="sxs-lookup"><span data-stu-id="bf546-107">Then, the Visual Studio tools are used to bind the types defined in the model to the WinForms controls.</span></span> <span data-ttu-id="bf546-108">Il Framework di associazione dati di WinForms consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="bf546-108">The WinForms data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="bf546-109">Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2013 ma è possibile completare questa procedura dettagliata con Visual Studio 2012 o Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="bf546-109">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="bf546-110">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="bf546-110">Pre-Requisites</span></span>

<span data-ttu-id="bf546-111">Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="bf546-111">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="bf546-112">Se si usa Visual Studio 2010, è anche necessario installare NuGet.</span><span class="sxs-lookup"><span data-stu-id="bf546-112">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="bf546-113">Per altre informazioni, vedere [installazione di NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span><span class="sxs-lookup"><span data-stu-id="bf546-113">For more information, see [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="bf546-114">Creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="bf546-114">Create the Application</span></span>

-   <span data-ttu-id="bf546-115">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bf546-115">Open Visual Studio</span></span>
-   <span data-ttu-id="bf546-116">**File- &gt; nuovo- &gt; progetto...**</span><span class="sxs-lookup"><span data-stu-id="bf546-116">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="bf546-117">Selezionare **Windows** nel riquadro sinistro e **Windows FormsApplication** nel riquadro a destra.</span><span class="sxs-lookup"><span data-stu-id="bf546-117">Select **Windows** in the left pane and **Windows FormsApplication** in the right pane</span></span>
-   <span data-ttu-id="bf546-118">Immettere **WinFormswithEFSample** come nome</span><span class="sxs-lookup"><span data-stu-id="bf546-118">Enter **WinFormswithEFSample** as the name</span></span>
-   <span data-ttu-id="bf546-119">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="bf546-119">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="bf546-120">Installare il pacchetto NuGet Entity Framework</span><span class="sxs-lookup"><span data-stu-id="bf546-120">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="bf546-121">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WinFormswithEFSample**</span><span class="sxs-lookup"><span data-stu-id="bf546-121">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="bf546-122">Selezionare **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="bf546-122">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="bf546-123">Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="bf546-123">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="bf546-124">Fai clic su **Install** (Installa).</span><span class="sxs-lookup"><span data-stu-id="bf546-124">Click **Install**</span></span>  
    > [!NOTE]
    > <span data-ttu-id="bf546-125">Oltre all'assembly EntityFramework, viene aggiunto anche un riferimento a System. ComponentModel. DataAnnotations.</span><span class="sxs-lookup"><span data-stu-id="bf546-125">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="bf546-126">Se il progetto contiene un riferimento a System. Data. Entity, verrà rimosso al momento dell'installazione del pacchetto EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="bf546-126">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="bf546-127">L'assembly System. Data. Entity non viene più usato per le applicazioni Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="bf546-127">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="implementing-ilistsource-for-collections"></a><span data-ttu-id="bf546-128">Implementazione di IListSource per le raccolte</span><span class="sxs-lookup"><span data-stu-id="bf546-128">Implementing IListSource for Collections</span></span>

<span data-ttu-id="bf546-129">Le proprietà della raccolta devono implementare l'interfaccia IListSource per consentire l'data binding bidirezionale con l'ordinamento quando si usa Windows Forms.</span><span class="sxs-lookup"><span data-stu-id="bf546-129">Collection properties must implement the IListSource interface to enable two-way data binding with sorting when using Windows Forms.</span></span> <span data-ttu-id="bf546-130">A tale scopo, si estenderà l'oggetto ObservableCollection per aggiungere la funzionalità IListSource.</span><span class="sxs-lookup"><span data-stu-id="bf546-130">To do this we are going to extend ObservableCollection to add IListSource functionality.</span></span>

-   <span data-ttu-id="bf546-131">Aggiungere una classe **ObservableListSource** al progetto:</span><span class="sxs-lookup"><span data-stu-id="bf546-131">Add an **ObservableListSource** class to the project:</span></span>
    -   <span data-ttu-id="bf546-132">Fare clic con il pulsante destro del mouse sul nome del progetto</span><span class="sxs-lookup"><span data-stu-id="bf546-132">Right-click on the project name</span></span>
    -   <span data-ttu-id="bf546-133">Selezionare **Aggiungi- &gt; nuovo elemento**</span><span class="sxs-lookup"><span data-stu-id="bf546-133">Select **Add -&gt; New Item**</span></span>
    -   <span data-ttu-id="bf546-134">Selezionare **classe** e immettere **ObservableListSource** per nome classe</span><span class="sxs-lookup"><span data-stu-id="bf546-134">Select **Class** and enter **ObservableListSource** for the class name</span></span>
-   <span data-ttu-id="bf546-135">Sostituire il codice generato per impostazione predefinita con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bf546-135">Replace the code generated by default with the following code:</span></span>

<span data-ttu-id="bf546-136">*Questa classe consente la data binding bidirezionale e l'ordinamento. La classe deriva da ObservableCollection &lt; T &gt; e aggiunge un'implementazione esplicita di IListSource. Il metodo GetList () di IListSource viene implementato per restituire un'implementazione di IBindingList che rimane sincronizzata con l'oggetto ObservableCollection. L'implementazione di IBindingList generata da tobindings supporta l'ordinamento. Il metodo di estensione tobinding è definito nell'assembly EntityFramework.*</span><span class="sxs-lookup"><span data-stu-id="bf546-136">*This class enables two-way data binding as well as sorting. The class derives from ObservableCollection&lt;T&gt; and adds an explicit implementation of IListSource. The GetList() method of IListSource is implemented to return an IBindingList implementation that stays in sync with the ObservableCollection. The IBindingList implementation generated by ToBindingList supports sorting. The ToBindingList extension method is defined in the EntityFramework assembly.*</span></span>

``` csharp
    using System.Collections;
    using System.Collections.Generic;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Diagnostics.CodeAnalysis;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public class ObservableListSource<T> : ObservableCollection<T>, IListSource
            where T : class
        {
            private IBindingList _bindingList;

            bool IListSource.ContainsListCollection { get { return false; } }

            IList IListSource.GetList()
            {
                return _bindingList ?? (_bindingList = this.ToBindingList());
            }
        }
    }
```

## <a name="define-a-model"></a><span data-ttu-id="bf546-137">Definire un modello</span><span class="sxs-lookup"><span data-stu-id="bf546-137">Define a Model</span></span>

<span data-ttu-id="bf546-138">In questa procedura dettagliata è possibile scegliere di implementare un modello usando Code First o la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="bf546-138">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="bf546-139">Completare una delle due sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="bf546-139">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="bf546-140">Opzione 1: definire un modello utilizzando Code First</span><span class="sxs-lookup"><span data-stu-id="bf546-140">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="bf546-141">In questa sezione viene illustrato come creare un modello e il relativo database associato utilizzando Code First.</span><span class="sxs-lookup"><span data-stu-id="bf546-141">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="bf546-142">Passare alla sezione successiva (**opzione 2: definire un modello usando database First)** se si preferisce usare database First per decompilare il modello da un database usando EF designer</span><span class="sxs-lookup"><span data-stu-id="bf546-142">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="bf546-143">Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio).</span><span class="sxs-lookup"><span data-stu-id="bf546-143">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="bf546-144">Aggiungere una nuova classe **prodotto** al progetto</span><span class="sxs-lookup"><span data-stu-id="bf546-144">Add a new **Product** class to project</span></span>
-   <span data-ttu-id="bf546-145">Sostituire il codice generato per impostazione predefinita con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bf546-145">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }
```

-   <span data-ttu-id="bf546-146">Aggiungere una classe **Category** al progetto.</span><span class="sxs-lookup"><span data-stu-id="bf546-146">Add a **Category** class to the project.</span></span>
-   <span data-ttu-id="bf546-147">Sostituire il codice generato per impostazione predefinita con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bf546-147">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace WinFormswithEFSample
    {
        public class Category
        {
            private readonly ObservableListSource<Product> _products =
                    new ObservableListSource<Product>();

            public int CategoryId { get; set; }
            public string Name { get; set; }
            public virtual ObservableListSource<Product> Products { get { return _products; } }
        }
    }
```

<span data-ttu-id="bf546-148">Oltre alla definizione delle entità, è necessario definire una classe che deriva da **DbContext** ed espone le proprietà **di &lt; DbSet &gt; TEntity** .</span><span class="sxs-lookup"><span data-stu-id="bf546-148">In addition to defining entities, you need to define a class that derives from **DbContext** and exposes **DbSet&lt;TEntity&gt;** properties.</span></span> <span data-ttu-id="bf546-149">Le proprietà **DbSet** consentono al contesto di individuare i tipi che si desidera includere nel modello.</span><span class="sxs-lookup"><span data-stu-id="bf546-149">The **DbSet** properties let the context know which types you want to include in the model.</span></span> <span data-ttu-id="bf546-150">I tipi **DbContext** e **DbSet** sono definiti nell'assembly EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="bf546-150">The **DbContext** and **DbSet** types are defined in the EntityFramework assembly.</span></span>

<span data-ttu-id="bf546-151">Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.</span><span class="sxs-lookup"><span data-stu-id="bf546-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="bf546-152">Aggiungere una nuova classe **ProductContext** al progetto.</span><span class="sxs-lookup"><span data-stu-id="bf546-152">Add a new **ProductContext** class to the project.</span></span>
-   <span data-ttu-id="bf546-153">Sostituire il codice generato per impostazione predefinita con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="bf546-153">Replace the code generated by default with the following code:</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;
    using System.Text;

    namespace WinFormswithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="bf546-154">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="bf546-154">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="bf546-155">Opzione 2: definire un modello utilizzando Database First</span><span class="sxs-lookup"><span data-stu-id="bf546-155">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="bf546-156">Questa sezione illustra come usare Database First per decompilare il modello da un database usando la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="bf546-156">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="bf546-157">Se è stata completata la sezione precedente (**opzione 1: definire un modello con Code First)**, ignorare questa sezione e passare direttamente alla sezione **caricamento lazy** .</span><span class="sxs-lookup"><span data-stu-id="bf546-157">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="bf546-158">Creare un database esistente</span><span class="sxs-lookup"><span data-stu-id="bf546-158">Create an Existing Database</span></span>

<span data-ttu-id="bf546-159">In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.</span><span class="sxs-lookup"><span data-stu-id="bf546-159">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="bf546-160">Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:</span><span class="sxs-lookup"><span data-stu-id="bf546-160">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="bf546-161">Se si usa Visual Studio 2010 verrà creato un database di SQL Express.</span><span class="sxs-lookup"><span data-stu-id="bf546-161">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="bf546-162">Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202.aspx) .</span><span class="sxs-lookup"><span data-stu-id="bf546-162">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="bf546-163">Procediamo con la generazione del database.</span><span class="sxs-lookup"><span data-stu-id="bf546-163">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="bf546-164">**Visualizza- &gt; Esplora server**</span><span class="sxs-lookup"><span data-stu-id="bf546-164">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="bf546-165">Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**</span><span class="sxs-lookup"><span data-stu-id="bf546-165">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="bf546-166">Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati</span><span class="sxs-lookup"><span data-stu-id="bf546-166">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Modifica origine dati](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="bf546-168">Connettersi al database locale o a SQL Express, a seconda di quale installato e immettere i **prodotti** come nome del database</span><span class="sxs-lookup"><span data-stu-id="bf546-168">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![Aggiungere la connessione al database locale](~/ef6/media/addconnectionlocaldb.png)

    ![Aggiungi connessione rapida](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="bf546-171">Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="bf546-171">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Create Database](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="bf546-173">Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .</span><span class="sxs-lookup"><span data-stu-id="bf546-173">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="bf546-174">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="bf546-174">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="bf546-175">Decodificare il modello</span><span class="sxs-lookup"><span data-stu-id="bf546-175">Reverse Engineer Model</span></span>

<span data-ttu-id="bf546-176">Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bf546-176">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="bf546-177">**Progetto- &gt; Aggiungi nuovo elemento...**</span><span class="sxs-lookup"><span data-stu-id="bf546-177">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="bf546-178">Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="bf546-178">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="bf546-179">Immettere **ProductModel** come nome e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="bf546-179">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="bf546-180">Viene avviata la **procedura guidata Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="bf546-180">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="bf546-181">Selezionare **genera da database** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="bf546-181">Select **Generate from Database** and click **Next**</span></span>

    ![Scelta dei contenuti del modello](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="bf546-183">Selezionare la connessione al database creato nella prima sezione, immettere **ProductContext** come nome della stringa di connessione e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="bf546-183">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![Scegliere la connessione](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="bf546-185">Fare clic sulla casella di controllo accanto a' Tables ' per importare tutte le tabelle e fare clic su' Finish '</span><span class="sxs-lookup"><span data-stu-id="bf546-185">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Scegliere gli oggetti](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="bf546-187">Una volta completato il processo di Reverse Engineering, il nuovo modello viene aggiunto al progetto e aperto per la visualizzazione nel Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="bf546-187">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="bf546-188">Al progetto è stato aggiunto anche un file di App.config con i dettagli della connessione per il database.</span><span class="sxs-lookup"><span data-stu-id="bf546-188">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="bf546-189">Passaggi aggiuntivi in Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="bf546-189">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="bf546-190">Se si lavora in Visual Studio 2010, sarà necessario aggiornare la finestra di progettazione di Entity Framework per l'uso della generazione di codice EF6.</span><span class="sxs-lookup"><span data-stu-id="bf546-190">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="bf546-191">Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**</span><span class="sxs-lookup"><span data-stu-id="bf546-191">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="bf546-192">Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**</span><span class="sxs-lookup"><span data-stu-id="bf546-192">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="bf546-193">Selezionare il **Generatore EF 6. x DbContext per C \# ,** immettere **ProductsModel** come nome e fare clic su Aggiungi.</span><span class="sxs-lookup"><span data-stu-id="bf546-193">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="bf546-194">Aggiornamento della generazione di codice per data binding</span><span class="sxs-lookup"><span data-stu-id="bf546-194">Updating code generation for data binding</span></span>

<span data-ttu-id="bf546-195">EF genera codice dal modello usando i modelli T4.</span><span class="sxs-lookup"><span data-stu-id="bf546-195">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="bf546-196">I modelli forniti con Visual Studio o scaricati da Visual Studio Gallery sono destinati all'uso generico.</span><span class="sxs-lookup"><span data-stu-id="bf546-196">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="bf546-197">Questo significa che le entità generate da questi modelli hanno &lt; Proprietà ICollection T semplici &gt; .</span><span class="sxs-lookup"><span data-stu-id="bf546-197">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="bf546-198">Tuttavia, quando si esegue data binding è preferibile disporre di proprietà di raccolta che implementino IListSource.</span><span class="sxs-lookup"><span data-stu-id="bf546-198">However, when doing data binding it is desirable to have collection properties that implement IListSource.</span></span> <span data-ttu-id="bf546-199">Questo è il motivo per cui è stata creata la classe ObservableListSource precedente e ora verranno modificati i modelli per usare questa classe.</span><span class="sxs-lookup"><span data-stu-id="bf546-199">This is why we created the ObservableListSource class above and we are now going to modify the templates to make use of this class.</span></span>

-   <span data-ttu-id="bf546-200">Aprire il **Esplora soluzioni** e trovare il file **ProductModel. edmx**</span><span class="sxs-lookup"><span data-stu-id="bf546-200">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="bf546-201">Trovare il file **ProductModel.TT** che verrà annidato nel file ProductModel. edmx</span><span class="sxs-lookup"><span data-stu-id="bf546-201">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![Modello del modello di prodotto](~/ef6/media/productmodeltemplate.png)

-   <span data-ttu-id="bf546-203">Fare doppio clic sul file ProductModel.tt per aprirlo nell'editor di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf546-203">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="bf546-204">Trovare e sostituire le due occorrenze di "**ICollection**" con "**ObservableListSource**".</span><span class="sxs-lookup"><span data-stu-id="bf546-204">Find and replace the two occurrences of “**ICollection**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="bf546-205">Si trovano a circa linee 296 e 484.</span><span class="sxs-lookup"><span data-stu-id="bf546-205">These are located at approximately lines 296 and 484.</span></span>
-   <span data-ttu-id="bf546-206">Trovare e sostituire la prima occorrenza di "**HashSet**" con "**ObservableListSource**".</span><span class="sxs-lookup"><span data-stu-id="bf546-206">Find and replace the first occurrence of “**HashSet**” with “**ObservableListSource**”.</span></span> <span data-ttu-id="bf546-207">Questa occorrenza si trova approssimativamente alla riga 50.</span><span class="sxs-lookup"><span data-stu-id="bf546-207">This occurrence is located at approximately line 50.</span></span> <span data-ttu-id="bf546-208">**Non sostituire la** seconda occorrenza di HashSet rilevata successivamente nel codice.</span><span class="sxs-lookup"><span data-stu-id="bf546-208">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="bf546-209">Salvare il file ProductModel.tt.</span><span class="sxs-lookup"><span data-stu-id="bf546-209">Save the ProductModel.tt file.</span></span> <span data-ttu-id="bf546-210">Questa operazione dovrebbe causare la rigenerazione del codice per le entità.</span><span class="sxs-lookup"><span data-stu-id="bf546-210">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="bf546-211">Se il codice non viene rigenerato automaticamente, fare clic con il pulsante destro del mouse su ProductModel.tt e scegliere "Esegui strumento personalizzato".</span><span class="sxs-lookup"><span data-stu-id="bf546-211">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="bf546-212">Se ora si apre il file Category.cs (annidato in ProductModel.tt), si noterà che la raccolta Products ha il tipo \*\*ObservableListSource &lt; Product &gt; \*\*.</span><span class="sxs-lookup"><span data-stu-id="bf546-212">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableListSource&lt;Product&gt;**.</span></span>

<span data-ttu-id="bf546-213">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="bf546-213">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="bf546-214">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="bf546-214">Lazy Loading</span></span>

<span data-ttu-id="bf546-215">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="bf546-215">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="bf546-216">In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="bf546-216">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="bf546-217">EF offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="bf546-217">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="bf546-218">Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.</span><span class="sxs-lookup"><span data-stu-id="bf546-218">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="bf546-219">Quando si usano i tipi di entità POCO, EF raggiunge il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento.</span><span class="sxs-lookup"><span data-stu-id="bf546-219">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="bf546-220">Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) ed è necessario che la classe non sia **sealed** (**NotOverridable** in Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="bf546-220">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="bf546-221">Quando si usano Database First proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="bf546-221">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="bf546-222">Nella sezione Code First si è scelto di rendere virtuali le proprietà di navigazione per lo stesso motivo</span><span class="sxs-lookup"><span data-stu-id="bf546-222">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="bf546-223">Associa oggetto a controlli</span><span class="sxs-lookup"><span data-stu-id="bf546-223">Bind Object to Controls</span></span>

<span data-ttu-id="bf546-224">Aggiungere le classi definite nel modello come origini dati per l'applicazione Windows Form.</span><span class="sxs-lookup"><span data-stu-id="bf546-224">Add the classes that are defined in the model as data sources for this WinForms application.</span></span>

-   <span data-ttu-id="bf546-225">Dal menu principale selezionare **progetto- &gt; Aggiungi nuova origine dati...**</span><span class="sxs-lookup"><span data-stu-id="bf546-225">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="bf546-226">in Visual Studio 2010 è necessario selezionare **dati- &gt; Aggiungi nuova origine dati...**</span><span class="sxs-lookup"><span data-stu-id="bf546-226">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="bf546-227">Nella finestra scegliere un tipo di origine dati selezionare **oggetto** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="bf546-227">In the Choose a Data Source Type window, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="bf546-228">Nella finestra di dialogo selezionare gli oggetti dati, espandere **WinFormswithEFSample** due volte e selezionare **Category** . non è necessario selezionare l'origine dati del prodotto, perché verrà visualizzata tramite la proprietà del prodotto nell'origine dati Category.</span><span class="sxs-lookup"><span data-stu-id="bf546-228">In the Select the Data Objects dialog, unfold the **WinFormswithEFSample** two times and select **Category** There is no need to select the Product data source, because we will get to it through the Product’s property on the Category data source.</span></span>

    ![origine dati](~/ef6/media/datasource.png)

-   <span data-ttu-id="bf546-230">Fare clic su **Finish** (Fine).</span><span class="sxs-lookup"><span data-stu-id="bf546-230">Click **Finish.**</span></span>
    <span data-ttu-id="bf546-231">Se la finestra Origini dati non viene visualizzata, selezionare **Visualizza- &gt; altre finestre- &gt; origini dati**</span><span class="sxs-lookup"><span data-stu-id="bf546-231">If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources**</span></span>
-   <span data-ttu-id="bf546-232">Premere l'icona Aggiungi, in modo che la finestra Origini dati non nasconda automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bf546-232">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="bf546-233">Se la finestra è già visibile, potrebbe essere necessario fare clic sul pulsante Aggiorna.</span><span class="sxs-lookup"><span data-stu-id="bf546-233">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Origine dati 2](~/ef6/media/datasource2.png)

-   <span data-ttu-id="bf546-235">In Esplora soluzioni fare doppio clic sul file **Form1.cs** per aprire il modulo principale in progettazione.</span><span class="sxs-lookup"><span data-stu-id="bf546-235">In Solution Explorer, double-click the **Form1.cs** file to open the main form in designer.</span></span>
-   <span data-ttu-id="bf546-236">Selezionare l'origine dati **Category** e trascinarla nel form.</span><span class="sxs-lookup"><span data-stu-id="bf546-236">Select the **Category** data source and drag it on the form.</span></span> <span data-ttu-id="bf546-237">Per impostazione predefinita, alla finestra di progettazione vengono aggiunti un nuovo DataGridView (**categoryDataGridView**) e i controlli della barra degli strumenti di navigazione.</span><span class="sxs-lookup"><span data-stu-id="bf546-237">By default, a new DataGridView (**categoryDataGridView**) and Navigation toolbar controls are added to the designer.</span></span> <span data-ttu-id="bf546-238">Questi controlli sono associati ai componenti BindingSource (**categoryBindingSource**) e**CategoryBindingNavigator**(Binding Navigator) creati.</span><span class="sxs-lookup"><span data-stu-id="bf546-238">These controls are bound to the BindingSource (**categoryBindingSource**) and Binding Navigator (**categoryBindingNavigator**) components that are created as well.</span></span>
-   <span data-ttu-id="bf546-239">Modificare le colonne in **categoryDataGridView**.</span><span class="sxs-lookup"><span data-stu-id="bf546-239">Edit the columns on the **categoryDataGridView**.</span></span> <span data-ttu-id="bf546-240">Si vuole impostare la colonna **CategoryID** su sola lettura.</span><span class="sxs-lookup"><span data-stu-id="bf546-240">We want to set the **CategoryId** column to read-only.</span></span> <span data-ttu-id="bf546-241">Il valore della proprietà **CategoryID** viene generato dal database dopo il salvataggio dei dati.</span><span class="sxs-lookup"><span data-stu-id="bf546-241">The value for the **CategoryId** property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="bf546-242">Fare clic con il pulsante destro del mouse sul controllo DataGridView e scegliere Modifica colonne...</span><span class="sxs-lookup"><span data-stu-id="bf546-242">Right-click the DataGridView control and select Edit Columns…</span></span>
    -   <span data-ttu-id="bf546-243">Selezionare la colonna CategoryId e impostare ReadOnly su true</span><span class="sxs-lookup"><span data-stu-id="bf546-243">Select the CategoryId column and set ReadOnly to True</span></span>
    -   <span data-ttu-id="bf546-244">Premere OK</span><span class="sxs-lookup"><span data-stu-id="bf546-244">Press OK</span></span>
-   <span data-ttu-id="bf546-245">Selezionare Products dall'origine dati Category e trascinarlo nel form.</span><span class="sxs-lookup"><span data-stu-id="bf546-245">Select Products from under the Category data source and drag it on the form.</span></span> <span data-ttu-id="bf546-246">ProductDataGridView e productBindingSource vengono aggiunti al form.</span><span class="sxs-lookup"><span data-stu-id="bf546-246">The productDataGridView and productBindingSource are added to the form.</span></span>
-   <span data-ttu-id="bf546-247">Modificare le colonne in productDataGridView.</span><span class="sxs-lookup"><span data-stu-id="bf546-247">Edit the columns on the productDataGridView.</span></span> <span data-ttu-id="bf546-248">Si desidera nascondere le colonne CategoryId e Category e impostare ProductId su Read-only.</span><span class="sxs-lookup"><span data-stu-id="bf546-248">We want to hide the CategoryId and Category columns and set ProductId to read-only.</span></span> <span data-ttu-id="bf546-249">Il valore della proprietà ProductId viene generato dal database dopo il salvataggio dei dati.</span><span class="sxs-lookup"><span data-stu-id="bf546-249">The value for the ProductId property is generated by the database after we save the data.</span></span>
    -   <span data-ttu-id="bf546-250">Fare clic con il pulsante destro del mouse sul controllo DataGridView e scegliere **modifica colonne..**..</span><span class="sxs-lookup"><span data-stu-id="bf546-250">Right-click the DataGridView control and select **Edit Columns...**.</span></span>
    -   <span data-ttu-id="bf546-251">Selezionare la colonna **ProductID** e impostare **ReadOnly** su **true**.</span><span class="sxs-lookup"><span data-stu-id="bf546-251">Select the **ProductId** column and set **ReadOnly** to **True**.</span></span>
    -   <span data-ttu-id="bf546-252">Selezionare la colonna **CategoryID** e premere il pulsante **Rimuovi** .</span><span class="sxs-lookup"><span data-stu-id="bf546-252">Select the **CategoryId** column and press the **Remove** button.</span></span> <span data-ttu-id="bf546-253">Eseguire la stessa operazione con la colonna **Category** .</span><span class="sxs-lookup"><span data-stu-id="bf546-253">Do the same with the **Category** column.</span></span>
    -   <span data-ttu-id="bf546-254">Fare clic su **OK**.</span><span class="sxs-lookup"><span data-stu-id="bf546-254">Press **OK**.</span></span>

    <span data-ttu-id="bf546-255">Finora sono stati associati i controlli DataGridView ai componenti BindingSource nella finestra di progettazione.</span><span class="sxs-lookup"><span data-stu-id="bf546-255">So far, we associated our DataGridView controls with BindingSource components in the designer.</span></span> <span data-ttu-id="bf546-256">Nella sezione successiva verrà aggiunto il codice al code-behind per impostare categoryBindingSource. DataSource sulla raccolta di entità attualmente rilevate da DbContext.</span><span class="sxs-lookup"><span data-stu-id="bf546-256">In the next section we will add code to the code behind to set categoryBindingSource.DataSource to the collection of entities that are currently tracked by DbContext.</span></span> <span data-ttu-id="bf546-257">Quando sono stati trascinati e eliminati prodotti da sotto la categoria, il componente WinForms si è occupata della configurazione della proprietà productsBindingSource. DataSource sulla proprietà categoryBindingSource e productsBindingSource. DataMember sui prodotti.</span><span class="sxs-lookup"><span data-stu-id="bf546-257">When we dragged-and-dropped Products from under the Category, the WinForms took care of setting up the productsBindingSource.DataSource property to categoryBindingSource and productsBindingSource.DataMember property to Products.</span></span> <span data-ttu-id="bf546-258">A causa di questa associazione, solo i prodotti che appartengono alla categoria attualmente selezionata verranno visualizzati in productDataGridView.</span><span class="sxs-lookup"><span data-stu-id="bf546-258">Because of this binding, only the products that belong to the currently selected Category will be displayed in the productDataGridView.</span></span>
-   <span data-ttu-id="bf546-259">Abilitare il pulsante **Salva** sulla barra degli strumenti di spostamento facendo clic con il pulsante destro del mouse e selezionando **abilitato**.</span><span class="sxs-lookup"><span data-stu-id="bf546-259">Enable the **Save** button on the Navigation toolbar by clicking the right mouse button and selecting **Enabled**.</span></span>

    ![Finestra di progettazione form 1](~/ef6/media/form1-designer.png)

-   <span data-ttu-id="bf546-261">Aggiungere il gestore eventi per il pulsante Salva facendo doppio clic sul pulsante.</span><span class="sxs-lookup"><span data-stu-id="bf546-261">Add the event handler for the save button by double-clicking on the button.</span></span> <span data-ttu-id="bf546-262">Verrà aggiunto il gestore eventi e verrà riportato il code-behind per il form.</span><span class="sxs-lookup"><span data-stu-id="bf546-262">This will add the event handler and bring you to the code behind for the form.</span></span> <span data-ttu-id="bf546-263">Il codice per il gestore dell'evento \*\* \_ Click categoryBindingNavigatorSaveItem\*\* verrà aggiunto nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="bf546-263">The code for the **categoryBindingNavigatorSaveItem\_Click** event handler will be added in the next section.</span></span>

## <a name="add-the-code-that-handles-data-interaction"></a><span data-ttu-id="bf546-264">Aggiungere il codice che gestisce l'interazione dei dati</span><span class="sxs-lookup"><span data-stu-id="bf546-264">Add the Code that Handles Data Interaction</span></span>

<span data-ttu-id="bf546-265">A questo punto verrà aggiunto il codice per usare ProductContext per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="bf546-265">We'll now add the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="bf546-266">Aggiornare il codice per la finestra del form principale, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="bf546-266">Update the code for the main form window as shown below.</span></span>

<span data-ttu-id="bf546-267">Il codice dichiara un'istanza con esecuzione prolungata di ProductContext.</span><span class="sxs-lookup"><span data-stu-id="bf546-267">The code declares a long-running instance of ProductContext.</span></span> <span data-ttu-id="bf546-268">L'oggetto ProductContext viene utilizzato per eseguire query e salvare i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="bf546-268">The ProductContext object is used to query and save data to the database.</span></span> <span data-ttu-id="bf546-269">Il metodo Dispose () sull'istanza ProductContext viene quindi chiamato dal metodo OnClosing sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="bf546-269">The Dispose() method on the ProductContext instance is then called from the overridden OnClosing method.</span></span> <span data-ttu-id="bf546-270">I commenti del codice forniscono informazioni dettagliate sul funzionamento del codice.</span><span class="sxs-lookup"><span data-stu-id="bf546-270">The code comments provide details about what the code does.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Data;
    using System.Drawing;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows.Forms;
    using System.Data.Entity;

    namespace WinFormswithEFSample
    {
        public partial class Form1 : Form
        {
            ProductContext _context;
            public Form1()
            {
                InitializeComponent();
            }

            protected override void OnLoad(EventArgs e)
            {
                base.OnLoad(e);
                _context = new ProductContext();

                // Call the Load method to get the data for the given DbSet
                // from the database.
                // The data is materialized as entities. The entities are managed by
                // the DbContext instance.
                _context.Categories.Load();

                // Bind the categoryBindingSource.DataSource to
                // all the Unchanged, Modified and Added Category objects that
                // are currently tracked by the DbContext.
                // Note that we need to call ToBindingList() on the
                // ObservableCollection<TEntity> returned by
                // the DbSet.Local property to get the BindingList<T>
                // in order to facilitate two-way binding in WinForms.
                this.categoryBindingSource.DataSource =
                    _context.Categories.Local.ToBindingList();
            }

            private void categoryBindingNavigatorSaveItem_Click(object sender, EventArgs e)
            {
                this.Validate();

                // Currently, the Entity Framework doesn’t mark the entities
                // that are removed from a navigation property (in our example the Products)
                // as deleted in the context.
                // The following code uses LINQ to Objects against the Local collection
                // to find all products and marks any that do not have
                // a Category reference as deleted.
                // The ToList call is required because otherwise
                // the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can do LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                // Save the changes to the database.
                this._context.SaveChanges();

                // Refresh the controls to show the values         
                // that were generated by the database.
                this.categoryDataGridView.Refresh();
                this.productsDataGridView.Refresh();
            }

            protected override void OnClosing(CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }
    }
```

## <a name="test-the-windows-forms-application"></a><span data-ttu-id="bf546-271">Testare l'applicazione Windows Forms</span><span class="sxs-lookup"><span data-stu-id="bf546-271">Test the Windows Forms Application</span></span>

-   <span data-ttu-id="bf546-272">Compilare ed eseguire l'applicazione ed è possibile testare la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="bf546-272">Compile and run the application and you can test out the functionality.</span></span>

    ![Modulo 1 prima del salvataggio](~/ef6/media/form1beforesave.png)

-   <span data-ttu-id="bf546-274">Dopo il salvataggio, le chiavi generate dall'archivio vengono visualizzate sullo schermo.</span><span class="sxs-lookup"><span data-stu-id="bf546-274">After saving the store generated keys are shown on the screen.</span></span>

    ![Modulo 1 dopo il salvataggio](~/ef6/media/form1aftersave.png)

-   <span data-ttu-id="bf546-276">Se è stato usato Code First, si noterà anche che viene creato un database **WinFormswithEFSample. ProductContext** .</span><span class="sxs-lookup"><span data-stu-id="bf546-276">If you used Code First, then you will also see that a **WinFormswithEFSample.ProductContext** database is created for you.</span></span>

    ![Esplora oggetti server](~/ef6/media/serverobjexplorer.png)
