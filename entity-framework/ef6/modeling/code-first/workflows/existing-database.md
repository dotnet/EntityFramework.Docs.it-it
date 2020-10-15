---
title: Code First a un database esistente-EF6
description: Code First a un database esistente in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 2305236f68cfdc9f6e1582cca1163abf380b4075
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066421"
---
# <a name="code-first-to-an-existing-database"></a><span data-ttu-id="cb96f-103">Code First a un database esistente</span><span class="sxs-lookup"><span data-stu-id="cb96f-103">Code First to an Existing Database</span></span>
<span data-ttu-id="cb96f-104">Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo di Code First destinati a un database esistente.</span><span class="sxs-lookup"><span data-stu-id="cb96f-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting an existing database.</span></span> <span data-ttu-id="cb96f-105">Code First consente di definire il modello utilizzando \# le classi C o VB.NET.</span><span class="sxs-lookup"><span data-stu-id="cb96f-105">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="cb96f-106">Facoltativamente, è possibile eseguire una configurazione aggiuntiva usando gli attributi delle classi e delle proprietà oppure usando un'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="cb96f-106">Optionally additional configuration can be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="cb96f-107">Video</span><span class="sxs-lookup"><span data-stu-id="cb96f-107">Watch the video</span></span>
<span data-ttu-id="cb96f-108">Questo video è [ora disponibile su Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span><span class="sxs-lookup"><span data-stu-id="cb96f-108">This video is [now available on Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="cb96f-109">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="cb96f-109">Pre-Requisites</span></span>

<span data-ttu-id="cb96f-110">Per completare questa procedura dettagliata, è necessario che **Visual Studio 2012** o **Visual Studio 2013** sia installato.</span><span class="sxs-lookup"><span data-stu-id="cb96f-110">You will need to have **Visual Studio 2012** or **Visual Studio 2013** installed to complete this walkthrough.</span></span>

<span data-ttu-id="cb96f-111">Sarà inoltre necessario installare la versione **6,1** (o successiva) del **Entity Framework Tools per Visual Studio** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-111">You will also need version **6.1** (or later) of the **Entity Framework Tools for Visual Studio** installed.</span></span> <span data-ttu-id="cb96f-112">Per informazioni sull'installazione della versione più recente del Entity Framework Tools, vedere [Get Entity Framework](xref:ef6/fundamentals/install) .</span><span class="sxs-lookup"><span data-stu-id="cb96f-112">See [Get Entity Framework](xref:ef6/fundamentals/install) for information on installing the latest version of the Entity Framework Tools.</span></span>

## <a name="1-create-an-existing-database"></a><span data-ttu-id="cb96f-113">1. creare un database esistente</span><span class="sxs-lookup"><span data-stu-id="cb96f-113">1. Create an Existing Database</span></span>

<span data-ttu-id="cb96f-114">In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.</span><span class="sxs-lookup"><span data-stu-id="cb96f-114">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="cb96f-115">Procediamo con la generazione del database.</span><span class="sxs-lookup"><span data-stu-id="cb96f-115">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="cb96f-116">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb96f-116">Open Visual Studio</span></span>
-   <span data-ttu-id="cb96f-117">**Visualizza- &gt; Esplora server**</span><span class="sxs-lookup"><span data-stu-id="cb96f-117">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="cb96f-118">Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**</span><span class="sxs-lookup"><span data-stu-id="cb96f-118">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="cb96f-119">Se non si è connessi a un database da **Esplora server** prima di selezionare **Microsoft SQL Server** come origine dati</span><span class="sxs-lookup"><span data-stu-id="cb96f-119">If you haven’t connected to a database from **Server Explorer** before you’ll need to select **Microsoft SQL Server** as the data source</span></span>

    ![Seleziona origine dati](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="cb96f-121">Connettersi all'istanza del database locale e immettere **Blog** come nome del database</span><span class="sxs-lookup"><span data-stu-id="cb96f-121">Connect to your LocalDB instance, and enter **Blogging** as the database name</span></span>

    ![Connessione al database locale](~/ef6/media/localdbconnection.png)

-   <span data-ttu-id="cb96f-123">Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-123">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Finestra di dialogo Crea database](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="cb96f-125">Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-125">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="cb96f-126">Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-126">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

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

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a><span data-ttu-id="cb96f-127">2. creare l'applicazione</span><span class="sxs-lookup"><span data-stu-id="cb96f-127">2. Create the Application</span></span>

<span data-ttu-id="cb96f-128">Per semplificare le operazioni, verrà creata un'applicazione console di base che usa Code First per eseguire l'accesso ai dati:</span><span class="sxs-lookup"><span data-stu-id="cb96f-128">To keep things simple we will build a basic console application that uses Code First to do the data access:</span></span>

-   <span data-ttu-id="cb96f-129">Aprire Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb96f-129">Open Visual Studio</span></span>
-   <span data-ttu-id="cb96f-130">**File- &gt; nuovo- &gt; progetto...**</span><span class="sxs-lookup"><span data-stu-id="cb96f-130">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="cb96f-131">Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**</span><span class="sxs-lookup"><span data-stu-id="cb96f-131">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="cb96f-132">Immettere **CodeFirstExistingDatabaseSample** come nome</span><span class="sxs-lookup"><span data-stu-id="cb96f-132">Enter **CodeFirstExistingDatabaseSample** as the name</span></span>
-   <span data-ttu-id="cb96f-133">Selezionare **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb96f-133">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="cb96f-134">3. Reverse Engineering Model</span><span class="sxs-lookup"><span data-stu-id="cb96f-134">3. Reverse Engineer Model</span></span>

<span data-ttu-id="cb96f-135">Si utilizzerà il Entity Framework Tools per Visual Studio per consentire la generazione di codice iniziale per eseguire il mapping al database.</span><span class="sxs-lookup"><span data-stu-id="cb96f-135">We will use the Entity Framework Tools for Visual Studio to help us generate some initial code to map to the database.</span></span> <span data-ttu-id="cb96f-136">Questi strumenti generano solo codice che può essere digitato manualmente, se si preferisce.</span><span class="sxs-lookup"><span data-stu-id="cb96f-136">These tools are just generating code that you could also type by hand if you prefer.</span></span>

-   <span data-ttu-id="cb96f-137">**Progetto- &gt; Aggiungi nuovo elemento...**</span><span class="sxs-lookup"><span data-stu-id="cb96f-137">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="cb96f-138">Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="cb96f-138">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="cb96f-139">Immettere **BloggingContext** come nome e fare clic su **OK** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-139">Enter **BloggingContext** as the name and click **OK**</span></span>
-   <span data-ttu-id="cb96f-140">Viene avviata la **procedura guidata Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="cb96f-140">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="cb96f-141">Selezionare **Code First da database** e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-141">Select **Code First from Database** and click **Next**</span></span>

    ![Procedura guidata One CFE](~/ef6/media/wizardonecfe.png)

-   <span data-ttu-id="cb96f-143">Selezionare la connessione al database creato nella prima sezione e fare clic su **Avanti** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-143">Select the connection to the database you created in the first section and click **Next**</span></span>

    ![Procedura guidata due](~/ef6/media/wizardtwocfe.png)

-   <span data-ttu-id="cb96f-145">Fare clic sulla casella di controllo accanto a **tabelle** per importare tutte le tabelle e fare clic su **fine** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-145">Click the checkbox next to **Tables** to import all tables and click **Finish**</span></span>

    ![Procedura guidata tre CFE](~/ef6/media/wizardthreecfe.png)

<span data-ttu-id="cb96f-147">Una volta completato il processo di Reverse Engineering, al progetto verrà aggiunto un certo numero di elementi. si osserverà ora cosa è stato aggiunto.</span><span class="sxs-lookup"><span data-stu-id="cb96f-147">Once the reverse engineer process completes a number of items will have been added to the project, let's take a look at what's been added.</span></span>

### <a name="configuration-file"></a><span data-ttu-id="cb96f-148">File di configurazione</span><span class="sxs-lookup"><span data-stu-id="cb96f-148">Configuration file</span></span>

<span data-ttu-id="cb96f-149">Al progetto è stato aggiunto un file di App.config. il file contiene la stringa di connessione al database esistente.</span><span class="sxs-lookup"><span data-stu-id="cb96f-149">An App.config file has been added to the project, this file contains the connection string to the existing database.</span></span>

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

<span data-ttu-id="cb96f-150">*Si noteranno anche altre impostazioni nel file di configurazione. si tratta di impostazioni EF predefinite che indicano a Code First dove creare i database. Poiché viene mappato a un database esistente, queste impostazioni verranno ignorate nell'applicazione.*</span><span class="sxs-lookup"><span data-stu-id="cb96f-150">*You’ll notice some other settings in the configuration file too, these are default EF settings that tell Code First where to create databases. Since we are mapping to an existing database these setting will be ignored in our application.*</span></span>

### <a name="derived-context"></a><span data-ttu-id="cb96f-151">Contesto derivato</span><span class="sxs-lookup"><span data-stu-id="cb96f-151">Derived Context</span></span>

<span data-ttu-id="cb96f-152">È stata aggiunta una classe **BloggingContext** al progetto.</span><span class="sxs-lookup"><span data-stu-id="cb96f-152">A **BloggingContext** class has been added to the project.</span></span> <span data-ttu-id="cb96f-153">Il contesto rappresenta una sessione con il database, consentendo di eseguire query e salvare i dati.</span><span class="sxs-lookup"><span data-stu-id="cb96f-153">The context represents a session with the database, allowing us to query and save data.</span></span>
<span data-ttu-id="cb96f-154">Il contesto espone una \*\*DbSet &lt; TEntity &gt; \*\* per ogni tipo nel modello.</span><span class="sxs-lookup"><span data-stu-id="cb96f-154">The context exposes a **DbSet&lt;TEntity&gt;** for each type in our model.</span></span> <span data-ttu-id="cb96f-155">Si noterà anche che il costruttore predefinito chiama un costruttore di base usando la sintassi **Name =** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-155">You’ll also notice that the default constructor calls a base constructor using the **name=** syntax.</span></span> <span data-ttu-id="cb96f-156">Questo indica Code First che la stringa di connessione da usare per questo contesto deve essere caricata dal file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="cb96f-156">This tells Code First that the connection string to use for this context should be loaded from the configuration file.</span></span>

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

<span data-ttu-id="cb96f-157">*Quando si utilizza una stringa di connessione nel file di configurazione, è necessario utilizzare sempre la sintassi **Name =** . Ciò garantisce che se la stringa di connessione non è presente, Entity Framework genererà un'eccezione anziché creare un nuovo database per convenzione.*</span><span class="sxs-lookup"><span data-stu-id="cb96f-157">*You should always use the **name=** syntax when you are using a connection string in the config file. This ensures that if the connection string is not present then Entity Framework will throw rather than creating a new database by convention.*</span></span>

### <a name="model-classes"></a><span data-ttu-id="cb96f-158">Classi modello</span><span class="sxs-lookup"><span data-stu-id="cb96f-158">Model classes</span></span>

<span data-ttu-id="cb96f-159">Infine, al progetto sono state aggiunte anche una classe **post** e **Blog** .</span><span class="sxs-lookup"><span data-stu-id="cb96f-159">Finally, a **Blog** and **Post** class have also been added to the project.</span></span> <span data-ttu-id="cb96f-160">Queste sono le classi di dominio che costituiscono il modello.</span><span class="sxs-lookup"><span data-stu-id="cb96f-160">These are the domain classes that make up our model.</span></span> <span data-ttu-id="cb96f-161">Verranno visualizzate le annotazioni dei dati applicate alle classi per specificare la configurazione in cui le convenzioni Code First non si allineano alla struttura del database esistente.</span><span class="sxs-lookup"><span data-stu-id="cb96f-161">You'll see Data Annotations applied to the classes to specify configuration where the Code First conventions would not align with the structure of the existing database.</span></span> <span data-ttu-id="cb96f-162">Si noterà, ad esempio, l'annotazione **StringLength** in **Blog.Name** e **Blog. URL** poiché la lunghezza massima è **200** nel database (il Code First impostazione predefinita consiste nell'utilizzare la lunghezza massima supportata dal provider di database- **nvarchar (max)** in SQL Server).</span><span class="sxs-lookup"><span data-stu-id="cb96f-162">For example, you'll see the **StringLength** annotation on **Blog.Name** and **Blog.Url** since they have a maximum length of **200** in the database (the Code First default is to use the maximun length supported by the database provider - **nvarchar(max)** in SQL Server).</span></span>

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a><span data-ttu-id="cb96f-163">4. lettura & scrittura di dati</span><span class="sxs-lookup"><span data-stu-id="cb96f-163">4. Reading & Writing Data</span></span>

<span data-ttu-id="cb96f-164">Ora che è disponibile un modello, è possibile usarlo per accedere ai dati.</span><span class="sxs-lookup"><span data-stu-id="cb96f-164">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="cb96f-165">Implementare il metodo **Main** in **Program.cs** , come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="cb96f-165">Implement the **Main** method in **Program.cs** as shown below.</span></span> <span data-ttu-id="cb96f-166">Questo codice crea una nuova istanza del contesto e la usa per inserire un nuovo **Blog**.</span><span class="sxs-lookup"><span data-stu-id="cb96f-166">This code creates a new instance of our context and then uses it to insert a new **Blog**.</span></span> <span data-ttu-id="cb96f-167">USA quindi una query LINQ per recuperare tutti i **Blog** dal database ordinati alfabeticamente in base al **titolo**.</span><span class="sxs-lookup"><span data-stu-id="cb96f-167">Then it uses a LINQ query to retrieve all **Blogs** from the database ordered alphabetically by **Title**.</span></span>

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

<span data-ttu-id="cb96f-168">È ora possibile eseguire l'applicazione e testarla.</span><span class="sxs-lookup"><span data-stu-id="cb96f-168">You can now run the application and test it.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a><span data-ttu-id="cb96f-169">Che cosa succede se il database cambia?</span><span class="sxs-lookup"><span data-stu-id="cb96f-169">What if My Database Changes?</span></span>

<span data-ttu-id="cb96f-170">La procedura guidata Code First per database è progettata per generare un set di punti di partenza per le classi che è possibile modificare e modificare.</span><span class="sxs-lookup"><span data-stu-id="cb96f-170">The Code First to Database wizard is designed to generate a starting point set of classes that you can then tweak and modify.</span></span> <span data-ttu-id="cb96f-171">Se lo schema del database viene modificato, è possibile modificare manualmente le classi o eseguire un altro decodificatore per sovrascrivere le classi.</span><span class="sxs-lookup"><span data-stu-id="cb96f-171">If your database schema changes you can either manually edit the classes or perform another reverse engineer to overwrite the classes.</span></span>

## <a name="using-code-first-migrations-to-an-existing-database"></a><span data-ttu-id="cb96f-172">Utilizzo di Migrazioni Code First a un database esistente</span><span class="sxs-lookup"><span data-stu-id="cb96f-172">Using Code First Migrations to an Existing Database</span></span>

<span data-ttu-id="cb96f-173">Se si desidera utilizzare Migrazioni Code First con un database esistente, vedere [migrazioni Code First a un database esistente](xref:ef6/modeling/code-first/migrations/existing-database).</span><span class="sxs-lookup"><span data-stu-id="cb96f-173">If you want to use Code First Migrations with an existing database, see [Code First Migrations to an existing database](xref:ef6/modeling/code-first/migrations/existing-database).</span></span>

## <a name="summary"></a><span data-ttu-id="cb96f-174">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="cb96f-174">Summary</span></span>

<span data-ttu-id="cb96f-175">In questa procedura dettagliata è stato esaminato Code First sviluppo usando un database esistente.</span><span class="sxs-lookup"><span data-stu-id="cb96f-175">In this walkthrough we looked at Code First development using an existing database.</span></span> <span data-ttu-id="cb96f-176">È stato usato il Entity Framework Tools per Visual Studio per decompilare un set di classi di cui è stato eseguito il mapping al database e può essere usato per archiviare e recuperare i dati.</span><span class="sxs-lookup"><span data-stu-id="cb96f-176">We used the Entity Framework Tools for Visual Studio to reverse engineer a set of classes that mapped to the database and could be used to store and retrieve data.</span></span>
