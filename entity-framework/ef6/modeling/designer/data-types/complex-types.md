---
title: Tipi complessi-EF designer-EF6
description: Tipi complessi-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 87b62c119894486cbb54adfc8d3af6208a6618a8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620598"
---
# <a name="complex-types---ef-designer"></a>Tipi complessi-EF designer
In questo argomento viene illustrato come eseguire il mapping di tipi complessi con il Entity Framework Designer (Entity Designer) e come eseguire una query per le entità che contengono proprietà di tipo complesso.

Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Quando si compila il modello concettuale, è possibile che in Elenco errori vengano visualizzati avvisi riguardanti entità e associazioni non mappate. È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.

## <a name="what-is-a-complex-type"></a>Che cos'è un tipo complesso

I tipi complessi sono proprietà non scalari di tipi di entità che consentono l'organizzazione delle proprietà scalari nelle entità. Analogamente alle entità, i tipi complessi sono costituiti da proprietà scalari o da altre proprietà dei tipi complessi.

Quando si utilizzano oggetti che rappresentano tipi complessi, tenere presente quanto segue:

-   I tipi complessi non dispongono di chiavi e pertanto non possono esistere in modo indipendente. I tipi complessi possono esistere solo come proprietà di tipi di entità o gli altri tipi complessi.
-   I tipi complessi non possono partecipare alle associazioni e non possono contenere proprietà di navigazione.
-   Le proprietà del tipo complesso non possono essere **null**. Si verifica un' **eccezione InvalidOperationException **quando viene chiamato **DbContext. SaveChanges**   e viene rilevato un oggetto complesso null. Le proprietà scalari degli oggetti complessi possono essere **null**.
-   I tipi complessi non possono ereditare da altri tipi complessi.
-   È necessario definire il tipo complesso come una **classe**. 
-   EF rileva le modifiche apportate ai membri in un oggetto di tipo complesso quando viene chiamato **DbContext. DetectChanges** . Entity Framework chiama automaticamente **DetectChanges** quando vengono chiamati i membri seguenti: **DbSet. Find**, **DbSet. local**, **DbSet. Remove**, **DbSet. Add**, **DbSet. Connetti**, **DbContext. SaveChanges**, **DbContext. GetValidationErrors**, **DbContext. entry**, DbChangeTracker **. Entries**.

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>Effettuare il refactoring delle proprietà di un'entità in un nuovo tipo complesso

Se si dispone già di un'entità nel modello concettuale, potrebbe essere necessario effettuare il refactoring di alcune proprietà in una proprietà di tipo complesso.

Nell'area di progettazione selezionare una o più proprietà (escluse le proprietà di navigazione) di un'entità, quindi fare clic con il pulsante destro del mouse e scegliere **refactoring- &gt; Sposta in nuovo tipo complesso**.

![Effettua il refactoring nel nuovo tipo complesso](~/ef6/media/refactor.png)

Un nuovo tipo complesso con le proprietà selezionate viene aggiunto a **browser modello**. Al tipo complesso viene assegnato un nome predefinito.

Una proprietà complessa del tipo appena creato sostituisce le proprietà selezionate. Tutti i mapping delle proprietà vengono mantenuti.

![Effettua il refactoring nel nuovo tipo complesso 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>Crea nuovo tipo complesso

È anche possibile creare un nuovo tipo complesso che non contiene proprietà di un'entità esistente.

Fare clic con il pulsante destro del mouse sulla cartella **tipi complessi** in browser modello, scegliere **tipo complesso AddNew...**. In alternativa, è possibile selezionare la cartella **tipi complessi** e premere il tasto **ins**   sulla tastiera.

![Aggiungi nuovo tipo complesso](~/ef6/media/addnewcomplextype.png)

Un nuovo tipo complesso verrà aggiunto nella cartella con un nome predefinito. È ora possibile aggiungere proprietà al tipo.

## <a name="add-properties-to-a-complex-type"></a>Aggiungere proprietà a un tipo complesso

Le proprietà di un tipo complesso possono essere tipi scalari o tipi complessi esistenti. Tuttavia, le proprietà dei tipi complessi non possono avere riferimenti circolari. Un tipo complesso **OnsiteCourseDetails**, ad esempio,   non può avere una proprietà di tipo complesso **OnsiteCourseDetails**.

È possibile aggiungere una proprietà a un tipo complesso in uno dei modi elencati di seguito.

-   Fare clic con il pulsante destro del mouse su un tipo complesso in browser modello, scegliere **Aggiungi**, scegliere **proprietà scalare**   o **proprietà complessa**, quindi selezionare il tipo di proprietà desiderato. In alternativa, è possibile selezionare un tipo complesso e premere il tasto **ins**   sulla tastiera.  

    ![Aggiungere proprietà a un tipo complesso](~/ef6/media/addpropertiestocomplextype.png)

    Una nuova proprietà verrà aggiunta al tipo complesso con un nome predefinito.

- oppure -

-   Fare clic con il pulsante destro del mouse su una proprietà dell'entità nell'area di **progettazione EF** e scegliere **copia**, quindi fare clic con il pulsante destro del mouse sul tipo complesso in **browser modello** e scegliere **Incolla**.

## <a name="rename-a-complex-type"></a>Rinominare un tipo complesso

Quando si rinomina un tipo complesso, tutti i riferimenti al tipo presenti nell'intero progetto vengono aggiornati.

-   Fare doppio clic su un tipo complesso in **browser modello**.
    Il nome verrà selezionato e sarà in modalità di modifica.

- oppure -

-   Fare clic con il pulsante destro del mouse su un tipo complesso in **browser modello** e scegliere **Rinomina**.

- oppure -

-   Selezionare un tipo complesso nella finestra Browser modello e premere il tasto F2.

- oppure -

-   Fare clic con il pulsante destro del mouse su un tipo complesso in **browser modello** e scegliere **Proprietà**. Modificare il nome nella finestra **Proprietà**   .

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>Aggiungere un tipo complesso esistente a un'entità ed eseguire il mapping delle proprietà alle colonne della tabella

1.  Fare clic con il pulsante destro del mouse su un'entità, scegliere **Aggiungi nuovo**e selezionare **proprietà complessa**.
    Una proprietà del tipo complesso con un nome predefinito verrà aggiunta all'entità. Un tipo predefinito, scelto tra i tipi complessi esistenti, verrà assegnato alla proprietà.
2.  Assegnare il tipo desiderato alla proprietà nella finestra **Proprietà**   .
    Dopo avere aggiunto una proprietà del tipo complesso a un'entità, è necessario eseguire il mapping delle proprietà alle colonne della tabella.
3.  Fare clic con il pulsante destro del mouse su un tipo di entità nell'area di progettazione o in **browser modello**   e selezionare **mapping tabella**.
    I mapping delle tabelle vengono visualizzati nella finestra **Dettagli mapping**   .
4.  Espandere il nodo **mapping a &lt; nome &gt; tabella**   .
    Viene visualizzato un nodo **Mapping colonne**   .
5.  Espandere il nodo **Mapping colonne**   .
    Verrà visualizzato un elenco di tutte le colonne della tabella. Le proprietà predefinite, se presenti, a cui è associato il mapping delle colonne sono elencate sotto l'intestazione **valore/proprietà**   .
6.  Selezionare la colonna di cui si desidera eseguire il mapping, quindi fare clic con il pulsante destro del mouse sul campo **valore/proprietà**corrispondente   .
    Verrà visualizzato un elenco a discesa di tutte le proprietà scalari.
7.  Selezionare la proprietà appropriata.

    ![Tipo complesso mappa](~/ef6/media/mapcomplextype.png)

8.  Ripetere i passaggi 6 e 7 per ogni colonna della tabella.

>[!NOTE]
> Per eliminare un mapping di colonna, selezionare la colonna di cui si desidera eseguire il mapping, quindi fare clic sul campo **valore/proprietà**   . Selezionare quindi **Elimina** dall'elenco a discesa.

## <a name="map-a-function-import-to-a-complex-type"></a>Eseguire il mapping di un'importazione di funzioni a un tipo complesso

Le importazioni di funzioni sono basate sulle stored procedure. Per mappare un'importazione di funzioni a un tipo complesso, le colonne restituite dalla stored procedure corrispondente devono corrispondere alle proprietà del tipo complesso nel numero e devono disporre di tipi di archiviazione che sono compatibili con i tipi di proprietà.

-   Fare doppio clic su una funzione importata di cui si desidera eseguire il mapping a un tipo complesso.

    ![Importazioni di funzioni](~/ef6/media/functionimports.png)

-   Specificare le impostazioni per la nuova importazione di funzioni come segue:
    -   Specificare il stored procedure per cui si sta creando un'importazione di funzioni nel campo **Nome stored procedure**   . Questo campo è un elenco a discesa in cui sono visualizzate tutte le stored procedure contenute nel modello di archiviazione.
    -   Specificare il nome dell'importazione di funzioni nel campo **Nome importazione funzione**   .
    -   Selezionare **complesso**   come tipo restituito e quindi specificare il tipo restituito complesso specifico scegliendo il tipo appropriato dall'elenco a discesa.

        ![Modifica importazione funzione](~/ef6/media/editfunctionimport.png)

-   Fare clic su **OK**.
    L'importazione di funzioni viene creata nel modello concettuale.

### <a name="customize-column-mapping-for-function-import"></a>Personalizzare il mapping delle colonne per l'importazione di funzioni

-   Fare clic con il pulsante destro del mouse sull'importazione di funzioni in browser modello, quindi scegliere **mapping importazione funzioni**.
    Viene visualizzata la finestra **Dettagli mapping**   che mostra il mapping predefinito per l'importazione di funzioni. Le frecce indicano i mapping tra i valori della proprietà e i valori della colonna. Per impostazione predefinita, i nomi della colonna corrispondono ai nomi delle proprietà di tipo complesso. I nomi delle colonne predefiniti vengono visualizzati in grigio.
-   Se necessario, modificare i nomi delle colonne per renderli corrispondenti ai nomi delle colonne restituiti dalla stored procedure che corrisponde all'importazione di funzioni.

## <a name="delete-a-complex-type"></a>Eliminare un tipo complesso

Quando si elimina un tipo complesso, questo viene eliminato dal modello concettuale; vengono inoltre eliminati i mapping di tutte le istanze del tipo. I riferimenti al tipo non vengono tuttavia aggiornati. Se, ad esempio, un'entità ha una proprietà di tipo complesso di tipo Tipocomplesso1 e Tipocomplesso1 viene eliminato in **browser modello**, la proprietà dell'entità corrispondente non viene aggiornata. Il modello non verrà convalidato perché contiene un'entità che fa riferimento a un tipo complesso eliminato. Tramite Entity Designer è possibile aggiornare o eliminare i riferimenti ai tipi complessi eliminati.

-   Fare clic con il pulsante destro del mouse su un tipo complesso in browser modello e scegliere **Elimina**.

- oppure -

-   Selezionare un tipo complesso nella finestra Browser modello e premere il tasto CANC sulla tastiera.

## <a name="query-for-entities-containing-properties-of-complex-type"></a>Query per entità contenenti proprietà di tipo complesso

Nel codice seguente viene illustrato come eseguire una query che restituisce una raccolta di oggetti del tipo di entità che contengono una proprietà di tipo complesso.

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
