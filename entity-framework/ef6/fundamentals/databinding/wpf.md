---
title: Associazione dati con WPF-EF6
description: Associazione dati con WPF in Entity Framework 6
author: ajcvickers
ms.date: 05/19/2020
uid: ef6/fundamentals/databinding/wpf
ms.openlocfilehash: b6dc484d09340671dfcd841e34234d55e9808af2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065654"
---
# <a name="databinding-with-wpf"></a>Data binding con WPF

> [!IMPORTANT]
> **Questo documento è valido per WPF solo sul .NET Framework**
>
> Questo documento descrive l'associazione dati per WPF nel .NET Framework. Per i nuovi progetti .NET Core, è consigliabile usare [EF Core](xref:core/index) anziché Entity Framework 6. La documentazione per l'associazione dati in EF Core è la seguente: [Introduzione con WPF](xref:core/get-started/wpf).

Questa procedura dettagliata illustra come associare tipi POCO ai controlli WPF in un modulo "Master-Detail". L'applicazione usa le API Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.

Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: **categoria** ( \\ master principale) e **prodotto** ( \\ Dettagli dipendenti). Gli strumenti di Visual Studio vengono quindi utilizzati per associare i tipi definiti nel modello ai controlli WPF. Il Framework di associazione dei dati WPF consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.

Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2013 ma è possibile completare questa procedura dettagliata con Visual Studio 2012 o Visual Studio 2010.

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a>Utilizzare l'opzione ' Object ' per la creazione di origini dati WPF

Con la versione precedente di Entity Framework si consiglia di usare l'opzione di **database** quando si crea una nuova origine dati basata su un modello creato con la finestra di progettazione EF. Questo è dovuto al fatto che la finestra di progettazione genera un contesto che deriva da ObjectContext e da classi di entità derivate da EntityObject. L'uso dell'opzione di database consente di scrivere il codice migliore per interagire con questa superficie dell'API.

I progettisti EF per Visual Studio 2012 e Visual Studio 2013 generano un contesto che deriva da DbContext insieme a classi di entità POCO semplici. Con Visual Studio 2010 è consigliabile eseguire lo scambio in un modello di generazione del codice che usa DbContext, come descritto più avanti in questa procedura dettagliata.

Quando si usa la superficie dell'API DbContext, è consigliabile usare l'opzione **oggetto** durante la creazione di una nuova origine dati, come illustrato in questa procedura dettagliata.

Se necessario, è possibile [ripristinare la generazione di codice basata su ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext) per i modelli creati con la finestra di progettazione EF.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010.

Se si usa Visual Studio 2010, è anche necessario installare NuGet. Per altre informazioni, vedere [installazione di NuGet](/nuget/install-nuget-client-tools).  

## <a name="create-the-application"></a>Creare l'applicazione

- Aprire Visual Studio
- **File- &gt; nuovo- &gt; progetto...**
- Selezionare **Windows**   nel riquadro a sinistra e **WPFApplication** nel riquadro a destra.
- Immettere **WPFwithEFSample**   come nome
- Seleziona **OK**

## <a name="install-the-entity-framework-nuget-package"></a>Installare il pacchetto NuGet Entity Framework

- In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WinFormswithEFSample**
- Selezionare **Gestisci pacchetti NuGet...**
- Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework**
- Fai clic su **Install** (Installa).  
    >[!NOTE]
    > Oltre all'assembly EntityFramework, viene aggiunto anche un riferimento a System. ComponentModel. DataAnnotations. Se il progetto contiene un riferimento a System. Data. Entity, verrà rimosso al momento dell'installazione del pacchetto EntityFramework. L'assembly System. Data. Entity non viene più usato per le applicazioni Entity Framework 6.

## <a name="define-a-model"></a>Definire un modello

In questa procedura dettagliata è possibile scegliere di implementare un modello usando Code First o la finestra di progettazione EF. Completare una delle due sezioni riportate di seguito.

### <a name="option-1-define-a-model-using-code-first"></a>Opzione 1: definire un modello utilizzando Code First

In questa sezione viene illustrato come creare un modello e il relativo database associato utilizzando Code First. Passare alla sezione successiva (**opzione 2: definire un modello usando database First)** se si preferisce usare database First per decompilare il modello da un database usando EF designer

Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio).

- Aggiungere una nuova classe a **WPFwithEFSample:**
  - Fare clic con il pulsante destro del mouse sul nome del progetto
  - Selezionare **Aggiungi**, quindi **nuovo elemento**
  - Selezionare la **classe** e immettere **Product**   per nome classe
- Sostituire la **Product**   definizione della classe di prodotto con il codice seguente:

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

La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione. In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.

Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; . Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.

Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.

- Aggiungere una nuova classe **ProductContext** al progetto con la definizione seguente:

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

Compilare il progetto.

### <a name="option-2-define-a-model-using-database-first"></a>Opzione 2: definire un modello utilizzando Database First

Questa sezione illustra come usare Database First per decompilare il modello da un database usando la finestra di progettazione EF. Se è stata completata la sezione precedente (**opzione 1: definire un modello con Code First)**, ignorare questa sezione e passare direttamente alla sezione **caricamento lazy** .

#### <a name="create-an-existing-database"></a>Creare un database esistente

In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.

Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:

- Se si usa Visual Studio 2010 verrà creato un database di SQL Express.
- Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202.aspx) .

Procediamo con la generazione del database.

- **Visualizza- &gt; Esplora server**
- Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**
- Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati

    ![Modifica origine dati](~/ef6/media/changedatasource.png)

- Connettersi al database locale o a SQL Express, a seconda di quale installato e immettere i **prodotti** come nome del database

    ![Aggiungere la connessione al database locale](~/ef6/media/addconnectionlocaldb.png)

    ![Aggiungi connessione rapida](~/ef6/media/addconnectionexpress.png)

- Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .

    ![Create Database](~/ef6/media/createdatabase.png)

- Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .
- Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .

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

#### <a name="reverse-engineer-model"></a>Decodificare il modello

Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.

- **Progetto- &gt; Aggiungi nuovo elemento...**
- Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**
- Immettere **ProductModel** come nome e fare clic su **OK** .
- Viene avviata la **procedura guidata Entity Data Model**
- Selezionare **genera da database** e fare clic su **Avanti** .

    ![Scelta dei contenuti del modello](~/ef6/media/choosemodelcontents.png)

- Selezionare la connessione al database creato nella prima sezione, immettere **ProductContext** come nome della stringa di connessione e fare clic su **Avanti** .

    ![Scegliere la connessione](~/ef6/media/chooseyourconnection.png)

- Fare clic sulla casella di controllo accanto a' Tables ' per importare tutte le tabelle e fare clic su' Finish '

    ![Scegliere gli oggetti](~/ef6/media/chooseyourobjects.png)

Una volta completato il processo di Reverse Engineering, il nuovo modello viene aggiunto al progetto e aperto per la visualizzazione nel Entity Framework Designer. Al progetto è stato aggiunto anche un file di App.config con i dettagli della connessione per il database.

#### <a name="additional-steps-in-visual-studio-2010"></a>Passaggi aggiuntivi in Visual Studio 2010

Se si lavora in Visual Studio 2010, sarà necessario aggiornare la finestra di progettazione di Entity Framework per l'uso della generazione di codice EF6.

- Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**
- Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**
- Selezionare il **Generatore EF 6. x DbContext per C \# ,** immettere **ProductsModel** come nome e fare clic su Aggiungi.

#### <a name="updating-code-generation-for-data-binding"></a>Aggiornamento della generazione di codice per data binding

EF genera codice dal modello usando i modelli T4. I modelli forniti con Visual Studio o scaricati da Visual Studio Gallery sono destinati all'uso generico. Questo significa che le entità generate da questi modelli hanno &lt; Proprietà ICollection T semplici &gt; . Tuttavia, quando si esegue data binding utilizzando WPF, è consigliabile utilizzare **ObservableCollection** per le proprietà della raccolta in modo che WPF possa tenere traccia delle modifiche apportate alle raccolte. A questo scopo, è possibile modificare i modelli per usare ObservableCollection.

- Aprire il **Esplora soluzioni** e trovare il file **ProductModel. edmx**
- Trovare il file **ProductModel.TT** che verrà annidato nel file ProductModel. edmx

    ![Modello di modello di prodotto WPF](~/ef6/media/wpfproductmodeltemplate.png)

- Fare doppio clic sul file ProductModel.tt per aprirlo nell'editor di Visual Studio
- Trovare e sostituire le due occorrenze di "**ICollection**" con "**ObservableCollection**". Si trovano approssimativamente a linee 296 e 484.
- Trovare e sostituire la prima occorrenza di "**HashSet**" con "**ObservableCollection**". Questa occorrenza si trova approssimativamente alla riga 50. **Non sostituire la** seconda occorrenza di HashSet rilevata successivamente nel codice.
- Trovare e sostituire l'unica occorrenza di "**System. Collections. Generic**" con "**System. Collections. ObjectModel**". Si trova approssimativamente alla riga 424.
- Salvare il file ProductModel.tt. Questa operazione dovrebbe causare la rigenerazione del codice per le entità. Se il codice non viene rigenerato automaticamente, fare clic con il pulsante destro del mouse su ProductModel.tt e scegliere "Esegui strumento personalizzato".

Se ora si apre il file Category.cs (annidato in ProductModel.tt), si noterà che la raccolta Products ha il tipo di ** &lt; &gt; prodotto ObservableCollection**.

Compilare il progetto.

## <a name="lazy-loading"></a>Caricamento lazy

La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione. In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.

EF offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione. Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.

Quando si usano i tipi di entità POCO, EF raggiunge il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento. Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) ed è necessario che la classe non sia **sealed** (**NotOverridable** in Visual Basic). Quando si usano Database First proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy. Nella sezione Code First si è scelto di rendere virtuali le proprietà di navigazione per lo stesso motivo

## <a name="bind-object-to-controls"></a>Associa oggetto a controlli

Aggiungere le classi definite nel modello come origini dati per l'applicazione WPF.

- Fare doppio clic su **MainWindow. XAML** in Esplora soluzioni per aprire il modulo principale
- Dal menu principale selezionare **progetto- &gt; Aggiungi nuova origine dati...**
    in Visual Studio 2010 è necessario selezionare **dati- &gt; Aggiungi nuova origine dati...**
- Nella pagina scegliere un'origine dati Typewindow selezionare **oggetto** e fare clic su **Avanti** .
- Nella finestra di dialogo selezionare gli oggetti dati, espandere **WPFwithEFSample**   due volte e selezionare **Category**  
    *Non è necessario selezionare l'origine dati del **prodotto** perché verrà rilasciata tramite la proprietà del **prodotto**nell'origine dati **Category***  

    ![Selezione oggetti dati](~/ef6/media/selectdataobjects.png)

- Fare clic su **Finish** (Fine).
- La finestra Origini dati viene aperta accanto alla finestra MainWindow. XAML *se la finestra Origini dati non viene visualizzata, selezionare **Visualizza- &gt; altre &gt; origini dati Windows** *
- Premere l'icona Aggiungi, in modo che la finestra Origini dati non nasconda automaticamente. Se la finestra è già visibile, potrebbe essere necessario fare clic sul pulsante Aggiorna.

    ![Origini dati](~/ef6/media/datasources.png)

- Selezionare l'origine dati **Category** e trascinarla nel form.

Quando è stata trascinata questa origine, si è verificato quanto segue:

- La risorsa **categoryViewSource** e il controllo **categoryDataGrid** sono stati aggiunti a XAML
- La proprietà DataContext sull'elemento Grid padre è stata impostata su "{StaticResource **categoryViewSource** }".La risorsa **categoryViewSource** funge da origine dell'associazione per l' \\ elemento Grid padre esterno. Gli elementi della griglia interna ereditano quindi il valore DataContext dalla griglia padre (la proprietà ItemsSource di categoryDataGrid è impostata su "{binding}")

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

## <a name="adding-a-details-grid"></a>Aggiunta di una griglia dettagli

Ora che è disponibile una griglia per visualizzare le categorie, aggiungere una griglia dettagli per visualizzare i prodotti associati.

- Selezionare la proprietà **Products** dall'origine dati **Category** e trascinarla nel form.
  - La risorsa **categoryProductsViewSource** e la griglia **productDataGrid** vengono aggiunte a XAML
  - Il percorso di associazione per questa risorsa è impostato su prodotti
  - Il Framework di associazione dati WPF garantisce che vengano visualizzati solo i prodotti correlati alla categoria selezionata in **productDataGrid**
- Dalla casella degli strumenti trascinare il **pulsante del mouse** sul form. Impostare la proprietà **Name** su **buttonSave** e la proprietà **Content** su **Save**.

Il form dovrebbe essere simile al seguente:

![Form progettazione](~/ef6/media/designer.png)

## <a name="add-code-that-handles-data-interaction"></a>Aggiungere codice che gestisce l'interazione dei dati

È il momento di aggiungere alcuni gestori di eventi alla finestra principale.

- Nella finestra XAML fare clic sull'elemento ** &lt; Window** , che seleziona la finestra principale
- Nella finestra **Proprietà** scegliere **eventi** in alto a destra, quindi fare doppio clic sulla casella di testo a destra dell'etichetta **caricata** .

    ![Proprietà della finestra principale](~/ef6/media/mainwindowproperties.png)

- Aggiungere anche l'evento **Click** per il pulsante **Salva** facendo doppio clic sul pulsante Salva nella finestra di progettazione.

In questo modo viene riportato il codice sottostante per il modulo. verrà ora modificato il codice per usare ProductContext per eseguire l'accesso ai dati. Aggiornare il codice per MainWindow come illustrato di seguito.

Il codice dichiara un'istanza con esecuzione prolungata di **ProductContext**. L'oggetto **ProductContext** viene utilizzato per eseguire query e salvare i dati nel database. Il metodo **Dispose ()** sull'istanza **ProductContext** viene quindi chiamato dal metodo **OnClosing** sottoposto a override.I commenti del codice forniscono informazioni dettagliate sul funzionamento del codice.

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

## <a name="test-the-wpf-application"></a>Testare l'applicazione WPF

- Compilare l'applicazione ed eseguirla. Se è stato usato Code First, si noterà che viene creato un database **WPFwithEFSample. ProductContext** .
- Immettere un nome di categoria nella griglia superiore e i nomi di prodotto nella griglia inferiore non   *immettere elementi nelle colonne ID, perché la chiave primaria viene generata dal database*

    ![Finestra principale con nuove categorie e prodotti](~/ef6/media/screen1.png)

- Premere il pulsante **Salva** per salvare i dati nel database

Dopo la chiamata a **SaveChanges ()** di DbContext, gli ID vengono popolati con i valori generati dal database. Poiché è stato chiamato **Refresh ()** dopo **SaveChanges ()** , i controlli **DataGrid** vengono aggiornati anche con i nuovi valori.

![Finestra principale con ID popolati](~/ef6/media/screen2.png)

## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni sulle data binding alle raccolte utilizzando WPF, vedere [questo argomento](/dotnet/framework/wpf/data/data-binding-overview#binding-to-collections) nella documentazione di WPF.  
