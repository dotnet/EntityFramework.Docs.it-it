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
# <a name="getting-started-with-wpf"></a>Introduzione a WPF

Questa procedura dettagliata illustra come associare tipi POCO ai controlli WPF in un modulo "Main-Detail". L'applicazione usa le API Entity Framework per popolare gli oggetti con i dati del database, rilevare le modifiche e salvare in modo permanente i dati nel database.

Il modello definisce due tipi che fanno parte di una relazione uno-a-molti: **categoria** (principale \\ principale) e **prodotto** ( \\ Dettagli dipendenti). Il Framework di associazione dei dati WPF consente la navigazione tra oggetti correlati: la selezione di righe nella visualizzazione master comporta l'aggiornamento della visualizzazione dettagli con i dati figlio corrispondenti.

Le schermate e gli elenchi di codice in questa procedura dettagliata sono ricavati da Visual Studio 2019 16.6.5.

> [!TIP]
> È possibile visualizzare l'esempio di questo articolo [in GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).

## <a name="pre-requisites"></a>Prerequisiti

* Per completare questa procedura dettagliata, è necessario aver installato Visual Studio 2019 16,3 o versione successiva con il **carico di lavoro di .NET desktop** selezionato.
    
    Per ulteriori informazioni sull'installazione della versione più recente di Visual Studio, vedere [Install Visual Studio](/visualstudio/install/install-visual-studio).

## <a name="create-the-application"></a>Creare l'applicazione

1. Aprire Visual Studio.
2. Nella finestra iniziale scegliere **Crea nuovo progetto**.
3. Cercare "WPF", scegliere **app WPF (.NET Core)** , quindi fare clic su **Avanti**.
4. Nella schermata successiva assegnare un nome al progetto, ad esempio **GetStartedWPF**, e scegliere **Crea.**

## <a name="install-the-entity-framework-nuget-packages"></a>Installare i pacchetti NuGet di Entity Framework

1. Fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione...**

    ![Manage NuGet Packages](_static/wpf-tutorial-nuget.jpg)

1. Digitare `entityframeworkcore.sqlite` nella casella di ricerca.
1. Selezionare il pacchetto **Microsoft. EntityFrameworkCore. sqlite** .
1. Controllare il progetto nel riquadro destro e fare clic su **Installa** .

    ![Pacchetto sqlite](_static/wpf-tutorial-sqlite.jpg)

1. Ripetere i passaggi per cercare `entityframeworkcore.proxies` e installare **Microsoft. EntityFrameworkCore. proxy**.

> [!NOTE]
> Quando è stato installato il pacchetto SQLite, viene automaticamente eseguito il pull del pacchetto di base **Microsoft. EntityFrameworkCore** correlato. Il pacchetto **Microsoft. EntityFrameworkCore. proxy** fornisce supporto per i dati di caricamento lazy. Ciò significa che quando si hanno entità con entità figlio, solo gli elementi padre vengono recuperati al caricamento iniziale. I proxy rilevano quando viene eseguito un tentativo di accesso alle entità figlio e li carica automaticamente su richiesta. 

## <a name="define-a-model"></a>Definire un modello

In questa procedura dettagliata verrà implementato un modello utilizzando "Code First". Ciò significa che EF Core creerà le tabelle e lo schema del database in base alle classi C# definite.

Aggiungere una nuova classe. Assegnargli il nome: `Product.cs` e popolarlo come segue:

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

Aggiungere quindi una classe denominata `Category.cs` e popolarla con il codice seguente:

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione. In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.

Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; . Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.

Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.

Aggiungere una nuova `ProductContext.cs` classe al progetto con la definizione seguente:

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* `DbSet`Informa EF Core quali entità C# devono essere mappate al database.
* Esistono diversi modi per configurare la EF Core `DbContext` . È possibile leggere le informazioni in: [configurazione di un DbContext](xref:core/miscellaneous/configuring-dbcontext).
* Questo esempio usa l' `OnConfiguring` override per specificare un file di dati SQLite.
* La `UseLazyLoadingProxies` chiamata indica EF Core di implementare il caricamento lazy, in modo che le entità figlio vengano caricate automaticamente al momento dell'accesso dall'elemento padre.

Premere **CTRL + MAIUSC + B** o passare a **Compila compilazione &gt; soluzione** per compilare il progetto.

> [!TIP]
> Per informazioni sulle diverse operazioni, vedere la pagina relativa alla gestione degli schemi di [database](xref:core/managing-schemas/index)e dei modelli di EF core in sincronizzazione.

## <a name="lazy-loading"></a>Caricamento lazy

La proprietà **Products** della classe **Category** e della proprietà **Category** della classe **Product** sono proprietà di navigazione. In Entity Framework Core, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.

EF Core offre la possibilità di caricare automaticamente le entità correlate dal database al primo accesso alla proprietà di navigazione. Con questo tipo di caricamento (denominato caricamento lazy), tenere presente che la prima volta che si accede a ogni proprietà di navigazione viene eseguita una query separata sul database se il contenuto non è già presente nel contesto.

Quando si usano tipi di entità POCO (Plain Old C# Object), EF Core ottiene il caricamento lazy creando istanze di tipi proxy derivati durante il runtime e quindi eseguendo l'override delle proprietà virtuali nelle classi per aggiungere l'hook di caricamento. Per ottenere il caricamento lazy di oggetti correlati, è necessario dichiarare i getter della proprietà di navigazione come **public** e **Virtual** (**sottoponibile a override** in Visual Basic) e la classe non deve essere **sealed** (**NotOverridable** in Visual Basic). Quando si usa Database First, le proprietà di navigazione vengono rese automaticamente virtuali per consentire il caricamento lazy. 

## <a name="bind-object-to-controls"></a>Associa oggetto a controlli

Aggiungere le classi definite nel modello come origini dati per l'applicazione WPF.

1. Fare doppio clic su **MainWindow. XAML** in Esplora soluzioni per aprire il modulo principale
1. Scegliere la scheda **XAML** per modificare il codice XAML.
1. Subito dopo il tag di apertura `Window` aggiungere le origini seguenti per connettersi alle entità EF core.

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. In questo modo viene impostata l'origine per le categorie "padre" e la seconda origine per i prodotti "Detail".
1. Successivamente, aggiungere il markup seguente al codice XAML dopo il tag di chiusura `Window.Resources` .

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. Si noti che la `CategoryId` è impostata su `ReadOnly` perché è assegnata dal database e non può essere modificata.

## <a name="adding-a-details-grid"></a>Aggiunta di una griglia dettagli

Ora che la griglia esiste per visualizzare le categorie, è possibile aggiungere la griglia dei dettagli per visualizzare i prodotti.

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

Infine, aggiungere un `Save` pulsante e un cavo nell'evento click a `Button_Click` .

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

La visualizzazione progettazione dovrebbe essere simile alla seguente:

![Screenshot della finestra di progettazione WPF](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a>Aggiungere codice che gestisce l'interazione dei dati

È il momento di aggiungere alcuni gestori di eventi alla finestra principale.

1. Nella finestra XAML fare clic sull'elemento ** &lt; Window ( &gt; finestra** ) per selezionare la finestra principale.
1. Nella finestra **Proprietà** scegliere **eventi** in alto a destra, quindi fare doppio clic sulla casella di testo a destra dell'etichetta **caricata** .

    ![Proprietà della finestra principale](_static/wpf-tutorial-loaded.jpg)

In questo modo viene riportato il codice sottostante per il modulo. verrà ora modificato il codice per usare `ProductContext` per eseguire l'accesso ai dati. Aggiornare il codice come illustrato di seguito.

Il codice dichiara un'istanza con esecuzione prolungata di `ProductContext` . L' `ProductContext` oggetto viene utilizzato per eseguire query e salvare i dati nel database. Il `Dispose()` metodo sull' `ProductContext` istanza viene quindi chiamato dal metodo sottoposto a override `OnClosing` .I commenti del codice spiegano cosa accade in ogni passaggio.

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> Il codice usa una chiamata a `EnsureCreated()` per compilare il database alla prima esecuzione. Questa operazione è accettabile per le demo, ma nelle app di produzione è consigliabile esaminare le [migrazioni](xref:core/managing-schemas/migrations/index) per gestire lo schema. Anche il codice viene eseguito in modo sincrono perché usa un database SQLite locale. Per gli scenari di produzione che in genere coinvolgono un server remoto, è consigliabile usare le versioni asincrone dei `Load` `SaveChanges` metodi e.

## <a name="test-the-wpf-application"></a>Testare l'applicazione WPF

Compilare ed eseguire l'applicazione premendo **F5** o scegliendo **debug &gt; Avvia debug**. Il database deve essere creato automaticamente con un file denominato `products.db` . Immettere un nome di categoria e premere INVIO, quindi aggiungere prodotti alla griglia inferiore. Fare clic su Salva e controllare l'aggiornamento della griglia con gli ID forniti dal database. Evidenziare una riga e fare clic su **Elimina** per rimuovere la riga. L'entità verrà eliminata quando si fa clic su **Salva**.

![Applicazione in esecuzione](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a>Notifica di modifiche alle proprietà

Questo esempio si basa su quattro passaggi per sincronizzare le entità con l'interfaccia utente.

1. La chiamata iniziale `_context.Categories.Load()` carica i dati delle categorie.
1. I proxy di caricamento lazy caricano i dati dei prodotti dipendenti.
1. Il rilevamento delle modifiche incorporato EF Core apporta le modifiche necessarie alle entità, incluse le operazioni di inserimento ed eliminazione, quando `_context.SaveChanges()` viene chiamato il metodo.
1. Chiamate per `DataGridView.Items.Refresh()` forzare un ricaricamento con gli ID appena generati.

Questa operazione è valida per l'esempio introduttivo, ma potrebbe essere necessario codice aggiuntivo per altri scenari. I controlli WPF eseguono il rendering dell'interfaccia utente leggendo i campi e le proprietà delle entità. Quando si modifica un valore nell'interfaccia utente, tale valore viene passato all'entità. Quando si modifica il valore di una proprietà direttamente nell'entità, ad esempio il caricamento dal database, WPF non rifletterà immediatamente le modifiche nell'interfaccia utente. Il motore di rendering deve ricevere notifiche delle modifiche. Il progetto ha fatto questa operazione chiamando manualmente `Refresh()` . Un modo semplice per automatizzare questa notifica consiste nell'implementare l'interfaccia [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) . I componenti WPF rileveranno automaticamente l'interfaccia e si registreranno per gli eventi di modifica. L'entità è responsabile della generazione di questi eventi.

> [!TIP]
> Per ulteriori informazioni su come gestire le modifiche, vedere [la pagina relativa all'implementazione della notifica di modifica delle proprietà](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [configurazione di un DbContext](xref:core/miscellaneous/configuring-dbcontext).
