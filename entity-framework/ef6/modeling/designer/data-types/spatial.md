---
title: Spatial-EF designer-EF6
description: Spatial-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 06baa6e1-d680-4a95-845b-81305c87a962
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 10430d6a043182e5536dfad148dc4158cf4c96c9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620488"
---
# <a name="spatial---ef-designer"></a>Finestra di progettazione spaziale-EF
> [!NOTE]
> **EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Il video e la procedura dettagliata illustrano come eseguire il mapping di tipi spaziali con la Entity Framework Designer. Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.

Questa procedura dettagliata utilizzerà Model First per creare un nuovo database, ma è anche possibile usare la finestra di progettazione EF con il flusso di lavoro [database First](xref:ef6/modeling/designer/workflows/database-first) per eseguire il mapping a un database esistente.

Il supporto del tipo spaziale è stato introdotto in Entity Framework 5. Si noti che per utilizzare le nuove funzionalità, ad esempio il tipo spaziale, le enumerazioni e le funzioni con valori di tabella, è necessario definire come destinazione .NET Framework 4,5. Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.

Per utilizzare i tipi di dati spaziali, è necessario utilizzare anche un provider Entity Framework con supporto spaziale. Per ulteriori informazioni, vedere [supporto del provider per i tipi spaziali](xref:ef6/fundamentals/providers/spatial-support) .

Esistono due tipi di dati spaziali principali: geography e Geometry. Il tipo di dati geography archivia dati ellissoidali, ad esempio coordinate di latitudine e longitudine GPS. Il tipo di dati geometry rappresenta un sistema di coordinate euclideo (piano).

## <a name="watch-the-video"></a>Video
In questo video viene illustrato come eseguire il mapping di tipi spaziali con la Entity Framework Designer. Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.

**Presentato da**: Julia Kornich

**Video**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.

## <a name="set-up-the-project"></a>Configurare il progetto

1.  Aprire Visual Studio 2012
2.  Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .
3.  Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
4.  Immettere **SpatialEFDesigner** come nome del progetto e fare clic su **OK** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Creare un nuovo modello usando EF designer

1.  Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento** .
2.  Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
3.  Immettere **UniversityModel. edmx** per il nome del file e quindi fare clic su **Aggiungi** .
4.  Nella pagina Entity Data Model procedura guidata selezionare **modello vuoto** nella finestra di dialogo Scegli contenuto Model
5.  Fare clic su **Fine**

Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.

La procedura guidata consente di effettuare le azioni seguenti:

-   Genera il file EnumTestModel. edmx che definisce il modello concettuale, il modello di archiviazione e il mapping tra di essi. Imposta la proprietà di elaborazione degli artefatti dei metadati del file con estensione edmx da incorporare nell'assembly di output in modo che i file di metadati generati vengano incorporati nell'assembly.
-   Aggiunge un riferimento agli assembly seguenti: EntityFramework, System. ComponentModel. DataAnnotations e System. Data. Entity.
-   Crea i file UniversityModel.tt e UniversityModel.Context.tt e li aggiunge al file con estensione edmx. Questi file di modello T4 generano il codice che definisce il tipo derivato DbContext e i tipi POCO mappati alle entità nel modello con estensione edmx

## <a name="add-a-new-entity-type"></a>Aggiungere un nuovo tipo di entità

1.  Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi &gt; entità**. verrà visualizzata la finestra di dialogo nuova entità.
2.  Specificare **University** per il nome del tipo e specificare **UniversityID** per il nome della proprietà chiave, lasciare il tipo come **Int32**
3.  Fare clic su **OK**.
4.  Fare clic con il pulsante destro del mouse sull'entità e scegliere **Aggiungi nuova- &gt; scala proprietà**
5.  Rinominare la nuova proprietà in **nome**
6.  Aggiungere un'altra proprietà scalare e rinominarla in **location** aprire il finestra Proprietà e modificare il tipo della nuova proprietà in **geography**
7.  Salvare il modello e compilare il progetto
    > [!NOTE]
    > Quando si compila, i messaggi di avviso sulle entità e sulle associazioni non mappate possono essere visualizzati nel Elenco errori. È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.

## <a name="generate-database-from-model"></a>Genera database dal modello

A questo punto è possibile generare un database basato sul modello.

1.  Fare clic con il pulsante destro del mouse su uno spazio vuoto nell'area di Entity Designer e selezionare **genera database da modello** .
2.  Viene visualizzata la finestra di dialogo scegliere la connessione dati della procedura guidata genera database. fare clic sul pulsante **nuova connessione** specificare (local DB **) \\ mssqllocaldb** per il nome del server e l' **Università** del database e fare clic su **OK** .
3.  Viene visualizzata una finestra di dialogo in cui viene chiesto se si desidera creare un nuovo database, quindi fare clic su **Sì**.
4.  Fare clic su **Avanti** . la procedura guidata Crea database genera Data Definition Language (DDL) per la creazione di un database. la DDL generata viene visualizzata nella finestra di dialogo Riepilogo e impostazioni nota che il DDL non contiene una definizione per una tabella che esegue il mapping al tipo di enumerazione
5.  Fare **clic su fine per** non eseguire lo script DDL.
6.  La procedura guidata Crea database esegue le operazioni seguenti: apre **UniversityModel. edmx. SQL** nell'editor T-SQL genera le sezioni schema e mapping dell'archivio del file edmx aggiunge le informazioni sulla stringa di connessione al file di App.config
7.  Fare clic con il pulsante destro del mouse nell'editor T-SQL e selezionare **Esegui** la finestra di dialogo Connetti al server, immettere le informazioni di connessione nel passaggio 2 e fare clic su **Connetti** .
8.  Per visualizzare lo schema generato, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna** .

## <a name="persist-and-retrieve-data"></a>Mantieni e recupera dati

Aprire il file Program.cs in cui è definito il metodo Main. Aggiungere il codice seguente nella funzione Main.

Il codice aggiunge due nuovi oggetti University al contesto. Le proprietà spaziali vengono inizializzate tramite il Metodo DbGeography. FromText. Il punto geografico rappresentato come WellKnownText viene passato al metodo. Il codice salva quindi i dati. Quindi, la query LINQ che restituisce un oggetto University in cui la posizione è più vicina alla posizione specificata viene costruita ed eseguita.

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

Compilare l'applicazione ed eseguirla. Il programma produce l'output seguente:

```console
The closest University to you is: School of Fine Art.
```

Per visualizzare i dati nel database, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna**. Quindi, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati**.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato illustrato come eseguire il mapping dei tipi spaziali usando il Entity Framework Designer e come usare i tipi spaziali nel codice. 
