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
# <a name="databinding-with-winforms"></a>Data binding con WinForms
Questa procedura dettagliata illustra come associare tipi POCO a controlli Windows Form (WinForms) in un modulo "Master-Detail". L'applicazione utilizza Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.

Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: categoria ( \\ master principale) e prodotto ( \\ Dettagli dipendenti). Gli strumenti di Visual Studio vengono quindi utilizzati per associare i tipi definiti nel modello ai controlli WinForms. Il Framework di associazione dati di WinForms consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.

Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2013 ma è possibile completare questa procedura dettagliata con Visual Studio 2012 o Visual Studio 2010.

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010.

Se si usa Visual Studio 2010, è anche necessario installare NuGet. Per altre informazioni, vedere [installazione di NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

## <a name="create-the-application"></a>Creare l'applicazione

-   Aprire Visual Studio.
-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Windows** nel riquadro sinistro e **Windows FormsApplication** nel riquadro a destra.
-   Immettere **WinFormswithEFSample** come nome
-   Selezionare **OK**.

## <a name="install-the-entity-framework-nuget-package"></a>Installare il pacchetto NuGet Entity Framework

-   In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **WinFormswithEFSample**
-   Selezionare **Gestisci pacchetti NuGet...**
-   Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework**
-   Fai clic su **Install** (Installa).  
    > [!NOTE]
    > Oltre all'assembly EntityFramework, viene aggiunto anche un riferimento a System. ComponentModel. DataAnnotations. Se il progetto contiene un riferimento a System. Data. Entity, verrà rimosso al momento dell'installazione del pacchetto EntityFramework. L'assembly System. Data. Entity non viene più usato per le applicazioni Entity Framework 6.

## <a name="implementing-ilistsource-for-collections"></a>Implementazione di IListSource per le raccolte

Le proprietà della raccolta devono implementare l'interfaccia IListSource per consentire l'data binding bidirezionale con l'ordinamento quando si usa Windows Forms. A tale scopo, si estenderà l'oggetto ObservableCollection per aggiungere la funzionalità IListSource.

-   Aggiungere una classe **ObservableListSource** al progetto:
    -   Fare clic con il pulsante destro del mouse sul nome del progetto
    -   Selezionare **Aggiungi- &gt; nuovo elemento**
    -   Selezionare **classe** e immettere **ObservableListSource** per nome classe
-   Sostituire il codice generato per impostazione predefinita con il codice seguente:

*Questa classe consente la data binding bidirezionale e l'ordinamento. La classe deriva da ObservableCollection &lt; T &gt; e aggiunge un'implementazione esplicita di IListSource. Il metodo GetList () di IListSource viene implementato per restituire un'implementazione di IBindingList che rimane sincronizzata con l'oggetto ObservableCollection. L'implementazione di IBindingList generata da tobindings supporta l'ordinamento. Il metodo di estensione tobinding è definito nell'assembly EntityFramework.*

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

## <a name="define-a-model"></a>Definire un modello

In questa procedura dettagliata è possibile scegliere di implementare un modello usando Code First o la finestra di progettazione EF. Completare una delle due sezioni riportate di seguito.

### <a name="option-1-define-a-model-using-code-first"></a>Opzione 1: definire un modello utilizzando Code First

In questa sezione viene illustrato come creare un modello e il relativo database associato utilizzando Code First. Passare alla sezione successiva (**opzione 2: definire un modello usando database First)** se si preferisce usare database First per decompilare il modello da un database usando EF designer

Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio).

-   Aggiungere una nuova classe **prodotto** al progetto
-   Sostituire il codice generato per impostazione predefinita con il codice seguente:

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

-   Aggiungere una classe **Category** al progetto.
-   Sostituire il codice generato per impostazione predefinita con il codice seguente:

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

Oltre alla definizione delle entità, è necessario definire una classe che deriva da **DbContext** ed espone le proprietà **di &lt; DbSet &gt; TEntity** . Le proprietà **DbSet** consentono al contesto di individuare i tipi che si desidera includere nel modello. I tipi **DbContext** e **DbSet** sono definiti nell'assembly EntityFramework.

Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.

-   Aggiungere una nuova classe **ProductContext** al progetto.
-   Sostituire il codice generato per impostazione predefinita con il codice seguente:

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

Compilare il progetto.

### <a name="option-2-define-a-model-using-database-first"></a>Opzione 2: definire un modello utilizzando Database First

Questa sezione illustra come usare Database First per decompilare il modello da un database usando la finestra di progettazione EF. Se è stata completata la sezione precedente (**opzione 1: definire un modello con Code First)**, ignorare questa sezione e passare direttamente alla sezione **caricamento lazy** .

#### <a name="create-an-existing-database"></a>Creare un database esistente

In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.

Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:

-   Se si usa Visual Studio 2010 verrà creato un database di SQL Express.
-   Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202.aspx) .

Procediamo con la generazione del database.

-   **Visualizza- &gt; Esplora server**
-   Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**
-   Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati

    ![Modifica origine dati](~/ef6/media/changedatasource.png)

-   Connettersi al database locale o a SQL Express, a seconda di quale installato e immettere i **prodotti** come nome del database

    ![Aggiungere la connessione al database locale](~/ef6/media/addconnectionlocaldb.png)

    ![Aggiungi connessione rapida](~/ef6/media/addconnectionexpress.png)

-   Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .

    ![Create Database](~/ef6/media/createdatabase.png)

-   Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .
-   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .

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

-   **Progetto- &gt; Aggiungi nuovo elemento...**
-   Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**
-   Immettere **ProductModel** come nome e fare clic su **OK** .
-   Viene avviata la **procedura guidata Entity Data Model**
-   Selezionare **genera da database** e fare clic su **Avanti** .

    ![Scelta dei contenuti del modello](~/ef6/media/choosemodelcontents.png)

-   Selezionare la connessione al database creato nella prima sezione, immettere **ProductContext** come nome della stringa di connessione e fare clic su **Avanti** .

    ![Scegliere la connessione](~/ef6/media/chooseyourconnection.png)

-   Fare clic sulla casella di controllo accanto a' Tables ' per importare tutte le tabelle e fare clic su' Finish '

    ![Scegliere gli oggetti](~/ef6/media/chooseyourobjects.png)

Una volta completato il processo di Reverse Engineering, il nuovo modello viene aggiunto al progetto e aperto per la visualizzazione nel Entity Framework Designer. Al progetto è stato aggiunto anche un file di App.config con i dettagli della connessione per il database.

#### <a name="additional-steps-in-visual-studio-2010"></a>Passaggi aggiuntivi in Visual Studio 2010

Se si lavora in Visual Studio 2010, sarà necessario aggiornare la finestra di progettazione di Entity Framework per l'uso della generazione di codice EF6.

-   Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**
-   Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**
-   Selezionare il **Generatore EF 6. x DbContext per C \# ,** immettere **ProductsModel** come nome e fare clic su Aggiungi.

#### <a name="updating-code-generation-for-data-binding"></a>Aggiornamento della generazione di codice per data binding

EF genera codice dal modello usando i modelli T4. I modelli forniti con Visual Studio o scaricati da Visual Studio Gallery sono destinati all'uso generico. Questo significa che le entità generate da questi modelli hanno &lt; Proprietà ICollection T semplici &gt; . Tuttavia, quando si esegue data binding è preferibile disporre di proprietà di raccolta che implementino IListSource. Questo è il motivo per cui è stata creata la classe ObservableListSource precedente e ora verranno modificati i modelli per usare questa classe.

-   Aprire il **Esplora soluzioni** e trovare il file **ProductModel. edmx**
-   Trovare il file **ProductModel.TT** che verrà annidato nel file ProductModel. edmx

    ![Modello del modello di prodotto](~/ef6/media/productmodeltemplate.png)

-   Fare doppio clic sul file ProductModel.tt per aprirlo nell'editor di Visual Studio
-   Trovare e sostituire le due occorrenze di "**ICollection**" con "**ObservableListSource**". Si trovano a circa linee 296 e 484.
-   Trovare e sostituire la prima occorrenza di "**HashSet**" con "**ObservableListSource**". Questa occorrenza si trova approssimativamente alla riga 50. **Non sostituire la** seconda occorrenza di HashSet rilevata successivamente nel codice.
-   Salvare il file ProductModel.tt. Questa operazione dovrebbe causare la rigenerazione del codice per le entità. Se il codice non viene rigenerato automaticamente, fare clic con il pulsante destro del mouse su ProductModel.tt e scegliere "Esegui strumento personalizzato".

Se ora si apre il file Category.cs (annidato in ProductModel.tt), si noterà che la raccolta Products ha il tipo **ObservableListSource &lt; Product &gt; **.

Compilare il progetto.

## <a name="lazy-loading"></a>Caricamento lazy

La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione. In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.

EF offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione. Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.

Quando si usano i tipi di entità POCO, EF raggiunge il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento. Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) ed è necessario che la classe non sia **sealed** (**NotOverridable** in Visual Basic). Quando si usano Database First proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy. Nella sezione Code First si è scelto di rendere virtuali le proprietà di navigazione per lo stesso motivo

## <a name="bind-object-to-controls"></a>Associa oggetto a controlli

Aggiungere le classi definite nel modello come origini dati per l'applicazione Windows Form.

-   Dal menu principale selezionare **progetto- &gt; Aggiungi nuova origine dati...**
    in Visual Studio 2010 è necessario selezionare **dati- &gt; Aggiungi nuova origine dati...**
-   Nella finestra scegliere un tipo di origine dati selezionare **oggetto** e fare clic su **Avanti** .
-   Nella finestra di dialogo selezionare gli oggetti dati, espandere **WinFormswithEFSample** due volte e selezionare **Category** . non è necessario selezionare l'origine dati del prodotto, perché verrà visualizzata tramite la proprietà del prodotto nell'origine dati Category.

    ![origine dati](~/ef6/media/datasource.png)

-   Fare clic su **Finish** (Fine).
    Se la finestra Origini dati non viene visualizzata, selezionare **Visualizza- &gt; altre finestre- &gt; origini dati**
-   Premere l'icona Aggiungi, in modo che la finestra Origini dati non nasconda automaticamente. Se la finestra è già visibile, potrebbe essere necessario fare clic sul pulsante Aggiorna.

    ![Origine dati 2](~/ef6/media/datasource2.png)

-   In Esplora soluzioni fare doppio clic sul file **Form1.cs** per aprire il modulo principale in progettazione.
-   Selezionare l'origine dati **Category** e trascinarla nel form. Per impostazione predefinita, alla finestra di progettazione vengono aggiunti un nuovo DataGridView (**categoryDataGridView**) e i controlli della barra degli strumenti di navigazione. Questi controlli sono associati ai componenti BindingSource (**categoryBindingSource**) e**CategoryBindingNavigator**(Binding Navigator) creati.
-   Modificare le colonne in **categoryDataGridView**. Si vuole impostare la colonna **CategoryID** su sola lettura. Il valore della proprietà **CategoryID** viene generato dal database dopo il salvataggio dei dati.
    -   Fare clic con il pulsante destro del mouse sul controllo DataGridView e scegliere Modifica colonne...
    -   Selezionare la colonna CategoryId e impostare ReadOnly su true
    -   Premere OK
-   Selezionare Products dall'origine dati Category e trascinarlo nel form. ProductDataGridView e productBindingSource vengono aggiunti al form.
-   Modificare le colonne in productDataGridView. Si desidera nascondere le colonne CategoryId e Category e impostare ProductId su Read-only. Il valore della proprietà ProductId viene generato dal database dopo il salvataggio dei dati.
    -   Fare clic con il pulsante destro del mouse sul controllo DataGridView e scegliere **modifica colonne..**..
    -   Selezionare la colonna **ProductID** e impostare **ReadOnly** su **true**.
    -   Selezionare la colonna **CategoryID** e premere il pulsante **Rimuovi** . Eseguire la stessa operazione con la colonna **Category** .
    -   Fare clic su **OK**.

    Finora sono stati associati i controlli DataGridView ai componenti BindingSource nella finestra di progettazione. Nella sezione successiva verrà aggiunto il codice al code-behind per impostare categoryBindingSource. DataSource sulla raccolta di entità attualmente rilevate da DbContext. Quando sono stati trascinati e eliminati prodotti da sotto la categoria, il componente WinForms si è occupata della configurazione della proprietà productsBindingSource. DataSource sulla proprietà categoryBindingSource e productsBindingSource. DataMember sui prodotti. A causa di questa associazione, solo i prodotti che appartengono alla categoria attualmente selezionata verranno visualizzati in productDataGridView.
-   Abilitare il pulsante **Salva** sulla barra degli strumenti di spostamento facendo clic con il pulsante destro del mouse e selezionando **abilitato**.

    ![Finestra di progettazione form 1](~/ef6/media/form1-designer.png)

-   Aggiungere il gestore eventi per il pulsante Salva facendo doppio clic sul pulsante. Verrà aggiunto il gestore eventi e verrà riportato il code-behind per il form. Il codice per il gestore dell'evento ** \_ Click categoryBindingNavigatorSaveItem** verrà aggiunto nella sezione successiva.

## <a name="add-the-code-that-handles-data-interaction"></a>Aggiungere il codice che gestisce l'interazione dei dati

A questo punto verrà aggiunto il codice per usare ProductContext per eseguire l'accesso ai dati. Aggiornare il codice per la finestra del form principale, come illustrato di seguito.

Il codice dichiara un'istanza con esecuzione prolungata di ProductContext. L'oggetto ProductContext viene utilizzato per eseguire query e salvare i dati nel database. Il metodo Dispose () sull'istanza ProductContext viene quindi chiamato dal metodo OnClosing sottoposto a override. I commenti del codice forniscono informazioni dettagliate sul funzionamento del codice.

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

## <a name="test-the-windows-forms-application"></a>Testare l'applicazione Windows Forms

-   Compilare ed eseguire l'applicazione ed è possibile testare la funzionalità.

    ![Modulo 1 prima del salvataggio](~/ef6/media/form1beforesave.png)

-   Dopo il salvataggio, le chiavi generate dall'archivio vengono visualizzate sullo schermo.

    ![Modulo 1 dopo il salvataggio](~/ef6/media/form1aftersave.png)

-   Se è stato usato Code First, si noterà anche che viene creato un database **WinFormswithEFSample. ProductContext** .

    ![Esplora oggetti server](~/ef6/media/serverobjexplorer.png)
