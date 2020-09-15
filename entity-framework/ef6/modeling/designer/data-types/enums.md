---
title: Supporto enum-EF designer-EF6
description: Supporto enum-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: f3dff2c71cfdeb32183865265144a938eff087c0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073123"
---
# <a name="enum-support---ef-designer"></a>Supporto enum-EF designer
> [!NOTE]
> **EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Questo video e la procedura dettagliata illustrano come usare i tipi enum con la Entity Framework Designer. Viene inoltre illustrato come utilizzare le enumerazioni in una query LINQ.

Questa procedura dettagliata utilizzerà Model First per creare un nuovo database, ma è anche possibile usare la finestra di progettazione EF con il flusso di lavoro [database First](xref:ef6/modeling/designer/workflows/database-first) per eseguire il mapping a un database esistente.

Il supporto enum è stato introdotto in Entity Framework 5. Per utilizzare le nuove funzionalità come enum, i tipi di dati spaziali e le funzioni con valori di tabella, è necessario specificare come destinazione .NET Framework 4,5. Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.

In Entity Framework, un'enumerazione può includere i seguenti tipi sottostanti: **byte**, **Int16**, **Int32**, **Int64** o **SByte**.

## <a name="watch-the-video"></a>Guarda il video
In questo video viene illustrato come utilizzare i tipi enum con la Entity Framework Designer. Viene inoltre illustrato come utilizzare le enumerazioni in una query LINQ.

**Presentato da**: Julia Kornich

**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.

## <a name="set-up-the-project"></a>Configurare il progetto

1.  Aprire Visual Studio 2012
2.  Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .
3.  Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
4.  Immettere **EnumEFDesigner** come nome del progetto e fare clic su **OK** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Creare un nuovo modello usando EF designer

1.  Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento** .
2.  Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
3.  Immettere **EnumTestModel. edmx** per il nome del file e quindi fare clic su **Aggiungi** .
4.  Nella pagina Entity Data Model procedura guidata selezionare **modello vuoto** nella finestra di dialogo Scegli contenuto Model
5.  Fare clic su **Fine**

Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.

La procedura guidata consente di effettuare le azioni seguenti:

-   Genera il file EnumTestModel. edmx che definisce il modello concettuale, il modello di archiviazione e il mapping tra di essi. Imposta la proprietà di elaborazione degli artefatti dei metadati del file con estensione edmx da incorporare nell'assembly di output in modo che i file di metadati generati vengano incorporati nell'assembly.
-   Aggiunge un riferimento agli assembly seguenti: EntityFramework, System. ComponentModel. DataAnnotations e System. Data. Entity.
-   Crea i file EnumTestModel.tt e EnumTestModel.Context.tt e li aggiunge al file con estensione edmx. Questi file di modello T4 generano il codice che definisce il tipo derivato DbContext e i tipi POCO mappati alle entità nel modello con estensione edmx.

## <a name="add-a-new-entity-type"></a>Aggiungere un nuovo tipo di entità

1.  Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi &gt; entità**. verrà visualizzata la finestra di dialogo nuova entità.
2.  Specificare **Department** per il nome del tipo e specificare **DepartmentID** per il nome della proprietà chiave, lasciare il tipo come **Int32**
3.  Fare clic su **OK**.
4.  Fare clic con il pulsante destro del mouse sull'entità e scegliere **Aggiungi nuova- &gt; scala proprietà**
5.  Rinominare la nuova proprietà in **nome**
6.  Modificare il tipo della nuova proprietà in **Int32** (per impostazione predefinita, la nuova proprietà è di tipo stringa) per modificare il tipo, aprire il finestra Proprietà e modificare la proprietà Type in **Int32** .
7.  Aggiungere un'altra proprietà scalare e rinominarla in **budget**, modificare il tipo in **Decimal**

## <a name="add-an-enum-type"></a>Aggiungere un tipo enum

1.  Nella Entity Framework Designer fare clic con il pulsante destro del mouse sulla proprietà nome, quindi scegliere **Converti in enum**

    ![Converti in enum](~/ef6/media/converttoenum.png)

2.  Nella finestra di dialogo **Aggiungi enum** digitare **DepartmentNames** per il nome del tipo enum, modificare il tipo sottostante in **Int32**, quindi aggiungere i membri seguenti al tipo: English, Math ed Economics

    ![Aggiungi tipo enum](~/ef6/media/addenumtype.png)

3.  Premi **OK**
4.  Salvare il modello e compilare il progetto
    > [!NOTE]
    > Quando si compila, i messaggi di avviso sulle entità e sulle associazioni non mappate possono essere visualizzati nel Elenco errori. È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.

Se si esaminano le Finestra Proprietà, si noterà che il tipo della proprietà Name è stato modificato in **DepartmentNames** e che il tipo enum appena aggiunto è stato aggiunto all'elenco di tipi.

Se si passa alla finestra browser modello, si noterà che il tipo è stato aggiunto anche al nodo tipi enum.

![Browser modello](~/ef6/media/modelbrowser.png)

>[!NOTE]
> È anche possibile aggiungere nuovi tipi enum da questa finestra facendo clic con il pulsante destro del mouse e selezionando **Aggiungi tipo enum**. Una volta creato, il tipo verrà visualizzato nell'elenco dei tipi e sarà possibile associarlo a una proprietà

## <a name="generate-database-from-model"></a>Genera database dal modello

A questo punto è possibile generare un database basato sul modello.

1.  Fare clic con il pulsante destro del mouse su uno spazio vuoto nell'area di Entity Designer e selezionare **genera database da modello** .
2.  Viene visualizzata la finestra di dialogo scegliere la connessione dati della procedura guidata genera database. fare clic sul pulsante **nuova connessione** specificare (local DB **) \\ mssqllocaldb** per il nome del server e **EnumTest** per il database e fare clic su **OK** .
3.  Viene visualizzata una finestra di dialogo in cui viene chiesto se si desidera creare un nuovo database, quindi fare clic su **Sì**.
4.  Fare clic su **Avanti** . la procedura guidata Crea database genera Data Definition Language (DDL) per la creazione di un database. la DDL generata viene visualizzata nella finestra di dialogo Riepilogo e impostazioni nota che il DDL non contiene una definizione per una tabella che esegue il mapping al tipo di enumerazione
5.  Fare **clic su fine per** non eseguire lo script DDL.
6.  La procedura guidata Crea database esegue le operazioni seguenti: apre **EnumTest. edmx. SQL** nell'editor T-SQL genera le sezioni schema e mapping dell'archivio del file edmx aggiunge le informazioni sulla stringa di connessione al file di App.config
7.  Fare clic con il pulsante destro del mouse nell'editor T-SQL e selezionare **Esegui** la finestra di dialogo Connetti al server, immettere le informazioni di connessione nel passaggio 2 e fare clic su **Connetti** .
8.  Per visualizzare lo schema generato, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna** .

## <a name="persist-and-retrieve-data"></a>Mantieni e recupera dati

Aprire il file Program.cs in cui è definito il metodo Main. Aggiungere il codice seguente nella funzione Main. Il codice aggiunge un nuovo oggetto Department al contesto. Quindi Salva i dati. Il codice esegue anche una query LINQ che restituisce un reparto in cui il nome è DepartmentNames. English.

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Compilare l'applicazione ed eseguirla. Il programma produce l'output seguente:

```console
DepartmentID: 1 Name: English
```

Per visualizzare i dati nel database, fare clic con il pulsante destro del mouse sul nome del database in Esplora oggetti di SQL Server e selezionare **Aggiorna**. Quindi, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati**.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato illustrato come eseguire il mapping dei tipi enum usando il Entity Framework Designer e come usare le enumerazioni nel codice. 
