---
title: Stored procedure CUD della finestra di progettazione-EF6
description: Stored procedure CUD della finestra di progettazione in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: fae795922980452cf062a62e3cbb47de49b49c74
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073214"
---
# <a name="designer-cud-stored-procedures"></a>Stored procedure CUD della finestra di progettazione

Questa procedura dettagliata illustra come eseguire il mapping delle \\ operazioni di creazione di istruzioni INSERT, Update e Delete (CUD) di un tipo di entità alle stored procedure usando il Entity Framework Designer (Entity Framework Designer). Per impostazione predefinita, il Entity Framework genera automaticamente le istruzioni SQL per le operazioni CUD, ma è anche possibile eseguire il mapping delle stored procedure a queste operazioni.  

Si noti che Code First non supporta il mapping a stored procedure o funzioni. Tuttavia, è possibile chiamare stored procedure o funzioni utilizzando il metodo System. Data. Entity. DbSet. sqlQuery. Esempio:

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>Considerazioni relative al mapping delle operazioni CUD alle stored procedure

Quando si esegue il mapping delle operazioni CUD alle stored procedure, si applicano le considerazioni seguenti:

- Se si esegue il mapping di una delle operazioni CUD a una stored procedure, eseguirne il mapping. Se non si esegue il mapping di tutti e tre, le operazioni non mappate avranno esito negativo se eseguite e verrà generata un'eccezione **UpdateException**   .
- È necessario eseguire il mapping di ogni parametro del stored procedure alle proprietà dell'entità.
- Se il server genera il valore della chiave primaria per la riga inserita, è necessario eseguire il mapping di questo valore alla proprietà della chiave dell'entità. Nell'esempio seguente il **InsertPerson**   stored procedure restituisce la chiave primaria appena creata come parte del set di risultati della stored procedure. Viene eseguito il mapping della chiave primaria alla chiave di entità (**PersonID**) usando la funzionalità di ** &lt; aggiunta dei binding &gt; di risultato**   di EF designer.
- Viene eseguito il mapping delle chiamate stored procedure 1:1 con le entità del modello concettuale. Se, ad esempio, si implementa una gerarchia di ereditarietà nel modello concettuale ed è quindi necessario eseguire il mapping delle stored procedure CUD per le entità **padre** (base) e **figlio** (derivata), il salvataggio delle modifiche **figlio** chiamerà solo le stored procedure del **figlio**, non attiverà le chiamate alle stored procedure del **padre**.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario disporre di:

- Una versione recente di Visual Studio.
- [Database di esempio School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurare il progetto

- Aprire Visual Studio 2012.
- Selezionare **file- &gt; nuovo- &gt; progetto**
- Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
- Immettere **CUDSProcsSample**   come nome.
- Selezionare **OK**.

## <a name="create-a-model"></a>Creare il modello

- Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.
- Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
- Immettere **CUDSProcs. edmx** per il nome del file e quindi fare clic su **Aggiungi**.
- Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.
- Fare clic su **nuova connessione**. Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.
    La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.
- Nella finestra di dialogo Scegli oggetti di database, nel nodo **tabelle**   , selezionare la tabella **Person** .
- Inoltre, selezionare le stored procedure seguenti nel nodo **stored procedure e funzioni** : **DeletePerson**, **InsertPerson**e **UpdatePerson**.
- A partire da Visual Studio 2012, EF Designer supporta l'importazione bulk di stored procedure. Per impostazione predefinita, l' **importazione delle stored procedure e delle funzioni selezionate nel modello di entità** è selezionata. Poiché in questo esempio sono presenti stored procedure che inseriscono, aggiornano ed eliminano i tipi di entità, non è necessario importarli e deselezionare questa casella di controllo.

    ![Procedure Import S](~/ef6/media/importsprocs.jpg)

- Fare clic su **fine**.
    Viene visualizzata la finestra di progettazione EF, che fornisce un'area di progettazione per la modifica del modello.

## <a name="map-the-person-entity-to-stored-procedures"></a>Eseguire il mapping dell'entità Person alle stored procedure

- Fare clic con il **Person**pulsante destro del mouse sul   tipo di entità Person e selezionare **stored procedure mapping**.
- I mapping di stored procedure vengono visualizzati nella finestra **Dettagli mapping**   .
- Fare clic su ** &lt; Seleziona &gt; Inserisci funzione**.
    Questo campo diventa l'elenco a discesa delle stored procedure contenute nel modello di archiviazione che possono essere mappate ai tipi di entità del modello concettuale.
    Selezionare **InsertPerson**   nell'elenco a discesa.
- Vengono visualizzati i mapping predefiniti tra i parametri delle stored procedure e le proprietà dell'entità. Come indicato dalle frecce, che mostrano la direzione del mapping, per i parametri delle stored procedure vengono specificati i valori delle proprietà.
- Fare clic su ** &lt; Aggiungi &gt; associazione di risultati**.
- Digitare **NewPersonID**, il nome del parametro restituito dal stored procedure **InsertPerson**   . Assicurarsi di non digitare spazi iniziali o finali.
- Premere **invio**.
- Per impostazione predefinita **NewPersonID**,   viene eseguito il mapping di NewPersonID alla chiave di entità **PersonID**. Come indicato dalle frecce, che mostrano la direzione del mapping, per la proprietà viene specificato il valore della colonna dei risultati.

    ![Dettagli mapping](~/ef6/media/mappingdetails.png)

- Fare clic su ** &lt; Seleziona &gt; funzione di aggiornamento**   e selezionare **UpdatePerson**   nell'elenco a discesa risultante.
- Vengono visualizzati i mapping predefiniti tra i parametri delle stored procedure e le proprietà dell'entità.
- Fare clic su ** &lt; Seleziona &gt; funzione di eliminazione**   e selezionare **DeletePerson**   nell'elenco a discesa risultante.
- Vengono visualizzati i mapping predefiniti tra i parametri delle stored procedure e le proprietà dell'entità.

Le operazioni di inserimento, aggiornamento ed eliminazione del tipo di entità **Person**   sono ora mappate alle stored procedure.

Per abilitare il controllo della concorrenza durante l'aggiornamento o l'eliminazione di un'entità con stored procedure, utilizzare una delle opzioni seguenti:

- Utilizzare un parametro di **output** per restituire il numero di righe interessate dalla stored procedure e selezionare la casella di controllo **righe interessate parametro**   accanto al nome del parametro. Se il valore restituito è zero quando viene chiamata l'operazione,  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)   verrà generata un'eccezione OptimisticConcurrencyException.
- Selezionare la casella di controllo **Usa valore originale** accanto a una proprietà che si desidera utilizzare per il controllo della concorrenza. Quando si tenta di eseguire un aggiornamento, il valore della proprietà originariamente letta dal database verrà utilizzato per la scrittura di dati nel database. Se il valore non corrisponde al valore nel database, **OptimisticConcurrencyException**   verrà generata un'eccezione OptimisticConcurrencyException.

## <a name="use-the-model"></a>Usare il modello

Aprire il file **Program.cs** in cui è definito il metodo **Main** . Aggiungere il codice seguente nella funzione Main.

Il codice crea un nuovo oggetto **Person** , quindi aggiorna l'oggetto e infine elimina l'oggetto.

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- Compilare l'applicazione ed eseguirla. Il programma produce l'output seguente *

> [!NOTE]
> PersonID viene generato automaticamente dal server, pertanto è probabile che venga visualizzato un numero diverso *

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

Se si utilizza la versione finale di Visual Studio, è possibile utilizzare IntelliTrace con il debugger per visualizzare le istruzioni SQL che vengono eseguite.

![Eseguire il debug con IntelliTrace](~/ef6/media/intellitrace.png)
