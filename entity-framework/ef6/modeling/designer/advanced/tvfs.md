---
title: Funzioni Table-Valued (funzioni con valori)-EF6
description: Funzioni Table-Valued (funzioni con valori) in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 2c699cad3ace253842d17aca4eca4df2318c4795
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066200"
---
# <a name="table-valued-functions-tvfs"></a>Funzioni Table-Valued (funzioni con valori)
> [!NOTE]
> **EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Il video e la procedura dettagliata illustrano come eseguire il mapping di funzioni con valori di tabella (funzioni con valori) usando il Entity Framework Designer. Viene inoltre illustrato come chiamare un TVF da una query LINQ.

Funzioni con valori sono attualmente supportati solo nel flusso di lavoro Database First.

Il supporto di TVF è stato introdotto in Entity Framework versione 5. Si noti che per utilizzare le nuove funzionalità, ad esempio le funzioni con valori di tabella, le enumerazioni e i tipi spaziali, è necessario definire come destinazione .NET Framework 4,5. Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.

Funzioni con valori sono molto simili alle stored procedure con una differenza fondamentale: il risultato di un TVF è componibile. Ciò significa che i risultati di un TVF possono essere utilizzati in una query LINQ mentre i risultati di un stored procedure non possono.

## <a name="watch-the-video"></a>Video

**Presentato da**: Julia Kornich

[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  |  [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario:

- Installare il [database School](xref:ef6/resources/school-database).

- Avere una versione recente di Visual Studio

## <a name="set-up-the-project"></a>Configurare il progetto

1.  Aprire Visual Studio
2.  Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .
3.  Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
4.  Immettere **TVF** come nome del progetto e fare clic su **OK** .

## <a name="add-a-tvf-to-the-database"></a>Aggiungere un TVF al database

-   Selezionare **Visualizza- &gt; Esplora oggetti di SQL Server**
-   Se il database locale non è presente nell'elenco dei server: fare clic con il pulsante destro del mouse su **SQL Server** e scegliere **Aggiungi SQL Server** utilizzare l' **autenticazione di Windows** predefinita per la connessione al server del database locale.
-   Espandere il nodo del database locale
-   Nel nodo database fare clic con il pulsante destro del mouse sul nodo School database e scegliere **nuova query...**
-   Nell'editor T-SQL incollare la definizione TVF seguente

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   Fare clic con il pulsante destro del mouse sull'editor T-SQL e selezionare **Esegui** .
-   La funzione GetStudentGradesForCourse viene aggiunta al database School

 

## <a name="create-a-model"></a>Creare il modello

1.  Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento** .
2.  Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro **modelli** .
3.  Immettere **TVFModel. edmx** per il nome del file e quindi fare clic su **Aggiungi** .
4.  Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti** .
5.  Fare clic su **nuova connessione** immettere (local DB **) \\ mssqllocaldb** nella casella di testo nome server immettere **School**   per il nome del database, quindi fare clic su **OK**
6.  Nella finestra di dialogo Scegli oggetti di database, sotto il nodo **tabelle**   , selezionare le **tabelle Person**, **StudentGrade**e **Course**  
7.  Selezionare la funzione **GetStudentGradesForCourse** situata sotto la nota relativa al nodo **stored procedure e funzioni**   , che a partire da Visual Studio 2012, il Entity Designer consente di importare in batch le stored procedure e le funzioni
8.  Fare clic su **fine**
9.  Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello. Tutti gli oggetti selezionati nella finestra di dialogo **Scegli oggetti di database**   vengono aggiunti al modello.
10. Per impostazione predefinita, la forma risultante di ogni stored procedure o funzione importata diventerà automaticamente un nuovo tipo complesso nel modello di entità. Tuttavia, si desidera eseguire il mapping dei risultati della funzione GetStudentGradesForCourse all'entità StudentGrade: fare clic con il pulsante destro del mouse sull'area di progettazione e selezionare **browser modello** in browser modello, selezionare **importazioni di funzioni**, quindi fare doppio clic sulla funzione **GetStudentGradesForCourse** nella finestra di dialogo modifica importazione funzione, selezionare **entità**   e scegliere **StudentGrade**

## <a name="persist-and-retrieve-data"></a>Mantieni e recupera dati

Aprire il file in cui è definito il metodo Main. Aggiungere il codice seguente nella funzione Main.

Nel codice seguente viene illustrato come compilare una query che utilizza una funzione con valori di tabella. La query proietta i risultati in un tipo anonimo che contiene il titolo del corso correlato e gli studenti correlati con un grado maggiore o uguale a 3,5.

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

Compilare l'applicazione ed eseguirla. Il programma produce l'output seguente:

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato illustrato come eseguire il mapping di funzioni con valori di tabella (funzioni con valori) utilizzando il Entity Framework Designer. È stato inoltre illustrato come chiamare un TVF da una query LINQ.
