---
title: Suddivisione delle entità della finestra di progettazione-EF6
description: Suddivisione delle entità della finestra di progettazione in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: d3be4e54d4bcd3ca253d8970c612acddf48dbaf4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620522"
---
# <a name="designer-entity-splitting"></a>Suddivisione delle entità della finestra di progettazione
Questa procedura dettagliata illustra come eseguire il mapping di un tipo di entità a due tabelle modificando un modello con il Entity Framework Designer (EF designer). È possibile eseguire il mapping di un'entità a più tabelle quando le tabelle in questione condividono una chiave comune. I concetti relativi all'esecuzione del mapping di un tipo di entità a due tabelle possono essere facilmente estesi anche all'esecuzione del mapping a più di due tabelle.

Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Prerequisiti

Visual Studio 2012 o Visual Studio 2010, Ultimate, Premium, Professional o Web Express Edition.

## <a name="create-the-database"></a>Creare il database

Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:

-   Se si usa Visual Studio 2012, si creerà un database del database locale.
-   Se si usa Visual Studio 2010 verrà creato un database di SQL Express.

Verrà innanzitutto creato un database con due tabelle che verranno combinate in una singola entità.

-   Aprire Visual Studio.
-   **Visualizza- &gt; Esplora server**
-   Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**
-   Se non si è connessi a un database da Esplora server prima di selezionare **Microsoft SQL Server** come origine dati
-   Connettersi a un database locale o a SQL Express, a seconda di quale installato
-   Immettere **EntitySplitting** come nome del database
-   Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .
-   Il nuovo database verrà ora visualizzato in Esplora server
-   Se si usa Visual Studio 2012
    -   Fare clic con il pulsante destro del mouse sul database in Esplora server e scegliere **Nuova query**
    -   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .
-   Se si usa Visual Studio 2010
    -   Selezione **dati- &gt; Editor Transact-SQL- &gt; nuova connessione query...**
    -   Immettere **. \\ SQLEXPRESS** come nome del server e fare clic su **OK**
    -   Selezionare il database **EntitySplitting** dall'elenco a discesa nella parte superiore dell'editor di query
    -   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui SQL**

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a>Creare il progetto

-   Scegliere **Nuovo** dal menu **File**e quindi fare clic su **Progetto**.
-   Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **applicazione console** .
-   Immettere **MapEntityToTablesSample** come nome del progetto e fare clic su **OK**.
-   Fare clic su **No** se viene richiesto di salvare la query SQL creata nella prima sezione.

## <a name="create-a-model-based-on-the-database"></a>Creazione di un modello basato sul database

-   Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento**.
-   Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
-   Immettere **MapEntityToTablesModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.
-   Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti.**
-   Selezionare la connessione **EntitySplitting** dall'elenco a discesa e fare clic su **Avanti**.
-   Nella finestra di dialogo Scegli oggetti di database selezionare la casella accanto al nodo **tabelle**   .
    In questo modo, tutte le tabelle del database **EntitySplitting** vengono aggiunte al modello.
-   Fare clic su **fine**.

Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.

## <a name="map-an-entity-to-two-tables"></a>Eseguire il mapping di un'entità a due tabelle

In questo passaggio verrà aggiornato il tipo di entità **Person** per combinare i dati delle tabelle **Person** e **PersonInfo** .

-   Selezionare le proprietà di **posta elettronica**   e **telefono** dell'entità **PersonInfo **e premere **CTRL + X** .
-   Selezionare l'entità **Person **e premere **CTRL + V** .
-   Nell'area di progettazione selezionare l'entità **PersonInfo**   e premere il pulsante **Elimina** sulla tastiera.
-   Fare clic su **No** quando viene richiesto se si desidera rimuovere la tabella **PersonInfo** dal modello, si sta per eseguirne il mapping all'entità **Person** .

    ![Elimina tabelle](~/ef6/media/deletetables.png)

Per i passaggi successivi è necessaria la finestra **Dettagli mapping**   . Se questa finestra non è visibile, fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Dettagli mapping**.

-   Selezionare il **Person**   tipo di entità Person e fare clic su ** &lt; Aggiungi tabella &gt; o vista**   nella finestra **Dettagli mapping**   .
-   Selezionare **PersonInfo **   nell'elenco a discesa.
    La finestra **Dettagli mapping**   viene aggiornata con i mapping predefiniti delle colonne, per questo scenario.

Il **Person**   tipo di entità Person viene ora mappato alle tabelle **Person**   e **PersonInfo**   .

![Mapping 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>Usare il modello

-   Incollare il codice seguente nel metodo Main.

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   Compilare l'applicazione ed eseguirla.

Le istruzioni T-SQL seguenti sono state eseguite sul database in seguito all'esecuzione dell'applicazione. 

-   Le due istruzioni **Insert** seguenti sono state eseguite in seguito all'esecuzione del contesto. SaveChanges (). I dati vengono presi dall'entità **Person** e suddivisi tra le tabelle **Person** e **PersonInfo** .

    ![Inserisci 1](~/ef6/media/insert1.png)

    ![Inserisci 2](~/ef6/media/insert2.png)
-   La seguente **selezione** è stata eseguita in seguito all'enumerazione degli utenti nel database. Combina i dati della tabella **Person** e **PersonInfo** .

    ![Selezionare la combinazione di dati Person e PersonInfo](~/ef6/media/select.png)
