---
title: Associazione dati con WPF-EF6
description: Associazione dati con WPF in Entity Framework 6
author: divega
ms.date: 05/19/2020
uid: ef6/fundamentals/databinding/wpf
ms.openlocfilehash: 85aea308d89241f1da9633ef60f4bc8c3997561d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072980"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="2a327-103">Data binding con WPF</span><span class="sxs-lookup"><span data-stu-id="2a327-103">Databinding with WPF</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2a327-104">**Questo documento è valido per WPF solo sul .NET Framework**</span><span class="sxs-lookup"><span data-stu-id="2a327-104">**This document is valid for WPF on the .NET Framework only**</span></span>
>
> <span data-ttu-id="2a327-105">Questo documento descrive l'associazione dati per WPF nel .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2a327-105">This document describes databinding for WPF on the .NET Framework.</span></span> <span data-ttu-id="2a327-106">Per i nuovi progetti .NET Core, è consigliabile usare [EF Core](xref:core/index) anziché Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="2a327-106">For new .NET Core projects, we recommend you use [EF Core](xref:core/index) instead of Entity Framework 6.</span></span> <span data-ttu-id="2a327-107">La documentazione per l'associazione dati in EF Core è la seguente: [Introduzione con WPF](xref:core/get-started/wpf).</span><span class="sxs-lookup"><span data-stu-id="2a327-107">The documentation for databinding in EF Core is here: [Getting Started with WPF](xref:core/get-started/wpf).</span></span>

<span data-ttu-id="2a327-108">Questa procedura dettagliata illustra come associare tipi POCO ai controlli WPF in un modulo "Master-Detail".</span><span class="sxs-lookup"><span data-stu-id="2a327-108">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="2a327-109">L'applicazione usa le API Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="2a327-109">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="2a327-110">Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: **categoria** ( \\ master principale) e **prodotto** ( \\ Dettagli dipendenti).</span><span class="sxs-lookup"><span data-stu-id="2a327-110">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="2a327-111">Gli strumenti di Visual Studio vengono quindi utilizzati per associare i tipi definiti nel modello ai controlli WPF.</span><span class="sxs-lookup"><span data-stu-id="2a327-111">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="2a327-112">Il Framework di associazione dei dati WPF consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="2a327-112">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="2a327-113">Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2013 ma è possibile completare questa procedura dettagliata con Visual Studio 2012 o Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="2a327-113">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="2a327-114">Utilizzare l'opzione ' Object ' per la creazione di origini dati WPF</span><span class="sxs-lookup"><span data-stu-id="2a327-114">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="2a327-115">Con la versione precedente di Entity Framework si consiglia di usare l'opzione di **database** quando si crea una nuova origine dati basata su un modello creato con la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="2a327-115">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="2a327-116">Questo è dovuto al fatto che la finestra di progettazione genera un contesto che deriva da ObjectContext e da classi di entità derivate da EntityObject.</span><span class="sxs-lookup"><span data-stu-id="2a327-116">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="2a327-117">L'uso dell'opzione di database consente di scrivere il codice migliore per interagire con questa superficie dell'API.</span><span class="sxs-lookup"><span data-stu-id="2a327-117">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="2a327-118">I progettisti EF per Visual Studio 2012 e Visual Studio 2013 generano un contesto che deriva da DbContext insieme a classi di entità POCO semplici.</span><span class="sxs-lookup"><span data-stu-id="2a327-118">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="2a327-119">Con Visual Studio 2010 è consigliabile eseguire lo scambio in un modello di generazione del codice che usa DbContext, come descritto più avanti in questa procedura dettagliata.</span><span class="sxs-lookup"><span data-stu-id="2a327-119">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="2a327-120">Quando si usa la superficie dell'API DbContext, è consigliabile usare l'opzione **oggetto** durante la creazione di una nuova origine dati, come illustrato in questa procedura dettagliata.</span><span class="sxs-lookup"><span data-stu-id="2a327-120">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="2a327-121">Se necessario, è possibile [ripristinare la generazione di codice basata su ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext) per i modelli creati con la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="2a327-121">If needed, you can [revert to ObjectContext based code generation](xref:ef6/modeling/designer/codegen/legacy-objectcontext) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="2a327-122">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="2a327-122">Pre-Requisites</span></span>

<span data-ttu-id="2a327-123">Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="2a327-123">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="2a327-124">Se si usa Visual Studio 2010, è anche necessario installare NuGet.</span><span class="sxs-lookup"><span data-stu-id="2a327-124">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="2a327-125">Per altre informazioni, vedere [installazione di NuGet](/nuget/install-nuget-client-tools).</span><span class="sxs-lookup"><span data-stu-id="2a327-125">For more information, see [Installing NuGet](/nuget/install-nuget-client-tools).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="2a327-126">Creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="2a327-126">Create the Application</span></span>

- <span data-ttu-id="2a327-127">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a327-127">Open Visual Studio</span></span>
- <span data-ttu-id="2a327-128">**File- &gt; nuovo- &gt; progetto...**</span><span class="sxs-lookup"><span data-stu-id="2a327-128">**File -&gt; New -&gt; Project….**</span></span>
- <span data-ttu-id="2a327-129">Selezionare **Windows**   nel riquadro a sinistra e **WPFApplication** nel riquadro a destra.</span><span class="sxs-lookup"><span data-stu-id="2a327-129">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
- <span data-ttu-id="2a327-130">Immettere **WPFwithEFSample**   come nome</span><span class="sxs-lookup"><span data-stu-id="2a327-130">Enter **WPFwithEFSample** as the name</span></span>
- <span data-ttu-id="2a327-131">Seleziona **OK**</span><span class="sxs-lookup"><span data-stu-id="2a327-131">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="2a327-132">Installare il pacchetto NuGet Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2a327-132">Install the Entity Framework NuGet package</span></span>

- <span data-ttu-id="2a327-133">In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WinFormswithEFSample**</span><span class="sxs-lookup"><span data-stu-id="2a327-133">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
- <span data-ttu-id="2a327-134">Selezionare **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="2a327-134">Select **Manage NuGet Packages…**</span></span>
- <span data-ttu-id="2a327-135">Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="2a327-135">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
- <span data-ttu-id="2a327-136">Fai clic su **Install** (Installa).</span><span class="sxs-lookup"><span data-stu-id="2a327-136">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="2a327-137">Oltre all'assembly EntityFramework, viene aggiunto anche un riferimento a System. ComponentModel. DataAnnotations.</span><span class="sxs-lookup"><span data-stu-id="2a327-137">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="2a327-138">Se il progetto contiene un riferimento a System. Data. Entity, verrà rimosso al momento dell'installazione del pacchetto EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="2a327-138">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="2a327-139">L'assembly System. Data. Entity non viene più usato per le applicazioni Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="2a327-139">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="2a327-140">Definire un modello</span><span class="sxs-lookup"><span data-stu-id="2a327-140">Define a Model</span></span>

<span data-ttu-id="2a327-141">In questa procedura dettagliata è possibile scegliere di implementare un modello usando Code First o la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="2a327-141">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="2a327-142">Completare una delle due sezioni riportate di seguito.</span><span class="sxs-lookup"><span data-stu-id="2a327-142">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="2a327-143">Opzione 1: definire un modello utilizzando Code First</span><span class="sxs-lookup"><span data-stu-id="2a327-143">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="2a327-144">In questa sezione viene illustrato come creare un modello e il relativo database associato utilizzando Code First.</span><span class="sxs-lookup"><span data-stu-id="2a327-144">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="2a327-145">Passare alla sezione successiva (**opzione 2: definire un modello usando database First)** se si preferisce usare database First per decompilare il modello da un database usando EF designer</span><span class="sxs-lookup"><span data-stu-id="2a327-145">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="2a327-146">Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio).</span><span class="sxs-lookup"><span data-stu-id="2a327-146">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

- <span data-ttu-id="2a327-147">Aggiungere una nuova classe a **WPFwithEFSample:**</span><span class="sxs-lookup"><span data-stu-id="2a327-147">Add a new class to the **WPFwithEFSample:**</span></span>
  - <span data-ttu-id="2a327-148">Fare clic con il pulsante destro del mouse sul nome del progetto</span><span class="sxs-lookup"><span data-stu-id="2a327-148">Right-click on the project name</span></span>
  - <span data-ttu-id="2a327-149">Selezionare **Aggiungi**, quindi **nuovo elemento**</span><span class="sxs-lookup"><span data-stu-id="2a327-149">Select **Add**, then **New Item**</span></span>
  - <span data-ttu-id="2a327-150">Selezionare la **classe** e immettere **Product**   per nome classe</span><span class="sxs-lookup"><span data-stu-id="2a327-150">Select **Class** and enter **Product** for the class name</span></span>
- <span data-ttu-id="2a327-151">Sostituire la **Product**   definizione della classe di prodotto con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="2a327-151">Replace the **Product** class definition with the following code:</span></span>

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

- Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

<span data-ttu-id="2a327-152">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="2a327-152">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="2a327-153">In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="2a327-153">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="2a327-154">Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; .</span><span class="sxs-lookup"><span data-stu-id="2a327-154">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="2a327-155">Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.</span><span class="sxs-lookup"><span data-stu-id="2a327-155">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="2a327-156">Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.</span><span class="sxs-lookup"><span data-stu-id="2a327-156">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

- <span data-ttu-id="2a327-157">Aggiungere una nuova classe **ProductContext** al progetto con la definizione seguente:</span><span class="sxs-lookup"><span data-stu-id="2a327-157">Add a new **ProductContext** class to the project with the following definition:</span></span>

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="2a327-158">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="2a327-158">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="2a327-159">Opzione 2: definire un modello utilizzando Database First</span><span class="sxs-lookup"><span data-stu-id="2a327-159">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="2a327-160">Questa sezione illustra come usare Database First per decompilare il modello da un database usando la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="2a327-160">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="2a327-161">Se è stata completata la sezione precedente (**opzione 1: definire un modello con Code First)**, ignorare questa sezione e passare direttamente alla sezione **caricamento lazy** .</span><span class="sxs-lookup"><span data-stu-id="2a327-161">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="2a327-162">Creare un database esistente</span><span class="sxs-lookup"><span data-stu-id="2a327-162">Create an Existing Database</span></span>

<span data-ttu-id="2a327-163">In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.</span><span class="sxs-lookup"><span data-stu-id="2a327-163">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="2a327-164">Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:</span><span class="sxs-lookup"><span data-stu-id="2a327-164">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

- <span data-ttu-id="2a327-165">Se si usa Visual Studio 2010 verrà creato un database di SQL Express.</span><span class="sxs-lookup"><span data-stu-id="2a327-165">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
- <span data-ttu-id="2a327-166">Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202.aspx) .</span><span class="sxs-lookup"><span data-stu-id="2a327-166">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="2a327-167">Procediamo con la generazione del database.</span><span class="sxs-lookup"><span data-stu-id="2a327-167">Let's go ahead and generate the database.</span></span>

- <span data-ttu-id="2a327-168">**Visualizza- &gt; Esplora server**</span><span class="sxs-lookup"><span data-stu-id="2a327-168">**View -&gt; Server Explorer**</span></span>
- <span data-ttu-id="2a327-169">Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**</span><span class="sxs-lookup"><span data-stu-id="2a327-169">Right click on **Data Connections -&gt; Add Connection…**</span></span>
- <span data-ttu-id="2a327-170">Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati</span><span class="sxs-lookup"><span data-stu-id="2a327-170">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Modifica origine dati](~/ef6/media/changedatasource.png)

- <span data-ttu-id="2a327-172">Connettersi al database locale o a SQL Express, a seconda di quale installato e immettere i **prodotti** come nome del database</span><span class="sxs-lookup"><span data-stu-id="2a327-172">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![Aggiungere la connessione al database locale](~/ef6/media/addconnectionlocaldb.png)

    ![Aggiungi connessione rapida](~/ef6/media/addconnectionexpress.png)

- <span data-ttu-id="2a327-175">Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="2a327-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Create Database](~/ef6/media/createdatabase.png)

- <span data-ttu-id="2a327-177">Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .</span><span class="sxs-lookup"><span data-stu-id="2a327-177">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
- <span data-ttu-id="2a327-178">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="2a327-178">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

#### <a name="reverse-engineer-model"></a><span data-ttu-id="2a327-179">Decodificare il modello</span><span class="sxs-lookup"><span data-stu-id="2a327-179">Reverse Engineer Model</span></span>

<span data-ttu-id="2a327-180">Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2a327-180">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

- <span data-ttu-id="2a327-181">**Progetto- &gt; Aggiungi nuovo elemento...**</span><span class="sxs-lookup"><span data-stu-id="2a327-181">**Project -&gt; Add New Item…**</span></span>
- <span data-ttu-id="2a327-182">Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="2a327-182">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
- <span data-ttu-id="2a327-183">Immettere **ProductModel** come nome e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="2a327-183">Enter **ProductModel** as the name and click **OK**</span></span>
- <span data-ttu-id="2a327-184">Viene avviata la **procedura guidata Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="2a327-184">This launches the **Entity Data Model Wizard**</span></span>
- <span data-ttu-id="2a327-185">Selezionare **genera da database** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="2a327-185">Select **Generate from Database** and click **Next**</span></span>

    ![Scelta dei contenuti del modello](~/ef6/media/choosemodelcontents.png)

- <span data-ttu-id="2a327-187">Selezionare la connessione al database creato nella prima sezione, immettere **ProductContext** come nome della stringa di connessione e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="2a327-187">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![Scegliere la connessione](~/ef6/media/chooseyourconnection.png)

- <span data-ttu-id="2a327-189">Fare clic sulla casella di controllo accanto a' Tables ' per importare tutte le tabelle e fare clic su' Finish '</span><span class="sxs-lookup"><span data-stu-id="2a327-189">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Scegliere gli oggetti](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="2a327-191">Una volta completato il processo di Reverse Engineering, il nuovo modello viene aggiunto al progetto e aperto per la visualizzazione nel Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="2a327-191">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="2a327-192">Al progetto è stato aggiunto anche un file di App.config con i dettagli della connessione per il database.</span><span class="sxs-lookup"><span data-stu-id="2a327-192">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="2a327-193">Passaggi aggiuntivi in Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="2a327-193">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="2a327-194">Se si lavora in Visual Studio 2010, sarà necessario aggiornare la finestra di progettazione di Entity Framework per l'uso della generazione di codice EF6.</span><span class="sxs-lookup"><span data-stu-id="2a327-194">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

- <span data-ttu-id="2a327-195">Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**</span><span class="sxs-lookup"><span data-stu-id="2a327-195">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
- <span data-ttu-id="2a327-196">Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**</span><span class="sxs-lookup"><span data-stu-id="2a327-196">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
- <span data-ttu-id="2a327-197">Selezionare il **Generatore EF 6. x DbContext per C \# ,** immettere **ProductsModel** come nome e fare clic su Aggiungi.</span><span class="sxs-lookup"><span data-stu-id="2a327-197">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="2a327-198">Aggiornamento della generazione di codice per data binding</span><span class="sxs-lookup"><span data-stu-id="2a327-198">Updating code generation for data binding</span></span>

<span data-ttu-id="2a327-199">EF genera codice dal modello usando i modelli T4.</span><span class="sxs-lookup"><span data-stu-id="2a327-199">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="2a327-200">I modelli forniti con Visual Studio o scaricati da Visual Studio Gallery sono destinati all'uso generico.</span><span class="sxs-lookup"><span data-stu-id="2a327-200">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="2a327-201">Questo significa che le entità generate da questi modelli hanno &lt; Proprietà ICollection T semplici &gt; .</span><span class="sxs-lookup"><span data-stu-id="2a327-201">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="2a327-202">Tuttavia, quando si esegue data binding utilizzando WPF, è consigliabile utilizzare **ObservableCollection** per le proprietà della raccolta in modo che WPF possa tenere traccia delle modifiche apportate alle raccolte.</span><span class="sxs-lookup"><span data-stu-id="2a327-202">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="2a327-203">A questo scopo, è possibile modificare i modelli per usare ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="2a327-203">To this end we will to modify the templates to use ObservableCollection.</span></span>

- <span data-ttu-id="2a327-204">Aprire il **Esplora soluzioni** e trovare il file **ProductModel. edmx**</span><span class="sxs-lookup"><span data-stu-id="2a327-204">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
- <span data-ttu-id="2a327-205">Trovare il file **ProductModel.TT** che verrà annidato nel file ProductModel. edmx</span><span class="sxs-lookup"><span data-stu-id="2a327-205">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![Modello di modello di prodotto WPF](~/ef6/media/wpfproductmodeltemplate.png)

- <span data-ttu-id="2a327-207">Fare doppio clic sul file ProductModel.tt per aprirlo nell'editor di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a327-207">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
- <span data-ttu-id="2a327-208">Trovare e sostituire le due occorrenze di "**ICollection**" con "**ObservableCollection**".</span><span class="sxs-lookup"><span data-stu-id="2a327-208">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="2a327-209">Si trovano approssimativamente a linee 296 e 484.</span><span class="sxs-lookup"><span data-stu-id="2a327-209">These are located approximately at lines 296 and 484.</span></span>
- <span data-ttu-id="2a327-210">Trovare e sostituire la prima occorrenza di "**HashSet**" con "**ObservableCollection**".</span><span class="sxs-lookup"><span data-stu-id="2a327-210">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="2a327-211">Questa occorrenza si trova approssimativamente alla riga 50.</span><span class="sxs-lookup"><span data-stu-id="2a327-211">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="2a327-212">**Non sostituire la** seconda occorrenza di HashSet rilevata successivamente nel codice.</span><span class="sxs-lookup"><span data-stu-id="2a327-212">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
- <span data-ttu-id="2a327-213">Trovare e sostituire l'unica occorrenza di "**System. Collections. Generic**" con "**System. Collections. ObjectModel**".</span><span class="sxs-lookup"><span data-stu-id="2a327-213">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="2a327-214">Si trova approssimativamente alla riga 424.</span><span class="sxs-lookup"><span data-stu-id="2a327-214">This is located approximately at line 424.</span></span>
- <span data-ttu-id="2a327-215">Salvare il file ProductModel.tt.</span><span class="sxs-lookup"><span data-stu-id="2a327-215">Save the ProductModel.tt file.</span></span> <span data-ttu-id="2a327-216">Questa operazione dovrebbe causare la rigenerazione del codice per le entità.</span><span class="sxs-lookup"><span data-stu-id="2a327-216">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="2a327-217">Se il codice non viene rigenerato automaticamente, fare clic con il pulsante destro del mouse su ProductModel.tt e scegliere "Esegui strumento personalizzato".</span><span class="sxs-lookup"><span data-stu-id="2a327-217">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="2a327-218">Se ora si apre il file Category.cs (annidato in ProductModel.tt), si noterà che la raccolta Products ha il tipo di \*\* &lt; &gt; prodotto ObservableCollection\*\*.</span><span class="sxs-lookup"><span data-stu-id="2a327-218">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="2a327-219">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="2a327-219">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="2a327-220">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="2a327-220">Lazy Loading</span></span>

<span data-ttu-id="2a327-221">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="2a327-221">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="2a327-222">In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="2a327-222">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="2a327-223">EF offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="2a327-223">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="2a327-224">Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.</span><span class="sxs-lookup"><span data-stu-id="2a327-224">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="2a327-225">Quando si usano i tipi di entità POCO, EF raggiunge il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento.</span><span class="sxs-lookup"><span data-stu-id="2a327-225">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="2a327-226">Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) ed è necessario che la classe non sia **sealed** (**NotOverridable** in Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="2a327-226">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="2a327-227">Quando si usano Database First proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="2a327-227">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="2a327-228">Nella sezione Code First si è scelto di rendere virtuali le proprietà di navigazione per lo stesso motivo</span><span class="sxs-lookup"><span data-stu-id="2a327-228">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="2a327-229">Associa oggetto a controlli</span><span class="sxs-lookup"><span data-stu-id="2a327-229">Bind Object to Controls</span></span>

<span data-ttu-id="2a327-230">Aggiungere le classi definite nel modello come origini dati per l'applicazione WPF.</span><span class="sxs-lookup"><span data-stu-id="2a327-230">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

- <span data-ttu-id="2a327-231">Fare doppio clic su **MainWindow. XAML** in Esplora soluzioni per aprire il modulo principale</span><span class="sxs-lookup"><span data-stu-id="2a327-231">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
- <span data-ttu-id="2a327-232">Dal menu principale selezionare **progetto- &gt; Aggiungi nuova origine dati...**</span><span class="sxs-lookup"><span data-stu-id="2a327-232">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="2a327-233">in Visual Studio 2010 è necessario selezionare **dati- &gt; Aggiungi nuova origine dati...**</span><span class="sxs-lookup"><span data-stu-id="2a327-233">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
- <span data-ttu-id="2a327-234">Nella pagina scegliere un'origine dati Typewindow selezionare **oggetto** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="2a327-234">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
- <span data-ttu-id="2a327-235">Nella finestra di dialogo selezionare gli oggetti dati, espandere **WPFwithEFSample**   due volte e selezionare **Category**</span><span class="sxs-lookup"><span data-stu-id="2a327-235">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="2a327-236">*Non è necessario selezionare l'origine dati del **prodotto** perché verrà rilasciata tramite la proprietà del **prodotto**nell'origine dati **Category***</span><span class="sxs-lookup"><span data-stu-id="2a327-236">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![Selezione oggetti dati](~/ef6/media/selectdataobjects.png)

- <span data-ttu-id="2a327-238">Fare clic su **Finish** (Fine).</span><span class="sxs-lookup"><span data-stu-id="2a327-238">Click **Finish.**</span></span>
- <span data-ttu-id="2a327-239">La finestra Origini dati viene aperta accanto alla finestra MainWindow. XAML \*se la finestra Origini dati non viene visualizzata, selezionare **Visualizza- &gt; altre &gt; origini dati Windows** \*</span><span class="sxs-lookup"><span data-stu-id="2a327-239">The Data Sources window is opened next to the MainWindow.xaml window   *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
- <span data-ttu-id="2a327-240">Premere l'icona Aggiungi, in modo che la finestra Origini dati non nasconda automaticamente.</span><span class="sxs-lookup"><span data-stu-id="2a327-240">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="2a327-241">Se la finestra è già visibile, potrebbe essere necessario fare clic sul pulsante Aggiorna.</span><span class="sxs-lookup"><span data-stu-id="2a327-241">You may need to hit the refresh button if the window was already visible.</span></span>

    ![Origini dati](~/ef6/media/datasources.png)

- <span data-ttu-id="2a327-243">Selezionare l'origine dati **Category** e trascinarla nel form.</span><span class="sxs-lookup"><span data-stu-id="2a327-243">Select the **Category** data source and drag it on the form.</span></span>

<span data-ttu-id="2a327-244">Quando è stata trascinata questa origine, si è verificato quanto segue:</span><span class="sxs-lookup"><span data-stu-id="2a327-244">The following happened when we dragged this source:</span></span>

- <span data-ttu-id="2a327-245">La risorsa **categoryViewSource** e il controllo **categoryDataGrid** sono stati aggiunti a XAML</span><span class="sxs-lookup"><span data-stu-id="2a327-245">The **categoryViewSource** resource and the **categoryDataGrid** control were added to XAML</span></span>
- <span data-ttu-id="2a327-246">La proprietà DataContext sull'elemento Grid padre è stata impostata su "{StaticResource **categoryViewSource** }".</span><span class="sxs-lookup"><span data-stu-id="2a327-246">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span><span data-ttu-id="2a327-247">La risorsa **categoryViewSource** funge da origine dell'associazione per l' \\ elemento Grid padre esterno.</span><span class="sxs-lookup"><span data-stu-id="2a327-247"> The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="2a327-248">Gli elementi della griglia interna ereditano quindi il valore DataContext dalla griglia padre (la proprietà ItemsSource di categoryDataGrid è impostata su "{binding}")</span><span class="sxs-lookup"><span data-stu-id="2a327-248">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}")</span></span>

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a><span data-ttu-id="2a327-249">Aggiunta di una griglia dettagli</span><span class="sxs-lookup"><span data-stu-id="2a327-249">Adding a Details Grid</span></span>

<span data-ttu-id="2a327-250">Ora che è disponibile una griglia per visualizzare le categorie, aggiungere una griglia dettagli per visualizzare i prodotti associati.</span><span class="sxs-lookup"><span data-stu-id="2a327-250">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

- <span data-ttu-id="2a327-251">Selezionare la proprietà **Products** dall'origine dati **Category** e trascinarla nel form.</span><span class="sxs-lookup"><span data-stu-id="2a327-251">Select the **Products** property from under the **Category** data source and drag it on the form.</span></span>
  - <span data-ttu-id="2a327-252">La risorsa **categoryProductsViewSource** e la griglia **productDataGrid** vengono aggiunte a XAML</span><span class="sxs-lookup"><span data-stu-id="2a327-252">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
  - <span data-ttu-id="2a327-253">Il percorso di associazione per questa risorsa è impostato su prodotti</span><span class="sxs-lookup"><span data-stu-id="2a327-253">The binding path for this resource is set to Products</span></span>
  - <span data-ttu-id="2a327-254">Il Framework di associazione dati WPF garantisce che vengano visualizzati solo i prodotti correlati alla categoria selezionata in **productDataGrid**</span><span class="sxs-lookup"><span data-stu-id="2a327-254">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
- <span data-ttu-id="2a327-255">Dalla casella degli strumenti trascinare il **pulsante del mouse** sul form.</span><span class="sxs-lookup"><span data-stu-id="2a327-255">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="2a327-256">Impostare la proprietà **Name** su **buttonSave** e la proprietà **Content** su **Save**.</span><span class="sxs-lookup"><span data-stu-id="2a327-256">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="2a327-257">Il form dovrebbe essere simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="2a327-257">The form should look similar to this:</span></span>

![Form progettazione](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="2a327-259">Aggiungere codice che gestisce l'interazione dei dati</span><span class="sxs-lookup"><span data-stu-id="2a327-259">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="2a327-260">È il momento di aggiungere alcuni gestori di eventi alla finestra principale.</span><span class="sxs-lookup"><span data-stu-id="2a327-260">It's time to add some event handlers to the main window.</span></span>

- <span data-ttu-id="2a327-261">Nella finestra XAML fare clic sull'elemento \*\* &lt; Window\*\* , che seleziona la finestra principale</span><span class="sxs-lookup"><span data-stu-id="2a327-261">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
- <span data-ttu-id="2a327-262">Nella finestra **Proprietà** scegliere **eventi** in alto a destra, quindi fare doppio clic sulla casella di testo a destra dell'etichetta **caricata** .</span><span class="sxs-lookup"><span data-stu-id="2a327-262">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![Proprietà della finestra principale](~/ef6/media/mainwindowproperties.png)

- <span data-ttu-id="2a327-264">Aggiungere anche l'evento **Click** per il pulsante **Salva** facendo doppio clic sul pulsante Salva nella finestra di progettazione.</span><span class="sxs-lookup"><span data-stu-id="2a327-264">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span>

<span data-ttu-id="2a327-265">In questo modo viene riportato il codice sottostante per il modulo. verrà ora modificato il codice per usare ProductContext per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="2a327-265">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="2a327-266">Aggiornare il codice per MainWindow come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="2a327-266">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="2a327-267">Il codice dichiara un'istanza con esecuzione prolungata di **ProductContext**.</span><span class="sxs-lookup"><span data-stu-id="2a327-267">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="2a327-268">L'oggetto **ProductContext** viene utilizzato per eseguire query e salvare i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="2a327-268">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="2a327-269">Il metodo **Dispose ()** sull'istanza **ProductContext** viene quindi chiamato dal metodo **OnClosing** sottoposto a override.</span><span class="sxs-lookup"><span data-stu-id="2a327-269">The **Dispose()** on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span><span data-ttu-id="2a327-270">I commenti del codice forniscono informazioni dettagliate sul funzionamento del codice.</span><span class="sxs-lookup"><span data-stu-id="2a327-270"> The code comments provide details about what the code does.</span></span>

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a><span data-ttu-id="2a327-271">Testare l'applicazione WPF</span><span class="sxs-lookup"><span data-stu-id="2a327-271">Test the WPF Application</span></span>

- <span data-ttu-id="2a327-272">Compilare l'applicazione ed eseguirla.</span><span class="sxs-lookup"><span data-stu-id="2a327-272">Compile and run the application.</span></span> <span data-ttu-id="2a327-273">Se è stato usato Code First, si noterà che viene creato un database **WPFwithEFSample. ProductContext** .</span><span class="sxs-lookup"><span data-stu-id="2a327-273">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
- <span data-ttu-id="2a327-274">Immettere un nome di categoria nella griglia superiore e i nomi di prodotto nella griglia inferiore non   *immettere elementi nelle colonne ID, perché la chiave primaria viene generata dal database*</span><span class="sxs-lookup"><span data-stu-id="2a327-274">Enter a category name in the top grid and product names in the bottom grid   *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![Finestra principale con nuove categorie e prodotti](~/ef6/media/screen1.png)

- <span data-ttu-id="2a327-276">Premere il pulsante **Salva** per salvare i dati nel database</span><span class="sxs-lookup"><span data-stu-id="2a327-276">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="2a327-277">Dopo la chiamata a **SaveChanges ()** di DbContext, gli ID vengono popolati con i valori generati dal database.</span><span class="sxs-lookup"><span data-stu-id="2a327-277">After the call to DbContext’s **SaveChanges()**, the IDs are populated with the database generated values.</span></span> <span data-ttu-id="2a327-278">Poiché è stato chiamato **Refresh ()** dopo **SaveChanges ()** , i controlli **DataGrid** vengono aggiornati anche con i nuovi valori.</span><span class="sxs-lookup"><span data-stu-id="2a327-278">Because we called **Refresh()** after **SaveChanges()** the **DataGrid** controls are updated with the new values as well.</span></span>

![Finestra principale con ID popolati](~/ef6/media/screen2.png)

## <a name="additional-resources"></a><span data-ttu-id="2a327-280">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2a327-280">Additional Resources</span></span>

<span data-ttu-id="2a327-281">Per ulteriori informazioni sulle data binding alle raccolte utilizzando WPF, vedere [questo argomento](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) nella documentazione di WPF.</span><span class="sxs-lookup"><span data-stu-id="2a327-281">To learn more about data binding to collections using WPF, see [this topic](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) in the WPF documentation.</span></span>  
