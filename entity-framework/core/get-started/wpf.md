---
title: Introduzione a WPF-EF Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535635"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="b7f90-102">Introduzione a WPF</span><span class="sxs-lookup"><span data-stu-id="b7f90-102">Getting Started with WPF</span></span>

<span data-ttu-id="b7f90-103">Questa procedura dettagliata illustra come associare tipi POCO ai controlli WPF in un modulo "Main-Detail".</span><span class="sxs-lookup"><span data-stu-id="b7f90-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="b7f90-104">L'applicazione usa le API Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="b7f90-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="b7f90-105">Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: **categoria** (principale \\ principale) e **prodotto** ( \\ Dettagli dipendenti).</span><span class="sxs-lookup"><span data-stu-id="b7f90-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="b7f90-106">Il Framework di associazione dei dati WPF consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="b7f90-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="b7f90-107">Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="b7f90-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="b7f90-108">È possibile visualizzare l'esempio di questo articolo [in GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span><span class="sxs-lookup"><span data-stu-id="b7f90-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="b7f90-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="b7f90-109">Pre-Requisites</span></span>

* <span data-ttu-id="b7f90-110">Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2019 16,3 o versione successiva con il **carico di lavoro di .NET desktop** selezionato.</span><span class="sxs-lookup"><span data-stu-id="b7f90-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="b7f90-111">Per ulteriori informazioni sull'installazione della versione più recente di Visual Studio, vedere [Install Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="b7f90-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="b7f90-112">Creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="b7f90-112">Create the Application</span></span>

1. <span data-ttu-id="b7f90-113">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7f90-113">Open Visual Studio</span></span>
2. <span data-ttu-id="b7f90-114">Nella finestra iniziale scegliere **Crea nuovo progetto**.</span><span class="sxs-lookup"><span data-stu-id="b7f90-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="b7f90-115">Cercare "WPF", scegliere **app WPF (.NET Core)** , quindi fare clic su **Avanti**.</span><span class="sxs-lookup"><span data-stu-id="b7f90-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="b7f90-116">Nella schermata successiva assegnare un nome al progetto, ad esempio **GetStartedWPF**, e scegliere **Crea.**</span><span class="sxs-lookup"><span data-stu-id="b7f90-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="b7f90-117">Installare i pacchetti NuGet di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b7f90-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="b7f90-118">Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione...**</span><span class="sxs-lookup"><span data-stu-id="b7f90-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="b7f90-120">Digitare `entityframeworkcore.sqlite` nella casella di ricerca.</span><span class="sxs-lookup"><span data-stu-id="b7f90-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="b7f90-121">Selezionare il pacchetto **Microsoft. EntityFrameworkCore. sqlite** .</span><span class="sxs-lookup"><span data-stu-id="b7f90-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="b7f90-122">Controllare il progetto nel riquadro destro e fare clic su **Installa** .</span><span class="sxs-lookup"><span data-stu-id="b7f90-122">Check the project in the right pane and click **Install**</span></span>

    ![Pacchetto sqlite](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="b7f90-124">Ripetere i passaggi per cercare `entityframeworkcore.proxies` e installare **Microsoft. EntityFrameworkCore. proxy**.</span><span class="sxs-lookup"><span data-stu-id="b7f90-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="b7f90-125">Quando è stato installato il pacchetto SQLite, viene automaticamente eseguito il pull del pacchetto di base **Microsoft. EntityFrameworkCore** correlato.</span><span class="sxs-lookup"><span data-stu-id="b7f90-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="b7f90-126">Il pacchetto **Microsoft. EntityFrameworkCore. proxy** fornisce supporto per i dati di caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="b7f90-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="b7f90-127">Ciò significa che quando si hanno entità con entità figlio, solo gli elementi padre vengono recuperati al caricamento iniziale.</span><span class="sxs-lookup"><span data-stu-id="b7f90-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="b7f90-128">I proxy rilevano quando viene eseguito un tentativo di accesso alle entità figlio e li carica automaticamente su richiesta.</span><span class="sxs-lookup"><span data-stu-id="b7f90-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="b7f90-129">Definire un modello</span><span class="sxs-lookup"><span data-stu-id="b7f90-129">Define a Model</span></span>

<span data-ttu-id="b7f90-130">In questa procedura dettagliata verrà implementato un modello utilizzando "Code First".</span><span class="sxs-lookup"><span data-stu-id="b7f90-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="b7f90-131">Ciò significa che EF Core creerà le tabelle e lo schema del database in base alle classi C# definite.</span><span class="sxs-lookup"><span data-stu-id="b7f90-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="b7f90-132">Aggiungere una nuova classe.</span><span class="sxs-lookup"><span data-stu-id="b7f90-132">Add a new class.</span></span> <span data-ttu-id="b7f90-133">Assegnargli il nome: `Product.cs` e popolarlo come segue:</span><span class="sxs-lookup"><span data-stu-id="b7f90-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="b7f90-134">Aggiungere quindi una classe denominata `Category.cs` e popolarla con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b7f90-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="b7f90-135">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="b7f90-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="b7f90-136">In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="b7f90-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="b7f90-137">Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; .</span><span class="sxs-lookup"><span data-stu-id="b7f90-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="b7f90-138">Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.</span><span class="sxs-lookup"><span data-stu-id="b7f90-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="b7f90-139">Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.</span><span class="sxs-lookup"><span data-stu-id="b7f90-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="b7f90-140">Aggiungere una nuova `ProductContext.cs` classe al progetto con la definizione seguente:</span><span class="sxs-lookup"><span data-stu-id="b7f90-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="b7f90-141">`DbSet`Informa EF Core quali entità C# devono essere mappate al database.</span><span class="sxs-lookup"><span data-stu-id="b7f90-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="b7f90-142">Esistono diversi modi per configurare la EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="b7f90-143">È possibile leggere le informazioni in: [configurazione di un DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b7f90-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="b7f90-144">Questo esempio usa l' `OnConfiguring` override per specificare un file di dati SQLite.</span><span class="sxs-lookup"><span data-stu-id="b7f90-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="b7f90-145">La `UseLazyLoadingProxies` chiamata indica EF Core di implementare il caricamento lazy, in modo che le entità figlio vengano caricate automaticamente al momento dell'accesso dall'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="b7f90-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="b7f90-146">Premere **CTRL + MAIUSC + B** o passare a **Compila compilazione &gt; soluzione** per compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="b7f90-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="b7f90-147">Per informazioni sulle diverse operazioni, vedere la pagina relativa alla gestione degli schemi di [database](/ef/core/managing-schemas)e dei modelli di EF core in sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="b7f90-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="b7f90-148">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="b7f90-148">Lazy Loading</span></span>

<span data-ttu-id="b7f90-149">La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="b7f90-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="b7f90-150">In Entity Framework Core, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="b7f90-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="b7f90-151">EF Core offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="b7f90-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="b7f90-152">Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.</span><span class="sxs-lookup"><span data-stu-id="b7f90-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="b7f90-153">Quando si usano tipi di entità POCO (Plain Old C# Object), EF Core ottiene il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento.</span><span class="sxs-lookup"><span data-stu-id="b7f90-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="b7f90-154">Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) e la classe non deve essere **sealed** (**NotOverridable** in Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="b7f90-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="b7f90-155">Quando si usa Database First, le proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="b7f90-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="b7f90-156">Associa oggetto a controlli</span><span class="sxs-lookup"><span data-stu-id="b7f90-156">Bind Object to Controls</span></span>

<span data-ttu-id="b7f90-157">Aggiungere le classi definite nel modello come origini dati per l'applicazione WPF.</span><span class="sxs-lookup"><span data-stu-id="b7f90-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="b7f90-158">Fare doppio clic su **MainWindow. XAML** in Esplora soluzioni per aprire il modulo principale</span><span class="sxs-lookup"><span data-stu-id="b7f90-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="b7f90-159">Scegliere la scheda **XAML** per modificare il codice XAML.</span><span class="sxs-lookup"><span data-stu-id="b7f90-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="b7f90-160">Subito dopo il tag di apertura `Window` aggiungere le origini seguenti per connettersi alle entità EF core.</span><span class="sxs-lookup"><span data-stu-id="b7f90-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="b7f90-161">In questo modo viene impostata l'origine per le categorie "padre" e la seconda origine per i prodotti "Detail".</span><span class="sxs-lookup"><span data-stu-id="b7f90-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="b7f90-162">Successivamente, aggiungere il markup seguente al codice XAML dopo il tag di chiusura `Window.Resources` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="b7f90-163">Si noti che la `CategoryId` è impostata su `ReadOnly` perché è assegnata dal database e non può essere modificata.</span><span class="sxs-lookup"><span data-stu-id="b7f90-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="b7f90-164">Aggiunta di una griglia dettagli</span><span class="sxs-lookup"><span data-stu-id="b7f90-164">Adding a Details Grid</span></span>

<span data-ttu-id="b7f90-165">Ora che la griglia esiste per visualizzare le categorie, è possibile aggiungere la griglia dei dettagli per visualizzare i prodotti.</span><span class="sxs-lookup"><span data-stu-id="b7f90-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="b7f90-166">Infine, aggiungere un `Save` pulsante e un cavo nell'evento click a `Button_Click` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="b7f90-167">La visualizzazione progettazione dovrebbe essere simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="b7f90-167">Your design view should look like this:</span></span>

![Screenshot della finestra di progettazione WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="b7f90-169">Aggiungere codice che gestisce l'interazione dei dati</span><span class="sxs-lookup"><span data-stu-id="b7f90-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="b7f90-170">È il momento di aggiungere alcuni gestori di eventi alla finestra principale.</span><span class="sxs-lookup"><span data-stu-id="b7f90-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="b7f90-171">Nella finestra XAML fare clic sull'elemento \*\* &lt; Window ( &gt; finestra\*\* ) per selezionare la finestra principale.</span><span class="sxs-lookup"><span data-stu-id="b7f90-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="b7f90-172">Nella finestra **Proprietà** scegliere **eventi** in alto a destra, quindi fare doppio clic sulla casella di testo a destra dell'etichetta **caricata** .</span><span class="sxs-lookup"><span data-stu-id="b7f90-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Proprietà della finestra principale](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="b7f90-174">In questo modo viene riportato il codice sottostante per il modulo. verrà ora modificato il codice per usare `ProductContext` per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="b7f90-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="b7f90-175">Aggiornare il codice come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="b7f90-175">Update the code as shown below.</span></span>

<span data-ttu-id="b7f90-176">Il codice dichiara un'istanza con esecuzione prolungata di `ProductContext` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="b7f90-177">L' `ProductContext` oggetto viene utilizzato per eseguire query e salvare i dati nel database.</span><span class="sxs-lookup"><span data-stu-id="b7f90-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="b7f90-178">Il `Dispose()` metodo sull' `ProductContext` istanza viene quindi chiamato dal metodo sottoposto a override `OnClosing` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="b7f90-179">I commenti del codice spiegano cosa accade in ogni passaggio.</span><span class="sxs-lookup"><span data-stu-id="b7f90-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="b7f90-180">Il codice usa una chiamata a `EnsureCreated()` per compilare il database alla prima esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b7f90-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="b7f90-181">Questa operazione è accettabile per le demo, ma nelle app di produzione è consigliabile esaminare le [migrazioni](/ef/core/managing-schemas/migrations/) per gestire lo schema.</span><span class="sxs-lookup"><span data-stu-id="b7f90-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="b7f90-182">Anche il codice viene eseguito in modo sincrono perché usa un database SQLite locale.</span><span class="sxs-lookup"><span data-stu-id="b7f90-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="b7f90-183">Per gli scenari di produzione che in genere coinvolgono un server remoto, è consigliabile usare le versioni asincrone dei `Load` `SaveChanges` metodi e.</span><span class="sxs-lookup"><span data-stu-id="b7f90-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="b7f90-184">Testare l'applicazione WPF</span><span class="sxs-lookup"><span data-stu-id="b7f90-184">Test the WPF Application</span></span>

<span data-ttu-id="b7f90-185">Compilare ed eseguire l'applicazione premendo **F5** o scegliendo **debug &gt; Avvia debug**.</span><span class="sxs-lookup"><span data-stu-id="b7f90-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="b7f90-186">Il database deve essere creato automaticamente con un file denominato `products.db` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="b7f90-187">Immettere un nome di categoria e premere INVIO, quindi aggiungere prodotti alla griglia inferiore.</span><span class="sxs-lookup"><span data-stu-id="b7f90-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="b7f90-188">Fare clic su Salva e controllare l'aggiornamento della griglia con gli ID forniti dal database.</span><span class="sxs-lookup"><span data-stu-id="b7f90-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="b7f90-189">Evidenziare una riga e fare clic su **Elimina** per rimuovere la riga.</span><span class="sxs-lookup"><span data-stu-id="b7f90-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="b7f90-190">L'entità verrà eliminata quando si fa clic su **Salva**.</span><span class="sxs-lookup"><span data-stu-id="b7f90-190">The entity will be deleted when you click **Save**.</span></span>

![Applicazione in esecuzione](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="b7f90-192">Notifica di modifiche alle proprietà</span><span class="sxs-lookup"><span data-stu-id="b7f90-192">Property Change Notification</span></span>

<span data-ttu-id="b7f90-193">Questo esempio si basa su quattro passaggi per sincronizzare le entità con l'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="b7f90-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="b7f90-194">La chiamata iniziale `_context.Categories.Load()` carica i dati delle categorie.</span><span class="sxs-lookup"><span data-stu-id="b7f90-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="b7f90-195">I proxy di caricamento lazy caricano i dati dei prodotti dipendenti.</span><span class="sxs-lookup"><span data-stu-id="b7f90-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="b7f90-196">Il rilevamento delle modifiche incorporato EF Core apporta le modifiche necessarie alle entità, incluse le operazioni di inserimento ed eliminazione, quando `_context.SaveChanges()` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="b7f90-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="b7f90-197">Chiamate per `DataGridView.Items.Refresh()` forzare un ricaricamento con gli ID appena generati.</span><span class="sxs-lookup"><span data-stu-id="b7f90-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="b7f90-198">Questa operazione è valida per l'esempio introduttivo, ma potrebbe essere necessario codice aggiuntivo per altri scenari.</span><span class="sxs-lookup"><span data-stu-id="b7f90-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="b7f90-199">I controlli WPF eseguono il rendering dell'interfaccia utente leggendo i campi e le proprietà delle entità.</span><span class="sxs-lookup"><span data-stu-id="b7f90-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="b7f90-200">Quando si modifica un valore nell'interfaccia utente, tale valore viene passato all'entità.</span><span class="sxs-lookup"><span data-stu-id="b7f90-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="b7f90-201">Quando si modifica il valore di una proprietà direttamente nell'entità, ad esempio il caricamento dal database, WPF non rifletterà immediatamente le modifiche nell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="b7f90-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="b7f90-202">Il motore di rendering deve ricevere notifiche delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="b7f90-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="b7f90-203">Il progetto ha fatto questa operazione chiamando manualmente `Refresh()` .</span><span class="sxs-lookup"><span data-stu-id="b7f90-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="b7f90-204">Un modo semplice per automatizzare questa notifica consiste nell'implementare l'interfaccia [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) .</span><span class="sxs-lookup"><span data-stu-id="b7f90-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="b7f90-205">I componenti WPF rileveranno automaticamente l'interfaccia e si registreranno per gli eventi di modifica.</span><span class="sxs-lookup"><span data-stu-id="b7f90-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="b7f90-206">L'entità è responsabile della generazione di questi eventi.</span><span class="sxs-lookup"><span data-stu-id="b7f90-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="b7f90-207">Per ulteriori informazioni su come gestire le modifiche, vedere [la pagina relativa all'implementazione della notifica di modifica delle proprietà](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="b7f90-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b7f90-208">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="b7f90-208">Next Steps</span></span>

<span data-ttu-id="b7f90-209">Altre informazioni sulla [configurazione di un DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="b7f90-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>