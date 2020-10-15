---
title: Procedura dettagliata per entità Self-Tracking-EF6
description: Procedura dettagliata per entità con rilevamento automatico per Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 7c50bbc9fad10a474728b03e79b685c549cf675d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065602"
---
# <a name="self-tracking-entities-walkthrough"></a>Procedura dettagliata per entità Self-Tracking
> [!IMPORTANT]
> L'uso del modello di entità con rilevamento automatico non è più consigliabile. Continuerà a essere disponibile solo per supportare le applicazioni esistenti. Se l'applicazione richiede l'uso con grafici di entità disconnesse, prendere in considerazione altre alternative, come ad esempio [Trackable Entities](https://trackableentities.github.io/), che è una tecnologia simile alle entità con rilevamento automatico ma viene sviluppata in modo più attivo dalla community, oppure la scrittura di codice personalizzato usando le API di rilevamento delle modifiche di basso livello.

In questa procedura dettagliata viene illustrato lo scenario in cui un servizio Windows Communication Foundation (WCF) espone un'operazione che restituisce un grafico di entità. Successivamente, un'applicazione client modifica il grafico e invia le modifiche a un'operazione del servizio che convalida e salva gli aggiornamenti in un database utilizzando Entity Framework.

Prima di completare questa procedura dettagliata, assicurarsi di leggere la pagina [entità con rilevamento automatico](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) .

In questa procedura dettagliata verranno completate le seguenti azioni:

-   Crea un database a cui accedere.
-   Crea una libreria di classi che contiene il modello.
-   Scambia il modello generatore di entità Self-Tracking.
-   Sposta le classi di entità in un progetto separato.
-   Crea un servizio WCF che espone operazioni per eseguire query e salvare entità.
-   Crea applicazioni client (console e WPF) che utilizzano il servizio.

In questa procedura dettagliata verranno usati Database First, ma le stesse tecniche si applicano ugualmente ai Model First.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, sarà necessaria una versione recente di Visual Studio.

## <a name="create-a-database"></a>Creare un database

Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:

-   Se si usa Visual Studio 2012, si creerà un database del database locale.
-   Se si usa Visual Studio 2010 verrà creato un database di SQL Express.

Procediamo con la generazione del database.

-   Aprire Visual Studio
-   **Visualizza- &gt; Esplora server**
-   Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**
-   Se non si è connessi a un database da Esplora server prima di selezionare **Microsoft SQL Server** come origine dati
-   Connettersi a un database locale o a SQL Express, a seconda di quale installato
-   Immettere **STESample** come nome del database
-   Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .
-   Il nuovo database verrà ora visualizzato in Esplora server
-   Se si usa Visual Studio 2012
    -   Fare clic con il pulsante destro del mouse sul database in Esplora server e scegliere **Nuova query**
    -   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .
-   Se si usa Visual Studio 2010
    -   Selezione **dati- &gt; Editor Transact-SQL- &gt; nuova connessione query...**
    -   Immettere **. \\ SQLEXPRESS** come nome del server e fare clic su **OK**
    -   Selezionare il database **STESample** dall'elenco a discesa nella parte superiore dell'editor di query
    -   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui SQL**

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a>Creare il modello

Per prima cosa, abbiamo bisogno di un progetto in cui inserire il modello.

-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Visual C \# ** dal riquadro a sinistra e quindi **libreria di classi**
-   Immettere **STESample** come nome e fare clic su **OK** .

Verrà ora creato un modello semplice in EF designer per accedere al database:

-   **Progetto- &gt; Aggiungi nuovo elemento...**
-   Selezionare i **dati** nel riquadro a sinistra e quindi **ADO.NET Entity Data Model**
-   Immettere **BloggingModel** come nome e fare clic su **OK** .
-   Selezionare **genera da database** e fare clic su **Avanti** .
-   Immettere le informazioni di connessione per il database creato nella sezione precedente
-   Immettere **BloggingContext** come nome della stringa di connessione e fare clic su **Next (avanti** ).
-   Selezionare la casella accanto a **tabelle** e fare clic su **fine** .

## <a name="swap-to-ste-code-generation"></a>Scambia alla generazione del codice STE

A questo punto è necessario disabilitare la generazione di codice predefinita e scambiare le entità Self-Tracking.

### <a name="if-you-are-using-visual-studio-2012"></a>Se si usa Visual Studio 2012

-   Espandere **BloggingModel. edmx** in **Esplora soluzioni** ed eliminare **BloggingModel.TT** e **BloggingModel.Context.TT**. 
     *verrà disabilitata la generazione di codice predefinita* .
-   Fare clic con il pulsante destro del mouse su un'area vuota nell'area di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**
-   Selezionare **online** dal riquadro a sinistra e cercare il **Generatore di ste**
-   Selezionare il modello **Ste Generator for \# C** , immettere **STETemplate** come nome e fare clic su **Aggiungi** .
-   I file **STETemplate.TT** e **STETemplate.Context.TT** vengono aggiunti annidati nel file BloggingModel. edmx

### <a name="if-you-are-using-visual-studio-2010"></a>Se si usa Visual Studio 2010

-   Fare clic con il pulsante destro del mouse su un'area vuota nell'area di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**
-   Selezionare il **codice** nel riquadro a sinistra e quindi **ADO.NET Self-Tracking generatore di entità**
-   Immettere **STETemplate** come nome e fare clic su **Aggiungi**
-   I file **STETemplate.TT** e **STETemplate.Context.TT** vengono aggiunti direttamente al progetto

## <a name="move-entity-types-into-separate-project"></a>Spostare i tipi di entità in un progetto separato

Per usare Self-Tracking entità, l'applicazione client deve accedere alle classi di entità generate dal modello. Poiché non si vuole esporre l'intero modello all'applicazione client, le classi di entità verranno spostate in un progetto distinto.

Il primo passaggio consiste nell'arrestare la generazione delle classi di entità nel progetto esistente:

-   Fare clic con il pulsante destro del mouse su **STETemplate.TT** in **Esplora soluzioni** e scegliere **proprietà** .
-   Nella finestra **Proprietà** deselezionare **TextTemplatingFileGenerator** dalla proprietà **CustomTool**
-   Espandere **STETemplate.TT** in **Esplora soluzioni** ed eliminare tutti i file annidati sotto di esso

Successivamente, verrà aggiunto un nuovo progetto e verranno generate le classi di entità

-   **File- &gt; Aggiungi- &gt; progetto...**
-   Selezionare **Visual C \# ** dal riquadro a sinistra e quindi **libreria di classi**
-   Immettere **STESample. Entities** come nome e fare clic su **OK** .
-   **Progetto- &gt; Aggiungi elemento esistente...**
-   Passare alla cartella del progetto **STESample**
-   Selezionare per visualizzare **tutti i file ( \* . \* )**
-   Selezionare il file **STETemplate.TT**
-   Fare clic sulla freccia a discesa accanto al pulsante **Aggiungi** e selezionare **Aggiungi come collegamento** .

    ![Aggiungi modello collegato](~/ef6/media/addlinkedtemplate.png)

Si verifica inoltre che le classi di entità vengano generate nello stesso spazio dei nomi del contesto. In questo modo si riduce il numero di istruzioni using che è necessario aggiungere all'interno dell'applicazione.

-   Fare clic con il pulsante destro del mouse sul **STETemplate.TT** collegato in **Esplora soluzioni** e selezionare **proprietà** .
-   Nella finestra **Proprietà** impostare lo **spazio dei nomi dello strumento personalizzato** su **STESample**

Per la compilazione del codice generato dal modello STE è necessario un riferimento a **System. Runtime. Serialization** . Questa libreria è necessaria per gli attributi **DataContract** e **DataMember** WCF utilizzati sui tipi di entità serializzabili.

-   Fare clic con il pulsante destro del mouse sul progetto **STESample. Entities** in **Esplora soluzioni** e scegliere **Aggiungi riferimento.**
    -   In Visual Studio 2012-selezionare la casella accanto a **System. Runtime. Serialization** e fare clic su **OK** .
    -   In Visual Studio 2010 selezionare **System. Runtime. Serialization** e fare clic su **OK** .

Infine, il progetto con il contesto in esso contenuto sarà necessario un riferimento ai tipi di entità.

-   Fare clic con il pulsante destro del mouse sul progetto **STESample** in **Esplora soluzioni** e scegliere **Aggiungi riferimento.**
    -   In Visual Studio 2012 selezionare **soluzione** nel riquadro a sinistra, selezionare la casella accanto a **STESample. Entities** e fare clic su **OK** .
    -   In Visual Studio 2010 selezionare la scheda **progetti** , selezionare **STESample. Entities** , quindi fare clic su **OK** .

>[!NOTE]
> Un'altra opzione per spostare i tipi di entità in un progetto separato consiste nello spostare il file modello, anziché collegarlo dal percorso predefinito. In tal caso, sarà necessario aggiornare la variabile **inputfile** nel modello per fornire il percorso relativo del file edmx, in questo esempio **.. \\ BloggingModel. edmx**).

## <a name="create-a-wcf-service"></a>Creazione di un servizio WCF

A questo punto è possibile aggiungere un servizio WCF per esporre i dati. si inizierà creando il progetto.

-   **File- &gt; Aggiungi- &gt; progetto...**
-   Selezionare **Visual C \# ** dal riquadro a sinistra e quindi **applicazione di servizio WCF**
-   Immettere **STESample. Service** come nome e fare clic su **OK** .
-   Aggiungere un riferimento all'assembly **System. Data. Entity**
-   Aggiungere un riferimento ai progetti **STESample** e **STESample. Entities**

È necessario copiare la stringa di connessione EF in questo progetto in modo che venga individuata in fase di esecuzione.

-   Aprire il file di **App.Config** per il progetto **STESample **e copiare l'elemento **connectionStrings**
-   Incollare l'elemento **connectionStrings** come elemento figlio dell'elemento **Configuration** del file di **Web.Config** nel progetto **STESample. Service**

A questo punto è possibile implementare il servizio effettivo.

-   Aprire **IService1.cs** e sostituire il contenuto con il codice seguente

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   Aprire **Service1. svc** e sostituire il contenuto con il codice seguente

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a>Utilizzare il servizio da un'applicazione console

Viene ora creata un'applicazione console che usa il servizio.

-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Visual C \# ** dal riquadro a sinistra e quindi **applicazione console**
-   Immettere **STESample. ConsoleTest** come nome e fare clic su **OK** .
-   Aggiungere un riferimento al progetto **STESample. Entities**

È necessario un riferimento al servizio WCF

-   Fare clic con il pulsante destro del mouse sul progetto **STESample. ConsoleTest** in **Esplora soluzioni** e selezionare **Aggiungi riferimento al servizio...**
-   Fare clic su **individua**
-   Immettere **BloggingService** come spazio dei nomi e fare clic su **OK** .

A questo punto è possibile scrivere codice per utilizzare il servizio.

-   Aprire **Program.cs** e sostituire il contenuto con il codice seguente.

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

È ora possibile eseguire l'applicazione per vederla in azione.

-   Fare clic con il pulsante destro del mouse sul progetto **STESample. ConsoleTest** in **Esplora soluzioni** e scegliere **debug- &gt; Avvia nuova istanza**

Quando l'applicazione viene eseguita, verrà visualizzato il seguente output.

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a>Utilizzare il servizio da un'applicazione WPF

Viene ora creata un'applicazione WPF che usa il servizio.

-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Visual C \# ** dal riquadro a sinistra e quindi **applicazione WPF**
-   Immettere **STESample. WPFTest** come nome e fare clic su **OK** .
-   Aggiungere un riferimento al progetto **STESample. Entities**

È necessario un riferimento al servizio WCF

-   Fare clic con il pulsante destro del mouse sul progetto **STESample. WPFTest** in **Esplora soluzioni** e selezionare **Aggiungi riferimento al servizio...**
-   Fare clic su **individua**
-   Immettere **BloggingService** come spazio dei nomi e fare clic su **OK** .

A questo punto è possibile scrivere codice per utilizzare il servizio.

-   Aprire **MainWindow. XAML** e sostituire il contenuto con il codice seguente.

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   Aprire il code-behind per MainWindow (**MainWindow.XAML.cs**) e sostituire il contenuto con il codice seguente.

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

È ora possibile eseguire l'applicazione per vederla in azione.

-   Fare clic con il pulsante destro del mouse sul progetto **STESample. WPFTest** in **Esplora soluzioni** e scegliere **debug- &gt; Avvia nuova istanza**
-   È possibile modificare i dati usando la schermata e salvarli tramite il servizio usando il pulsante **Salva**

![Finestra principale WPF](~/ef6/media/wpf.png)
