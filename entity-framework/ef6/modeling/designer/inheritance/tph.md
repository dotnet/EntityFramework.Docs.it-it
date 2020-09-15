---
title: Ereditarietà TPH della finestra di progettazione-EF6
description: Ereditarietà TPH della finestra di progettazione in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073396"
---
# <a name="designer-tph-inheritance"></a>Ereditarietà TPH della finestra di progettazione
Questa procedura dettagliata illustra come implementare l'ereditarietà tabella per gerarchia (TPH) nel modello concettuale con il Entity Framework Designer (EF designer). L'ereditarietà TPH utilizza una tabella di database per gestire i dati per tutti i tipi di entità in una gerarchia di ereditarietà.

In questa procedura dettagliata si eseguirà il mapping della tabella Person a tre tipi di entità: Person (il tipo di base), Student (deriva da Person) e Instructor (deriva da Person). Si creerà un modello concettuale dal database (Database First), quindi si modificherà il modello per implementare l'ereditarietà TPH usando la finestra di progettazione EF.

È possibile eseguire il mapping a un'ereditarietà TPH usando Model First ma è necessario scrivere un flusso di lavoro di generazione del database personalizzato, che è complesso. Il flusso di lavoro verrà quindi assegnato alla proprietà del **flusso di lavoro di generazione del database** nella finestra di progettazione EF. Un'alternativa più semplice consiste nell'usare Code First.

## <a name="other-inheritance-options"></a>Altre opzioni di ereditarietà

Table-for-Type (TPT) è un altro tipo di ereditarietà in cui viene eseguito il mapping delle tabelle separate del database a entità che partecipano all'ereditarietà. Per informazioni su come eseguire il mapping dell'ereditarietà tabella per tipo con EF designer, vedere [EF designer TPT ereditarietà](xref:ef6/modeling/designer/inheritance/tpt).

I modelli di ereditarietà della tabella per tipo concreto (TPC) e di ereditarietà mista sono supportati dal runtime di Entity Framework ma non sono supportati dalla finestra di progettazione EF. Se si vuole usare TPC o l'ereditarietà mista, sono disponibili due opzioni: usare Code First o modificare manualmente il file EDMX. Se si sceglie di utilizzare il file EDMX, la finestra Dettagli mapping verrà inserita in "modalità provvisoria" e non sarà possibile utilizzare la finestra di progettazione per modificare i mapping.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario disporre di:

- Una versione recente di Visual Studio.
- [Database di esempio School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurare il progetto

-   Aprire Visual Studio 2012.
-   Selezionare **file- &gt; nuovo- &gt; progetto**
-   Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
-   Immettere **TPHDBFirstSample**   come nome.
-   Selezionare **OK**.

## <a name="create-a-model"></a>Creare il modello

-   Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.
-   Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
-   Immettere **TPHModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.
-   Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.
-   Fare clic su **nuova connessione**.
    Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.
    La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.
-   Nella finestra di dialogo Scegli oggetti di database, nel nodo tabelle, selezionare la tabella **Person** .
-   Fare clic su **fine**.

Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello. Tutti gli oggetti selezionati nella finestra di dialogo Scegli oggetti di database vengono aggiunti al modello.

Questo è il modo in cui la tabella **Person** Cerca nel database.

![Tabella Person](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>Implementare l'ereditarietà tabella per gerarchia

La tabella **Person** contiene la colonna **Discriminator** , che può avere uno dei due valori seguenti: "Student" e "Instructor". A seconda del valore della tabella **Person** verrà eseguito il mapping all'entità **Student** o all'entità **Instructor** . La tabella **Person** dispone anche di due colonne, **hirete**   e **EnrollmentDate**, che devono **ammettere i valori null** perché una persona non può essere uno studente e un insegnante nello stesso momento, almeno non in questa procedura dettagliata.

### <a name="add-new-entities"></a>Aggiungi nuove entità

-   Aggiungere una nuova entità.
    A tale scopo, fare clic con il pulsante destro del mouse su uno spazio vuoto dell'area di progettazione dell'Entity Framework Designer e scegliere **Aggiungi- &gt; entità**.
-   Digitare **Instructor**   per **nome entità**   e selezionare **Person**nell'   elenco a discesa per il **tipo di base**.
-   Fare clic su **OK**.
-   Aggiungere un'altra nuova entità. Digitare **Student**   per **nome entità**   e selezionare **Person**nell'   elenco a discesa per il **tipo di base**.

Due nuovi tipi di entità sono stati aggiunti all'area di progettazione. Una freccia punta dai nuovi tipi di entità al tipo di entità **Person**   . indica che **Person**   è il tipo base per i nuovi tipi di entità.

-   Fare clic con il pulsante destro del mouse sulla proprietà **assunte**   dell'entità **Person**   . Selezionare **taglia** (oppure usare il tasto CTRL + X).
-   Fare clic con il pulsante destro del mouse sull'entità **Instructor**   e selezionare **Incolla** (oppure utilizzare il tasto CTRL + V).
-   Fare clic con il pulsante destro del mouse sulla proprietà **assunte**   e scegliere **proprietà**.
-   Nella finestra **Proprietà**   impostare la proprietà **Nullable**   su **false**.
-   Fare clic con il pulsante destro del mouse sulla proprietà **EnrollmentDate**   dell'entità **Person**   . Selezionare **taglia** (oppure usare il tasto CTRL + X).
-   Fare clic con il pulsante destro del mouse sull'entità **Student** e selezionare **Incolla (oppure utilizzare il tasto CTRL + V).**
-   Selezionare la proprietà **EnrollmentDate**   e impostare la proprietà **Nullable**   su **false**.
-   Selezionare il **Person**   tipo di entità Person. Nella finestra **Proprietà**   impostare la relativa proprietà **abstract**   su **true**.
-   Elimina la proprietà **Discriminator** da **Person**. Il motivo per cui deve essere eliminato è illustrato nella sezione seguente.

### <a name="map-the-entities"></a>Eseguire il mapping delle entità

-   Fare clic con il pulsante destro del mouse sull' **insegnante** e scegliere **mapping tabella.**
    L'entità Instructor è selezionata nella finestra Dettagli mapping.
-   Fare clic su ** &lt; Aggiungi tabella o &gt; vista**   nella finestra **Dettagli mapping**   .
    Il campo ** &lt; Aggiungi tabella o vista &gt; **   diventa un elenco a discesa di tabelle o viste a cui è possibile eseguire il mapping dell'entità selezionata.
-   Selezionare **Person**   dall'elenco a discesa.
-   La finestra **Dettagli mapping**   viene aggiornata con i mapping di colonna predefiniti e con un'opzione per l'aggiunta di una condizione.
-   Fare clic su ** &lt; Aggiungi una &gt; condizione**.
    Il campo ** &lt; Aggiungi una &gt; condizione**   diventa un elenco a discesa di colonne per le quali è possibile impostare le condizioni.
-   Selezionare **discriminatore**   dall'elenco a discesa.
-   Nella colonna **operatore**   della finestra **Dettagli mapping**   selezionare = nell'elenco a discesa.
-   Nella colonna **valore/proprietà** digitare **Instructor**. Il risultato finale dovrebbe essere simile al seguente:

    ![Dettagli mapping](~/ef6/media/mappingdetails2.png)

-   Ripetere questi passaggi per il **Student**   tipo di entità Student, ma rendere la condizione uguale al valore **Student** .  
    *Il motivo per cui si voleva rimuovere la proprietà **Discriminator** è che non è possibile eseguire il mapping di una colonna di tabella più di una volta. Questa colonna verrà usata per il mapping condizionale, pertanto non può essere usata anche per il mapping delle proprietà. L'unico modo in cui può essere usato per entrambi, se una condizione usa un oggetto **è null**   o **non è**un   confronto null.*

A questo punto l'ereditarietà tabella per gerarchia risulta implementata.

![TPH finale](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>Usare il modello

Aprire il file **Program.cs** in cui è definito il metodo **Main** . Incollare il codice seguente nella funzione **Main** . Il codice esegue tre query. La prima query riporta tutti gli oggetti **Person** . La seconda query usa il metodo **OfType** per restituire gli oggetti **Instructor** . La terza query usa il metodo **OfType** per restituire gli oggetti **Student** .

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
