---
title: Ereditarietà TPT della finestra di progettazione-EF6
description: Ereditarietà TPT della finestra di progettazione in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066174"
---
# <a name="designer-tpt-inheritance"></a>Ereditarietà TPT della finestra di progettazione
Questa procedura dettagliata illustra come implementare l'ereditarietà tabella per tipo (TPT) nel modello usando il Entity Framework Designer (EF designer). L'ereditarietà tabella per tipo prevede l'utilizzo di una tabella separata nel database per la gestione dei dati relativi alle proprietà non ereditate e alle proprietà chiave di ogni tipo della gerarchia di ereditarietà.

In questa procedura dettagliata verrà mappato il **corso** (tipo di base), **OnlineCourse** (deriva dal corso) e **OnsiteCourse**   (deriva dal **corso**) entità alle tabelle con gli stessi nomi. Verrà creato un modello dal database e quindi verrà modificato il modello per implementare l'ereditarietà TPT.

È anche possibile iniziare con il Model First e quindi generare il database dal modello. Per impostazione predefinita, in Entity Framework Designer viene utilizzata la strategia TPT, pertanto viene eseguito il mapping di qualsiasi ereditarietà nel modello a tabelle separate.

## <a name="other-inheritance-options"></a>Altre opzioni di ereditarietà

Tabella per gerarchia (TPH) è un altro tipo di ereditarietà in cui una tabella di database viene utilizzata per gestire i dati per tutti i tipi di entità in una gerarchia di ereditarietà.Per informazioni su come eseguire il mapping dell'ereditarietà tabella per gerarchia con la Entity Designer, vedere l' [ereditarietà TPH](xref:ef6/modeling/designer/inheritance/tph)di Entity Framework. 

Si noti che i modelli di ereditarietà della tabella per tipo concreto (TPC) e di ereditarietà mista sono supportati dal runtime di Entity Framework ma non sono supportati dalla finestra di progettazione EF. Se si vuole usare TPC o l'ereditarietà mista, sono disponibili due opzioni: usare Code First o modificare manualmente il file EDMX. Se si sceglie di utilizzare il file EDMX, la finestra Dettagli mapping verrà inserita in "modalità provvisoria" e non sarà possibile utilizzare la finestra di progettazione per modificare i mapping.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario disporre di:

- Una versione recente di Visual Studio.
- [Database di esempio School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurare il progetto

-   Aprire Visual Studio 2012.
-   Selezionare **file- &gt; nuovo- &gt; progetto**
-   Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
-   Immettere **TPTDBFirstSample**   come nome.
-   Selezionare **OK**.

## <a name="create-a-model"></a>Creare il modello

-   Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Aggiungi- &gt; nuovo elemento**.
-   Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
-   Immettere **TPTModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.
-   Nella finestra di dialogo Scegli contenuto Model selezionare**   genera da database**, quindi fare clic su **Avanti**.
-   Fare clic su **nuova connessione**.
    Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.
    La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.
-   Nella finestra di dialogo Scegli oggetti di database, nel nodo tabelle, selezionare le tabelle **Department**, **Course, OnlineCourse e OnsiteCourse** .
-   Fare clic su **fine**.

Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello. Tutti gli oggetti selezionati nella finestra di dialogo Scegli oggetti di database vengono aggiunti al modello.

## <a name="implement-table-per-type-inheritance"></a>Implementare l'ereditarietà tabella per tipo

-   Nell'area di progettazione fare clic con il pulsante destro del mouse sul tipo di entità **OnlineCourse** e scegliere **Proprietà**.
-   Nella finestra **Proprietà** impostare la proprietà tipo di base su **Course**.
-   Fare clic con il pulsante destro del mouse sul tipo di entità **OnsiteCourse** e scegliere **Proprietà**.
-   Nella finestra **Proprietà** impostare la proprietà tipo di base su **Course**.
-   Fare clic con il pulsante destro del mouse sull'associazione (la riga) tra i tipi di entità **OnlineCourse** e **Course** .
    Selezionare **Elimina dal modello**.
-   Fare clic con il pulsante destro del mouse sull'associazione tra i tipi di entità **OnsiteCourse** e **Course** .
    Selezionare **Elimina dal modello**.

A questo punto si eliminerà la proprietà **CourseID** da **OnlineCourse** e **OnsiteCourse** , perché queste classi ereditano **CourseID** dal tipo di base **Course** .

-   Fare clic con il pulsante destro del mouse sulla proprietà **CourseID** del tipo di entità **OnlineCourse** e quindi scegliere **Elimina dal modello**.
-   Fare clic con il pulsante destro del mouse sulla proprietà **CourseID** del tipo di entità **OnsiteCourse** e quindi scegliere **Elimina dal modello** .
-   A questo punto l'ereditarietà tabella per tipo risulta implementata.

![Tabella per tipo](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>Usare il modello

Aprire il file **Program.cs** in cui è definito il metodo **Main** . Incollare il codice seguente nella funzione **Main** . Il codice esegue tre query. La prima query riporta tutti i **corsi** correlati al reparto specificato. La seconda query usa il metodo **OfType** per restituire **OnlineCourses** correlati al reparto specificato. La terza query restituisce **OnsiteCourses**.

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
