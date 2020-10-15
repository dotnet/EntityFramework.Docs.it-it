---
title: Model First-EF6
description: Model First in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: b47c5805a63a1ea94baad4fb5eb137b633f6be1d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066031"
---
# <a name="model-first"></a><span data-ttu-id="e4a39-103">Model First</span><span class="sxs-lookup"><span data-stu-id="e4a39-103">Model First</span></span>
<span data-ttu-id="e4a39-104">Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo Model First con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e4a39-104">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="e4a39-105">Model First consente di creare un nuovo modello usando il Entity Framework Designer e quindi di generare uno schema del database dal modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-105">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="e4a39-106">Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="e4a39-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="e4a39-107">Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.</span><span class="sxs-lookup"><span data-stu-id="e4a39-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="e4a39-108">Video</span><span class="sxs-lookup"><span data-stu-id="e4a39-108">Watch the video</span></span>
<span data-ttu-id="e4a39-109">Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo Model First con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e4a39-109">This video and step-by-step walkthrough provide an introduction to Model First development using Entity Framework.</span></span> <span data-ttu-id="e4a39-110">Model First consente di creare un nuovo modello usando il Entity Framework Designer e quindi di generare uno schema del database dal modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-110">Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model.</span></span> <span data-ttu-id="e4a39-111">Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="e4a39-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="e4a39-112">Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.</span><span class="sxs-lookup"><span data-stu-id="e4a39-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="e4a39-113">**Presentato da**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="e4a39-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="e4a39-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span><span class="sxs-lookup"><span data-stu-id="e4a39-114">**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv) | [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="e4a39-115">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="e4a39-115">Pre-Requisites</span></span>

<span data-ttu-id="e4a39-116">Per completare questa procedura dettagliata, è necessario che Visual Studio 2010 o Visual Studio 2012 sia installato.</span><span class="sxs-lookup"><span data-stu-id="e4a39-116">You will need to have Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="e4a39-117">Se si usa Visual Studio 2010, sarà anche necessario che [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) sia installato.</span><span class="sxs-lookup"><span data-stu-id="e4a39-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="e4a39-118">1. creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="e4a39-118">1. Create the Application</span></span>

<span data-ttu-id="e4a39-119">Per semplificare le operazioni, verrà compilata un'applicazione console di base che usa il Model First per eseguire l'accesso ai dati:</span><span class="sxs-lookup"><span data-stu-id="e4a39-119">To keep things simple we’re going to build a basic console application that uses the Model First to perform data access:</span></span>

-   <span data-ttu-id="e4a39-120">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4a39-120">Open Visual Studio</span></span>
-   <span data-ttu-id="e4a39-121">**File- &gt; nuovo- &gt; progetto...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="e4a39-122">Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**</span><span class="sxs-lookup"><span data-stu-id="e4a39-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="e4a39-123">Immettere **ModelFirstSample** come nome</span><span class="sxs-lookup"><span data-stu-id="e4a39-123">Enter **ModelFirstSample** as the name</span></span>
-   <span data-ttu-id="e4a39-124">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="e4a39-124">Select **OK**</span></span>

## <a name="2-create-model"></a><span data-ttu-id="e4a39-125">2. creare un modello</span><span class="sxs-lookup"><span data-stu-id="e4a39-125">2. Create Model</span></span>

<span data-ttu-id="e4a39-126">Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e4a39-126">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="e4a39-127">**Progetto- &gt; Aggiungi nuovo elemento...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-127">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="e4a39-128">Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="e4a39-128">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="e4a39-129">Immettere **BloggingModel** come nome e fare clic su **OK**. viene avviata la procedura guidata Entity Data Model</span><span class="sxs-lookup"><span data-stu-id="e4a39-129">Enter **BloggingModel** as the name and click **OK**, this launches the Entity Data Model Wizard</span></span>
-   <span data-ttu-id="e4a39-130">Selezionare il **modello vuoto** e fare clic su **fine** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-130">Select **Empty Model** and click **Finish**</span></span>

    ![Crea modello vuoto](~/ef6/media/createemptymodel.png)

<span data-ttu-id="e4a39-132">Il Entity Framework Designer viene aperto con un modello vuoto.</span><span class="sxs-lookup"><span data-stu-id="e4a39-132">The Entity Framework Designer is opened with a blank model.</span></span> <span data-ttu-id="e4a39-133">A questo punto è possibile iniziare ad aggiungere entità, proprietà e associazioni al modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-133">Now we can start adding entities, properties and associations to the model.</span></span>

-   <span data-ttu-id="e4a39-134">Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Proprietà** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-134">Right-click on the design surface and select **Properties**</span></span>
-   <span data-ttu-id="e4a39-135">Nella finestra Proprietà modificare il **nome del contenitore di entità** in **BloggingContext** 
     *questo è il nome del contesto derivato che verrà generato automaticamente, il contesto rappresenta una sessione con il database, consentendo di eseguire query e salvare i dati*</span><span class="sxs-lookup"><span data-stu-id="e4a39-135">In the Properties window change the **Entity Container Name** to **BloggingContext**
*This is the name of the derived context that will be generated for you, the context represents a session with the database, allowing us to query and save data*</span></span>
-   <span data-ttu-id="e4a39-136">Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Aggiungi nuova &gt; entità...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-136">Right-click on the design surface and select **Add New -&gt; Entity…**</span></span>
-   <span data-ttu-id="e4a39-137">Immettere **Blog** come nome entità e **BlogId** come nome chiave e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-137">Enter **Blog** as the entity name and **BlogId** as the key name and click **OK**</span></span>

    ![Aggiungi entità Blog](~/ef6/media/addblogentity.png)

-   <span data-ttu-id="e4a39-139">Fare clic con il pulsante destro del mouse sulla nuova entità nell'area di progettazione e scegliere **Aggiungi nuova &gt; proprietà scalare**, immettere **nome** come nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="e4a39-139">Right-click on the new entity on the design surface and select **Add New -&gt; Scalar Property**, enter **Name** as the name of the property.</span></span>
-   <span data-ttu-id="e4a39-140">Ripetere questo processo per aggiungere una proprietà **URL** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-140">Repeat this process to add a **Url** property.</span></span>
-   <span data-ttu-id="e4a39-141">Fare clic con il pulsante destro del mouse sulla proprietà **URL** nell'area di progettazione e selezionare **Proprietà**, nel finestra Proprietà modificare l'impostazione **Nullable** su **true**. in 
     *questo modo è possibile salvare un blog nel database senza assegnargli un URL*</span><span class="sxs-lookup"><span data-stu-id="e4a39-141">Right-click on the **Url** property on the design surface and select **Properties**, in the Properties window change the **Nullable** setting to **True**
*This allows us to save a Blog to the database without assigning it a Url*</span></span>
-   <span data-ttu-id="e4a39-142">Usando le tecniche appena apprese, aggiungere un'entità **post** con una proprietà della chiave **postid**</span><span class="sxs-lookup"><span data-stu-id="e4a39-142">Using the techniques you just learnt, add a **Post** entity with a **PostId** key property</span></span>
-   <span data-ttu-id="e4a39-143">Aggiungere le proprietà scalari del **titolo** e del **contenuto** all'entità **post**</span><span class="sxs-lookup"><span data-stu-id="e4a39-143">Add **Title** and **Content** scalar properties to the **Post** entity</span></span>

<span data-ttu-id="e4a39-144">Ora che sono presenti due entità, è possibile aggiungere un'associazione (o relazione) tra di esse.</span><span class="sxs-lookup"><span data-stu-id="e4a39-144">Now that we have a couple of entities, it’s time to add an association (or relationship) between them.</span></span>

-   <span data-ttu-id="e4a39-145">Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Aggiungi nuova &gt; associazione...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-145">Right-click on the design surface and select **Add New -&gt; Association…**</span></span>
-   <span data-ttu-id="e4a39-146">Fare in modo che un'estremità della relazione punti al **Blog** con una molteplicità di **uno** e l'altro punto finale a **post** con una molteplicità di **molti**di 
     *questi significa che un Blog ha molti post e un post appartiene a* un Blog</span><span class="sxs-lookup"><span data-stu-id="e4a39-146">Make one end of the relationship point to **Blog** with a multiplicity of **One** and the other end point to **Post** with a multiplicity of **Many**
*This means that a Blog has many Posts and a Post belongs to one Blog*</span></span>
-   <span data-ttu-id="e4a39-147">Verificare che la casella dell' **entità Aggiungi proprietà di chiave esterna a "post"** sia selezionata e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-147">Ensure the **Add foreign key properties to 'Post' Entity** box is checked and click **OK**</span></span>

    ![Aggiungi associazione MF](~/ef6/media/addassociationmf.png)

<span data-ttu-id="e4a39-149">È ora disponibile un semplice modello che consente di generare un database da e di usare per leggere e scrivere i dati.</span><span class="sxs-lookup"><span data-stu-id="e4a39-149">We now have a simple model that we can generate a database from and use to read and write data.</span></span>

![Modello iniziale](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="e4a39-151">Passaggi aggiuntivi in Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="e4a39-151">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="e4a39-152">Se si lavora in Visual Studio 2010, è necessario seguire alcuni passaggi aggiuntivi per eseguire l'aggiornamento alla versione più recente di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e4a39-152">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="e4a39-153">L'aggiornamento è importante perché consente di accedere a una superficie API migliorata, molto più semplice da usare, nonché le correzioni di bug più recenti.</span><span class="sxs-lookup"><span data-stu-id="e4a39-153">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="e4a39-154">Prima di tutto, è necessario ottenere la versione più recente di Entity Framework da NuGet.</span><span class="sxs-lookup"><span data-stu-id="e4a39-154">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="e4a39-155">**Progetto- &gt; Gestisci pacchetti NuGet...** 
     \*Se non si ha l'opzione **Gestisci pacchetti NuGet...** è necessario installare la [versione più recente di NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="e4a39-155">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="e4a39-156">Selezionare la scheda **online**</span><span class="sxs-lookup"><span data-stu-id="e4a39-156">Select the **Online** tab</span></span>
-   <span data-ttu-id="e4a39-157">Selezionare il pacchetto **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="e4a39-157">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="e4a39-158">Fai clic su **Install** (Installa).</span><span class="sxs-lookup"><span data-stu-id="e4a39-158">Click **Install**</span></span>

<span data-ttu-id="e4a39-159">Successivamente, è necessario scambiare il modello per generare il codice che usa l'API DbContext, introdotta nelle versioni successive di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="e4a39-159">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="e4a39-160">Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-160">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="e4a39-161">Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**</span><span class="sxs-lookup"><span data-stu-id="e4a39-161">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="e4a39-162">Selezionare il \*\*Generatore EF 5. x DbContext per C \# \*\*, immettere **BloggingModel** come nome e fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-162">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![Modello DbContext](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a><span data-ttu-id="e4a39-164">3. generazione del database</span><span class="sxs-lookup"><span data-stu-id="e4a39-164">3. Generating the Database</span></span>

<span data-ttu-id="e4a39-165">Dato il modello, Entity Framework possibile calcolare uno schema del database che consentirà di archiviare e recuperare i dati utilizzando il modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-165">Given our model, Entity Framework can calculate a database schema that will allow us to store and retrieve data using the model.</span></span>

<span data-ttu-id="e4a39-166">Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:</span><span class="sxs-lookup"><span data-stu-id="e4a39-166">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="e4a39-167">Se si usa Visual Studio 2010 verrà creato un database di SQL Express.</span><span class="sxs-lookup"><span data-stu-id="e4a39-167">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="e4a39-168">Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .</span><span class="sxs-lookup"><span data-stu-id="e4a39-168">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

<span data-ttu-id="e4a39-169">Procediamo con la generazione del database.</span><span class="sxs-lookup"><span data-stu-id="e4a39-169">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="e4a39-170">Fare clic con il pulsante destro del mouse sull'area di progettazione e selezionare **genera database da modello...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-170">Right-click on the design surface and select **Generate Database from Model…**</span></span>
-   <span data-ttu-id="e4a39-171">Fare clic su **nuova connessione...**</span><span class="sxs-lookup"><span data-stu-id="e4a39-171">Click **New Connection…**</span></span> <span data-ttu-id="e4a39-172">e specificano il database locale o SQL Express, a seconda della versione di Visual Studio in uso, immettere **ModelFirst. blogging** come nome del database.</span><span class="sxs-lookup"><span data-stu-id="e4a39-172">and specify either LocalDB or SQL Express, depending on which version of Visual Studio you are using, enter **ModelFirst.Blogging** as the database name.</span></span>

    ![Connessione al database locale MF](~/ef6/media/localdbconnectionmf.png)

    ![Connessione SQL Express MF](~/ef6/media/sqlexpressconnectionmf.png)

-   <span data-ttu-id="e4a39-175">Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="e4a39-175">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="e4a39-176">Fare clic su **Avanti** . il Entity Framework Designer calcolerà uno script per creare lo schema del database</span><span class="sxs-lookup"><span data-stu-id="e4a39-176">Select **Next** and the Entity Framework Designer will calculate a script to create the database schema</span></span>
-   <span data-ttu-id="e4a39-177">Una volta visualizzato lo script, fare clic su **fine** e lo script verrà aggiunto al progetto e aperto</span><span class="sxs-lookup"><span data-stu-id="e4a39-177">Once the script is displayed, click **Finish** and the script will be added to your project and opened</span></span>
-   <span data-ttu-id="e4a39-178">Fare clic con il pulsante destro del mouse sullo script e scegliere **Esegui**. verrà richiesto di specificare il database a cui connettersi, specificare il database locale o SQL Server Express, a seconda della versione di Visual Studio in uso</span><span class="sxs-lookup"><span data-stu-id="e4a39-178">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="e4a39-179">4. lettura & scrittura di dati</span><span class="sxs-lookup"><span data-stu-id="e4a39-179">4. Reading & Writing Data</span></span>

<span data-ttu-id="e4a39-180">Ora che è disponibile un modello, è possibile usarlo per accedere ai dati.</span><span class="sxs-lookup"><span data-stu-id="e4a39-180">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="e4a39-181">Le classi da usare per accedere ai dati vengono automaticamente generate in base al file EDMX.</span><span class="sxs-lookup"><span data-stu-id="e4a39-181">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="e4a39-182">*Questa schermata è da Visual Studio 2012. Se si usa Visual Studio 2010, i file BloggingModel.tt e BloggingModel.Context.tt saranno direttamente nel progetto anziché annidati nel file EDMX.*</span><span class="sxs-lookup"><span data-stu-id="e4a39-182">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![Classi generate](~/ef6/media/generatedclasses.png)

<span data-ttu-id="e4a39-184">Implementare il metodo Main in Program.cs, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e4a39-184">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="e4a39-185">Questo codice crea una nuova istanza del contesto e la usa per inserire un nuovo blog.</span><span class="sxs-lookup"><span data-stu-id="e4a39-185">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="e4a39-186">USA quindi una query LINQ per recuperare tutti i Blog dal database ordinati alfabeticamente in base al titolo.</span><span class="sxs-lookup"><span data-stu-id="e4a39-186">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="e4a39-187">È ora possibile eseguire l'applicazione ed eseguirne il test.</span><span class="sxs-lookup"><span data-stu-id="e4a39-187">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="e4a39-188">5. gestione delle modifiche al modello</span><span class="sxs-lookup"><span data-stu-id="e4a39-188">5. Dealing with Model Changes</span></span>

<span data-ttu-id="e4a39-189">A questo punto è possibile apportare alcune modifiche al modello. quando si apportano queste modifiche, è necessario aggiornare anche lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="e4a39-189">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span>

<span data-ttu-id="e4a39-190">Si inizierà aggiungendo una nuova entità User al modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-190">We’ll start by adding a new User entity to our model.</span></span>

-   <span data-ttu-id="e4a39-191">Aggiungere un nuovo nome di entità **utente** con **username** come nome della chiave e **stringa** come tipo di proprietà per la chiave</span><span class="sxs-lookup"><span data-stu-id="e4a39-191">Add a new **User** entity name with **Username** as the key name and **String** as the property type for the key</span></span>

    ![Aggiungi entità utente](~/ef6/media/adduserentity.png)

-   <span data-ttu-id="e4a39-193">Fare clic con il pulsante destro del mouse sulla proprietà **username** nell'area di progettazione e selezionare **Proprietà**, nel finestra Proprietà modificare l'impostazione **MaxLength** su **50**per 
     *limitare i dati che possono essere archiviati nel nome utente a 50 caratteri*</span><span class="sxs-lookup"><span data-stu-id="e4a39-193">Right-click on the **Username** property on the design surface and select **Properties**, In the Properties window change the **MaxLength** setting to **50**
*This restricts the data that can be stored in username to 50 characters*</span></span>
-   <span data-ttu-id="e4a39-194">Aggiungere una proprietà scalare **DisplayName** all'entità **User**</span><span class="sxs-lookup"><span data-stu-id="e4a39-194">Add a **DisplayName** scalar property to the **User** entity</span></span>

<span data-ttu-id="e4a39-195">A questo punto si dispone di un modello aggiornato e si è pronti per aggiornare il database in modo da includere il nuovo tipo di entità utente.</span><span class="sxs-lookup"><span data-stu-id="e4a39-195">We now have an updated model and we are ready to update the database to accommodate our new User entity type.</span></span>

-   <span data-ttu-id="e4a39-196">Fare clic con il pulsante destro del mouse sull'area di progettazione e selezionare **genera database da modello...** Entity Framework calcolerà uno script per ricreare uno schema basato sul modello aggiornato.</span><span class="sxs-lookup"><span data-stu-id="e4a39-196">Right-click on the design surface and select **Generate Database from Model…**, Entity Framework will calculate a script to recreate a schema based on the updated model.</span></span>
-   <span data-ttu-id="e4a39-197">Fare clic su **Fine**</span><span class="sxs-lookup"><span data-stu-id="e4a39-197">Click **Finish**</span></span>
-   <span data-ttu-id="e4a39-198">È possibile ricevere avvisi relativi alla sovrascrittura dello script DDL esistente e delle parti di mapping e archiviazione del modello, fare clic su **Sì** per entrambi gli avvisi</span><span class="sxs-lookup"><span data-stu-id="e4a39-198">You may receive warnings about overwriting the existing DDL script and the mapping and storage parts of the model, click **Yes** for both these warnings</span></span>
-   <span data-ttu-id="e4a39-199">Verrà aperto lo script SQL aggiornato per creare il database</span><span class="sxs-lookup"><span data-stu-id="e4a39-199">The updated SQL script to create the database is opened for you</span></span>  
    <span data-ttu-id="e4a39-200">*Lo script generato eliminerà tutte le tabelle esistenti e ricreerà lo schema da zero. Questo può funzionare per lo sviluppo locale, ma non è possibile eseguire il push delle modifiche a un database già distribuito. Se è necessario pubblicare le modifiche in un database che è già stato distribuito, sarà necessario modificare lo script o usare uno strumento di confronto dello schema per calcolare uno script di migrazione.*</span><span class="sxs-lookup"><span data-stu-id="e4a39-200">*The script that is generated will drop all existing tables and then recreate the schema from scratch. This may work for local development but is not a viable for pushing changes to a database that has already been deployed. If you need to publish changes to a database that has already been deployed, you will need to edit the script or use a schema compare tool to calculate a migration script.*</span></span>
-   <span data-ttu-id="e4a39-201">Fare clic con il pulsante destro del mouse sullo script e scegliere **Esegui**. verrà richiesto di specificare il database a cui connettersi, specificare il database locale o SQL Server Express, a seconda della versione di Visual Studio in uso</span><span class="sxs-lookup"><span data-stu-id="e4a39-201">Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to, specify LocalDB or SQL Server Express, depending on which version of Visual Studio you are using</span></span>

## <a name="summary"></a><span data-ttu-id="e4a39-202">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="e4a39-202">Summary</span></span>

<span data-ttu-id="e4a39-203">In questa procedura dettagliata è stato esaminato Model First sviluppo, che ci ha consentito di creare un modello nella finestra di progettazione EF e quindi di generare un database da tale modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-203">In this walkthrough we looked at Model First development, which allowed us to create a model in the EF Designer and then generate a database from that model.</span></span> <span data-ttu-id="e4a39-204">Il modello è stato quindi utilizzato per leggere e scrivere alcuni dati dal database.</span><span class="sxs-lookup"><span data-stu-id="e4a39-204">We then used the model to read and write some data from the database.</span></span> <span data-ttu-id="e4a39-205">Infine, il modello è stato aggiornato e quindi ricreato lo schema del database in modo che corrisponda al modello.</span><span class="sxs-lookup"><span data-stu-id="e4a39-205">Finally, we updated the model and then recreated the database schema to match the model.</span></span>
