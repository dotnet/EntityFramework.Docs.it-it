---
title: Database First-EF6
description: Database First in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 095fc6d6882df306816e65995ff314f1a0e7178a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620444"
---
# <a name="database-first"></a><span data-ttu-id="8a93e-103">Database First</span><span class="sxs-lookup"><span data-stu-id="8a93e-103">Database First</span></span>
<span data-ttu-id="8a93e-104">Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo Database First con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8a93e-104">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="8a93e-105">Database First consente di decompilare un modello da un database esistente.</span><span class="sxs-lookup"><span data-stu-id="8a93e-105">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="8a93e-106">Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="8a93e-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="8a93e-107">Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.</span><span class="sxs-lookup"><span data-stu-id="8a93e-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="8a93e-108">Video</span><span class="sxs-lookup"><span data-stu-id="8a93e-108">Watch the video</span></span>
<span data-ttu-id="8a93e-109">In questo video viene fornita un'introduzione allo sviluppo Database First tramite Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8a93e-109">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="8a93e-110">Database First consente di decompilare un modello da un database esistente.</span><span class="sxs-lookup"><span data-stu-id="8a93e-110">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="8a93e-111">Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="8a93e-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="8a93e-112">Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.</span><span class="sxs-lookup"><span data-stu-id="8a93e-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="8a93e-113">**Presentato da**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="8a93e-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="8a93e-114">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="8a93e-114">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="8a93e-115">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="8a93e-115">Pre-Requisites</span></span>

<span data-ttu-id="8a93e-116">Per completare questa procedura dettagliata, è necessario che sia installato almeno Visual Studio 2010 o Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="8a93e-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="8a93e-117">Se si usa Visual Studio 2010, sarà anche necessario che [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) sia installato.</span><span class="sxs-lookup"><span data-stu-id="8a93e-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="8a93e-118">1. creare un database esistente</span><span class="sxs-lookup"><span data-stu-id="8a93e-118">1. Create an Existing Database</span></span>

<span data-ttu-id="8a93e-119">In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.</span><span class="sxs-lookup"><span data-stu-id="8a93e-119">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="8a93e-120">Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:</span><span class="sxs-lookup"><span data-stu-id="8a93e-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="8a93e-121">Se si usa Visual Studio 2010 verrà creato un database di SQL Express.</span><span class="sxs-lookup"><span data-stu-id="8a93e-121">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="8a93e-122">Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .</span><span class="sxs-lookup"><span data-stu-id="8a93e-122">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="8a93e-123">Procediamo con la generazione del database.</span><span class="sxs-lookup"><span data-stu-id="8a93e-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="8a93e-124">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a93e-124">Open Visual Studio</span></span>
-   <span data-ttu-id="8a93e-125">**Visualizza- &gt; Esplora server**</span><span class="sxs-lookup"><span data-stu-id="8a93e-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="8a93e-126">Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**</span><span class="sxs-lookup"><span data-stu-id="8a93e-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="8a93e-127">Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati</span><span class="sxs-lookup"><span data-stu-id="8a93e-127">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Seleziona origine dati](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="8a93e-129">Connettersi al database locale o a SQL Express, a seconda di quale installato e immettere **DatabaseFirst. blogging** come nome del database</span><span class="sxs-lookup"><span data-stu-id="8a93e-129">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![Connessione SQL Express DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![Connessione database locale DF](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="8a93e-132">Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-132">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Finestra di dialogo Crea database](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="8a93e-134">Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-134">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="8a93e-135">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);
```

## <a name="2-create-the-application"></a><span data-ttu-id="8a93e-136">2. creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="8a93e-136">2. Create the Application</span></span>

<span data-ttu-id="8a93e-137">Per semplificare le operazioni, verrà compilata un'applicazione console di base che usa il Database First per eseguire l'accesso ai dati:</span><span class="sxs-lookup"><span data-stu-id="8a93e-137">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="8a93e-138">Aprire Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a93e-138">Open Visual Studio</span></span>
-   <span data-ttu-id="8a93e-139">**File- &gt; nuovo- &gt; progetto...**</span><span class="sxs-lookup"><span data-stu-id="8a93e-139">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="8a93e-140">Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**</span><span class="sxs-lookup"><span data-stu-id="8a93e-140">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="8a93e-141">Immettere **DatabaseFirstSample** come nome</span><span class="sxs-lookup"><span data-stu-id="8a93e-141">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="8a93e-142">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="8a93e-142">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="8a93e-143">3. Reverse Engineering Model</span><span class="sxs-lookup"><span data-stu-id="8a93e-143">3. Reverse Engineer Model</span></span>

<span data-ttu-id="8a93e-144">Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8a93e-144">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="8a93e-145">**Progetto- &gt; Aggiungi nuovo elemento...**</span><span class="sxs-lookup"><span data-stu-id="8a93e-145">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="8a93e-146">Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="8a93e-146">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="8a93e-147">Immettere **BloggingModel** come nome e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-147">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="8a93e-148">Viene avviata la **procedura guidata Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="8a93e-148">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="8a93e-149">Selezionare **genera da database** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-149">Select **Generate from Database** and click **Next**</span></span>

    ![Passaggio 1 della procedura guidata](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="8a93e-151">Selezionare la connessione al database creato nella prima sezione, immettere **BloggingContext** come nome della stringa di connessione e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-151">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![Passaggio 2 della procedura guidata](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="8a93e-153">Fare clic sulla casella di controllo accanto a' Tables ' per importare tutte le tabelle e fare clic su' Finish '</span><span class="sxs-lookup"><span data-stu-id="8a93e-153">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Passaggio 3 della procedura guidata](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="8a93e-155">Una volta completato il processo di Reverse Engineering, il nuovo modello viene aggiunto al progetto e aperto per la visualizzazione nel Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="8a93e-155">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="8a93e-156">Al progetto è stato aggiunto anche un file di App.config con i dettagli della connessione per il database.</span><span class="sxs-lookup"><span data-stu-id="8a93e-156">An App.config file has also been added to your project with the connection details for the database.</span></span>

![Modello iniziale](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="8a93e-158">Passaggi aggiuntivi in Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="8a93e-158">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="8a93e-159">Se si lavora in Visual Studio 2010, è necessario seguire alcuni passaggi aggiuntivi per eseguire l'aggiornamento alla versione più recente di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8a93e-159">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="8a93e-160">L'aggiornamento è importante perché consente di accedere a una superficie API migliorata, molto più semplice da usare, nonché le correzioni di bug più recenti.</span><span class="sxs-lookup"><span data-stu-id="8a93e-160">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="8a93e-161">Prima di tutto, è necessario ottenere la versione più recente di Entity Framework da NuGet.</span><span class="sxs-lookup"><span data-stu-id="8a93e-161">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="8a93e-162">**Progetto- &gt; Gestisci pacchetti NuGet...** 
     \*Se non si ha l'opzione **Gestisci pacchetti NuGet...** è necessario installare la [versione più recente di NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) \*</span><span class="sxs-lookup"><span data-stu-id="8a93e-162">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="8a93e-163">Selezionare la scheda **online**</span><span class="sxs-lookup"><span data-stu-id="8a93e-163">Select the **Online** tab</span></span>
-   <span data-ttu-id="8a93e-164">Selezionare il pacchetto **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="8a93e-164">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="8a93e-165">Fai clic su **Install** (Installa).</span><span class="sxs-lookup"><span data-stu-id="8a93e-165">Click **Install**</span></span>

<span data-ttu-id="8a93e-166">Successivamente, è necessario scambiare il modello per generare il codice che usa l'API DbContext, introdotta nelle versioni successive di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="8a93e-166">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="8a93e-167">Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**</span><span class="sxs-lookup"><span data-stu-id="8a93e-167">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="8a93e-168">Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**</span><span class="sxs-lookup"><span data-stu-id="8a93e-168">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="8a93e-169">Selezionare il \*\*Generatore EF 5. x DbContext per C \# \*\*, immettere **BloggingModel** come nome e fare clic su **Aggiungi** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-169">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![Modello DbContext](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="8a93e-171">4. lettura & scrittura di dati</span><span class="sxs-lookup"><span data-stu-id="8a93e-171">4. Reading & Writing Data</span></span>

<span data-ttu-id="8a93e-172">Ora che è disponibile un modello, è possibile usarlo per accedere ai dati.</span><span class="sxs-lookup"><span data-stu-id="8a93e-172">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="8a93e-173">Le classi da usare per accedere ai dati vengono automaticamente generate in base al file EDMX.</span><span class="sxs-lookup"><span data-stu-id="8a93e-173">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="8a93e-174">*Questa schermata è da Visual Studio 2012. Se si usa Visual Studio 2010, i file BloggingModel.tt e BloggingModel.Context.tt saranno direttamente nel progetto anziché annidati nel file EDMX.*</span><span class="sxs-lookup"><span data-stu-id="8a93e-174">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![Classi generate DF](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="8a93e-176">Implementare il metodo Main in Program.cs, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="8a93e-176">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="8a93e-177">Questo codice crea una nuova istanza del contesto e la usa per inserire un nuovo blog.</span><span class="sxs-lookup"><span data-stu-id="8a93e-177">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="8a93e-178">USA quindi una query LINQ per recuperare tutti i Blog dal database ordinati alfabeticamente in base al titolo.</span><span class="sxs-lookup"><span data-stu-id="8a93e-178">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

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

<span data-ttu-id="8a93e-179">È ora possibile eseguire l'applicazione ed eseguirne il test.</span><span class="sxs-lookup"><span data-stu-id="8a93e-179">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="8a93e-180">5. gestione delle modifiche apportate al database</span><span class="sxs-lookup"><span data-stu-id="8a93e-180">5. Dealing with Database Changes</span></span>

<span data-ttu-id="8a93e-181">A questo punto è possibile apportare alcune modifiche allo schema del database. quando si apportano queste modifiche, è necessario aggiornare il modello in modo da riflettere le modifiche.</span><span class="sxs-lookup"><span data-stu-id="8a93e-181">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="8a93e-182">Il primo passaggio consiste nel apportare alcune modifiche allo schema del database.</span><span class="sxs-lookup"><span data-stu-id="8a93e-182">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="8a93e-183">Verrà aggiunta una tabella users allo schema.</span><span class="sxs-lookup"><span data-stu-id="8a93e-183">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="8a93e-184">Fare clic con il pulsante destro del mouse sul database **DatabaseFirst. blogging** in Esplora server e selezionare **nuova query** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-184">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="8a93e-185">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="8a93e-185">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="8a93e-186">Ora che lo schema è stato aggiornato, è necessario aggiornare il modello con tali modifiche.</span><span class="sxs-lookup"><span data-stu-id="8a93e-186">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="8a93e-187">Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e selezionare "Aggiorna modello da database". verrà avviata l'aggiornamento guidato</span><span class="sxs-lookup"><span data-stu-id="8a93e-187">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="8a93e-188">Nella scheda Aggiungi della procedura guidata di aggiornamento selezionare la casella accanto a tabelle per indicare che si desidera aggiungere nuove tabelle dallo schema.</span><span class="sxs-lookup"><span data-stu-id="8a93e-188">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="8a93e-189">*La scheda Aggiorna Mostra tutte le tabelle esistenti nel modello di cui verrà verificata la presenza di modifiche durante l'aggiornamento. Le schede Elimina visualizzano tutte le tabelle che sono state rimosse dallo schema e verranno rimosse anche dal modello come parte dell'aggiornamento. Le informazioni su queste due schede vengono rilevate automaticamente e vengono fornite solo a scopo informativo. non è possibile modificare le impostazioni.*</span><span class="sxs-lookup"><span data-stu-id="8a93e-189">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![Aggiornamento guidato](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="8a93e-191">Fare clic su fine nell'aggiornamento guidato</span><span class="sxs-lookup"><span data-stu-id="8a93e-191">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="8a93e-192">Il modello è ora aggiornato per includere una nuova entità utente mappata alla tabella users aggiunta al database.</span><span class="sxs-lookup"><span data-stu-id="8a93e-192">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![Modello aggiornato](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="8a93e-194">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="8a93e-194">Summary</span></span>

<span data-ttu-id="8a93e-195">In questa procedura dettagliata è stato esaminato Database First sviluppo, che ci ha consentito di creare un modello nella finestra di progettazione EF basata su un database esistente.</span><span class="sxs-lookup"><span data-stu-id="8a93e-195">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="8a93e-196">Il modello è stato quindi utilizzato per leggere e scrivere alcuni dati dal database.</span><span class="sxs-lookup"><span data-stu-id="8a93e-196">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="8a93e-197">Infine, il modello è stato aggiornato in modo da riflettere le modifiche apportate allo schema del database.</span><span class="sxs-lookup"><span data-stu-id="8a93e-197">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
