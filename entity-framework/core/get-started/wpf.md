---
title: Introduzione a WPF-EF Core
description: Esercitazione introduttiva per l'uso di WPF con Entity Framework Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 1198da5c9564663ca26392b33462c727275a432d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619310"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="71a00-103">Introduzione a WPF</span><span class="sxs-lookup"><span data-stu-id="71a00-103">Getting Started with WPF</span></span>

<span data-ttu-id="71a00-104">Questa procedura dettagliata illustra come associare tipi POCO ai controlli WPF in un modulo "Main-Detail".</span><span class="sxs-lookup"><span data-stu-id="71a00-104">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="71a00-105">L'applicazione usa le API Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="71a00-105">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="71a00-106">Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: **categoria** (principale \\ principale) e **prodotto** ( \\ Dettagli dipendenti).</span><span class="sxs-lookup"><span data-stu-id="71a00-106">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="71a00-107">Il Framework di associazione dei dati WPF consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="71a00-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="71a00-108">Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="71a00-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="71a00-109">È possibile visualizzare l'esempio di questo articolo [in GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span><span class="sxs-lookup"><span data-stu-id="71a00-109">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="71a00-110">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="71a00-110">Pre-Requisites</span></span>

* <span data-ttu-id="71a00-111">Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2019 16,3 o versione successiva con il **carico di lavoro di .NET desktop** selezionato.</span><span class="sxs-lookup"><span data-stu-id="71a00-111">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="71a00-112">Per ulteriori informazioni sull'installazione della versione più recente di Visual Studio, vedere [Install Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="71a00-112">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="71a00-113">Creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="71a00-113">Create the Application</span></span>

1. <span data-ttu-id="71a00-114">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="71a00-114">Open Visual Studio</span></span>
2. <span data-ttu-id="71a00-115">Nella finestra iniziale scegliere **Crea nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="71a00-115">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="71a00-116">Cercare "WPF", scegliere **app WPF (.NET Core)** , quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="71a00-116">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="71a00-117">Nella schermata successiva assegnare un nome al progetto, ad esempio **GetStartedWPF**, e scegliere **Crea.**</span><span class="sxs-lookup"><span data-stu-id="71a00-117">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="71a00-118">Installare i pacchetti NuGet di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="71a00-118">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="71a00-119">Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione...**</span><span class="sxs-lookup"><span data-stu-id="71a00-119">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="71a00-121">Digitare `entityframeworkcore.sqlite` nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="71a00-121">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="71a00-122">Selezionare il pacchetto **Microsoft. EntityFrameworkCore. sqlite** .</span><span class="sxs-lookup"><span data-stu-id="71a00-122">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="71a00-123">Controllare il progetto nel riquadro destro e fare clic su **Installa** .</span><span class="sxs-lookup"><span data-stu-id="71a00-123">Check the project in the right pane and click **Install**</span></span>

    ![Pacchetto sqlite](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="71a00-125">Ripetere i passaggi per cercare `entityframeworkcore.proxies` e installare **Microsoft. EntityFrameworkCore. proxy**.</span><span class="sxs-lookup"><span data-stu-id="71a00-125">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="71a00-126">Quando è stato installato il pacchetto SQLite, viene automaticamente eseguito il pull del pacchetto di base **Microsoft. EntityFrameworkCore** correlato.</span><span class="sxs-lookup"><span data-stu-id="71a00-126">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="71a00-127">Il pacchetto **Microsoft. EntityFrameworkCore. proxy** fornisce supporto per i dati di caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="71a00-127">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="71a00-128">Ciò significa che quando si hanno entità con entità figlio, solo gli elementi padre vengono recuperati al caricamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="71a00-128">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="71a00-129">I proxy rilevano quando viene eseguito un tentativo di accesso alle entità figlio e li carica automaticamente su richiesta.</span><span class="sxs-lookup"><span data-stu-id="71a00-129">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="71a00-130">Definire un modello</span><span class="sxs-lookup"><span data-stu-id="71a00-130">Define a Model</span></span>

<span data-ttu-id="71a00-131">In questa procedura dettagliata verrà implementato un modello utilizzando "Code First".</span><span class="sxs-lookup"><span data-stu-id="71a00-131">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="71a00-132">Ciò significa che EF Core creerà le tabelle e lo schema del database in base alle classi C# definite.</span><span class="sxs-lookup"><span data-stu-id="71a00-132">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="71a00-133">Aggiungere una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="71a00-133">Add a new class.</span></span> <span data-ttu-id="71a00-134">Assegnargli il nome: `Product.cs` e popolarlo come segue:</span><span class="sxs-lookup"><span data-stu-id="71a00-134">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="71a00-135">Aggiungere quindi una classe denominata `Category.cs` e popolarla con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="71a00-135">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="71a00-136">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="71a00-136">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="71a00-137">In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="71a00-137">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="71a00-138">Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; .</span><span class="sxs-lookup"><span data-stu-id="71a00-138">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="71a00-139">Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.</span><span class="sxs-lookup"><span data-stu-id="71a00-139">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="71a00-140">Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.</span><span class="sxs-lookup"><span data-stu-id="71a00-140">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="71a00-141">Aggiungere una nuova `ProductContext.cs` classe al progetto con la definizione seguente:</span><span class="sxs-lookup"><span data-stu-id="71a00-141">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="71a00-142">`DbSet`Informa EF Core quali entità C# devono essere mappate al database.</span><span class="sxs-lookup"><span data-stu-id="71a00-142">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="71a00-143">Esistono diversi modi per configurare la EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="71a00-143">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="71a00-144">È possibile leggere le informazioni in: [configurazione di un DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="71a00-144">You can read about them in: [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="71a00-145">Questo esempio usa l' `OnConfiguring` override per specificare un file di dati SQLite.</span><span class="sxs-lookup"><span data-stu-id="71a00-145">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="71a00-146">La `UseLazyLoadingProxies` chiamata indica EF Core di implementare il caricamento lazy, in modo che le entità figlio vengano caricate automaticamente al momento dell'accesso dall'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="71a00-146">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="71a00-147">Premere **CTRL + MAIUSC + B** o passare a **Compila compilazione &gt; soluzione** per compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="71a00-147">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="71a00-148">Per informazioni sulle diverse operazioni, vedere la pagina relativa alla gestione degli schemi di [database](xref:core/managing-schemas/index)e dei modelli di EF core in sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="71a00-148">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](xref:core/managing-schemas/index).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="71a00-149">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="71a00-149">Lazy Loading</span></span>

<span data-ttu-id="71a00-150">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="71a00-150">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="71a00-151">In Entity Framework Core, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="71a00-151">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="71a00-152">EF Core offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="71a00-152">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="71a00-153">Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.</span><span class="sxs-lookup"><span data-stu-id="71a00-153">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="71a00-154">Quando si usano tipi di entità POCO (Plain Old C# Object), EF Core ottiene il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento.</span><span class="sxs-lookup"><span data-stu-id="71a00-154">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="71a00-155">Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) e la classe non deve essere **sealed** (**NotOverridable** in Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="71a00-155">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="71a00-156">Quando si usa Database First, le proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="71a00-156">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="71a00-157">Associa oggetto a controlli</span><span class="sxs-lookup"><span data-stu-id="71a00-157">Bind Object to Controls</span></span>

<span data-ttu-id="71a00-158">Aggiungere le classi definite nel modello come origini dati per l'applicazione WPF.</span><span class="sxs-lookup"><span data-stu-id="71a00-158">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="71a00-159">Fare doppio clic su **MainWindow. XAML** in Esplora soluzioni per aprire il modulo principale</span><span class="sxs-lookup"><span data-stu-id="71a00-159">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="71a00-160">Scegliere la scheda **XAML** per modificare il codice XAML.</span><span class="sxs-lookup"><span data-stu-id="71a00-160">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="71a00-161">Subito dopo il tag di apertura `Window` aggiungere le origini seguenti per connettersi alle entità EF core.</span><span class="sxs-lookup"><span data-stu-id="71a00-161">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="71a00-162">In questo modo viene impostata l'origine per le categorie "padre" e la seconda origine per i prodotti "Detail".</span><span class="sxs-lookup"><span data-stu-id="71a00-162">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="71a00-163">Successivamente, aggiungere il markup seguente al codice XAML dopo il tag di chiusura `Window.Resources` .</span><span class="sxs-lookup"><span data-stu-id="71a00-163">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="71a00-164">Si noti che la `CategoryId` è impostata su `ReadOnly` perché è assegnata dal database e non può essere modificata.</span><span class="sxs-lookup"><span data-stu-id="71a00-164">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="71a00-165">Aggiunta di una griglia dettagli</span><span class="sxs-lookup"><span data-stu-id="71a00-165">Adding a Details Grid</span></span>

<span data-ttu-id="71a00-166">Ora che la griglia esiste per visualizzare le categorie, è possibile aggiungere la griglia dei dettagli per visualizzare i prodotti.</span><span class="sxs-lookup"><span data-stu-id="71a00-166">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="71a00-167">Infine, aggiungere un `Save` pulsante e un cavo nell'evento click a `Button_Click` .</span><span class="sxs-lookup"><span data-stu-id="71a00-167">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="71a00-168">La visualizzazione progettazione dovrebbe essere simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="71a00-168">Your design view should look like this:</span></span>

![Screenshot della finestra di progettazione WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="71a00-170">Aggiungere codice che gestisce l'interazione dei dati</span><span class="sxs-lookup"><span data-stu-id="71a00-170">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="71a00-171">È il momento di aggiungere alcuni gestori di eventi alla finestra principale.</span><span class="sxs-lookup"><span data-stu-id="71a00-171">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="71a00-172">Nella finestra XAML fare clic sull'elemento \*\* &lt; Window ( &gt; finestra\*\* ) per selezionare la finestra principale.</span><span class="sxs-lookup"><span data-stu-id="71a00-172">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="71a00-173">Nella finestra **Proprietà** scegliere **eventi** in alto a destra, quindi fare doppio clic sulla casella di testo a destra dell'etichetta **caricata** .</span><span class="sxs-lookup"><span data-stu-id="71a00-173">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Proprietà della finestra principale](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="71a00-175">In questo modo viene riportato il codice sottostante per il modulo. verrà ora modificato il codice per usare `ProductContext` per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="71a00-175">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="71a00-176">Aggiornare il codice come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="71a00-176">Update the code as shown below.</span></span>

<span data-ttu-id="71a00-177">Il codice dichiara un'istanza con esecuzione prolungata di `ProductContext` .</span><span class="sxs-lookup"><span data-stu-id="71a00-177">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="71a00-178">L' `ProductContext` oggetto viene utilizzato per eseguire query e salvare i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="71a00-178">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="71a00-179">Il `Dispose()` metodo sull' `ProductContext` istanza viene quindi chiamato dal metodo sottoposto a override `OnClosing` .</span><span class="sxs-lookup"><span data-stu-id="71a00-179">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="71a00-180">I commenti del codice spiegano cosa accade in ogni passaggio.</span><span class="sxs-lookup"><span data-stu-id="71a00-180"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="71a00-181">Il codice usa una chiamata a `EnsureCreated()` per compilare il database alla prima esecuzione.</span><span class="sxs-lookup"><span data-stu-id="71a00-181">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="71a00-182">Questa operazione è accettabile per le demo, ma nelle app di produzione è consigliabile esaminare le [migrazioni](xref:core/managing-schemas/migrations/index) per gestire lo schema.</span><span class="sxs-lookup"><span data-stu-id="71a00-182">This is acceptable for demos, but in production apps you should look at [migrations](xref:core/managing-schemas/migrations/index) to manage your schema.</span></span> <span data-ttu-id="71a00-183">Anche il codice viene eseguito in modo sincrono perché usa un database SQLite locale.</span><span class="sxs-lookup"><span data-stu-id="71a00-183">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="71a00-184">Per gli scenari di produzione che in genere coinvolgono un server remoto, è consigliabile usare le versioni asincrone dei `Load` `SaveChanges` metodi e.</span><span class="sxs-lookup"><span data-stu-id="71a00-184">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="71a00-185">Testare l'applicazione WPF</span><span class="sxs-lookup"><span data-stu-id="71a00-185">Test the WPF Application</span></span>

<span data-ttu-id="71a00-186">Compilare ed eseguire l'applicazione premendo **F5** o scegliendo **debug &gt; Avvia debug**.</span><span class="sxs-lookup"><span data-stu-id="71a00-186">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="71a00-187">Il database deve essere creato automaticamente con un file denominato `products.db` .</span><span class="sxs-lookup"><span data-stu-id="71a00-187">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="71a00-188">Immettere un nome di categoria e premere INVIO, quindi aggiungere prodotti alla griglia inferiore.</span><span class="sxs-lookup"><span data-stu-id="71a00-188">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="71a00-189">Fare clic su Salva e controllare l'aggiornamento della griglia con gli ID forniti dal database.</span><span class="sxs-lookup"><span data-stu-id="71a00-189">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="71a00-190">Evidenziare una riga e fare clic su **Elimina** per rimuovere la riga.</span><span class="sxs-lookup"><span data-stu-id="71a00-190">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="71a00-191">L'entità verrà eliminata quando si fa clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="71a00-191">The entity will be deleted when you click **Save**.</span></span>

![Applicazione in esecuzione](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="71a00-193">Notifica di modifiche alle proprietà</span><span class="sxs-lookup"><span data-stu-id="71a00-193">Property Change Notification</span></span>

<span data-ttu-id="71a00-194">Questo esempio si basa su quattro passaggi per sincronizzare le entità con l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="71a00-194">This example relies on four steps to synchronize the entities with the UI.</span></span>

1. <span data-ttu-id="71a00-195">La chiamata iniziale `_context.Categories.Load()` carica i dati delle categorie.</span><span class="sxs-lookup"><span data-stu-id="71a00-195">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="71a00-196">I proxy di caricamento lazy caricano i dati dei prodotti dipendenti.</span><span class="sxs-lookup"><span data-stu-id="71a00-196">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="71a00-197">Il rilevamento delle modifiche incorporato EF Core apporta le modifiche necessarie alle entità, incluse le operazioni di inserimento ed eliminazione, quando `_context.SaveChanges()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="71a00-197">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="71a00-198">Chiamate per `DataGridView.Items.Refresh()` forzare un ricaricamento con gli ID appena generati.</span><span class="sxs-lookup"><span data-stu-id="71a00-198">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="71a00-199">Questa operazione è valida per l'esempio introduttivo, ma potrebbe essere necessario codice aggiuntivo per altri scenari.</span><span class="sxs-lookup"><span data-stu-id="71a00-199">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="71a00-200">I controlli WPF eseguono il rendering dell'interfaccia utente leggendo i campi e le proprietà delle entità.</span><span class="sxs-lookup"><span data-stu-id="71a00-200">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="71a00-201">Quando si modifica un valore nell'interfaccia utente, tale valore viene passato all'entità.</span><span class="sxs-lookup"><span data-stu-id="71a00-201">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="71a00-202">Quando si modifica il valore di una proprietà direttamente nell'entità, ad esempio il caricamento dal database, WPF non rifletterà immediatamente le modifiche nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="71a00-202">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="71a00-203">Il motore di rendering deve ricevere notifiche delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="71a00-203">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="71a00-204">Il progetto ha fatto questa operazione chiamando manualmente `Refresh()` .</span><span class="sxs-lookup"><span data-stu-id="71a00-204">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="71a00-205">Un modo semplice per automatizzare questa notifica consiste nell'implementare l'interfaccia [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) .</span><span class="sxs-lookup"><span data-stu-id="71a00-205">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="71a00-206">I componenti WPF rileveranno automaticamente l'interfaccia e si registreranno per gli eventi di modifica.</span><span class="sxs-lookup"><span data-stu-id="71a00-206">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="71a00-207">L'entità è responsabile della generazione di questi eventi.</span><span class="sxs-lookup"><span data-stu-id="71a00-207">The entity is responsible for raising these events.</span></span>

> [!TIP]
> <span data-ttu-id="71a00-208">Per ulteriori informazioni su come gestire le modifiche, vedere [la pagina relativa all'implementazione della notifica di modifica delle proprietà](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="71a00-208">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="71a00-209">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="71a00-209">Next Steps</span></span>

<span data-ttu-id="71a00-210">Altre informazioni sulla [configurazione di un DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="71a00-210">Learn more about [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
