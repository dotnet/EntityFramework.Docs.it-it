---
title: Relazioni-EF designer-EF6
description: Relazioni-EF designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 65960dcfcafc239b5ecd8b396a52dababbb4ab08
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073292"
---
# <a name="relationships---ef-designer"></a>Relazioni-EF designer
> [!NOTE]
> In questa pagina vengono fornite informazioni sulla configurazione delle relazioni nel modello mediante la finestra di progettazione EF. Per informazioni generali sulle relazioni in EF e su come accedere e modificare i dati usando le relazioni, vedere [relazioni & proprietà di navigazione](xref:ef6/fundamentals/relationships).

Le associazioni definiscono le relazioni tra i tipi di entità in un modello. In questo argomento viene illustrato come eseguire il mapping delle associazioni con la Entity Framework Designer (progettazione EF). Nell'immagine seguente vengono illustrate le finestre principali che vengono usate quando si usa la finestra di progettazione EF.

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Quando si compila il modello concettuale, è possibile che in Elenco errori vengano visualizzati avvisi riguardanti entità e associazioni non mappate. È possibile ignorare questi avvisi perché, dopo aver scelto di generare il database dal modello, gli errori verranno rilasciati.

## <a name="associations-overview"></a>Cenni preliminari sulle associazioni

Quando si progetta il modello utilizzando la finestra di progettazione EF, un file con estensione edmx rappresenta il modello. Nel file con estensione edmx, un elemento **Association** definisce una relazione tra due tipi di entità. Un'associazione deve specificare i tipi di entità coinvolti nella relazione e il possibile numero di tipi di entità a ogni entità finale della relazione, che è noto come molteplicità. La molteplicità di un'entità finale di un'associazione può avere un valore pari a uno (1), zero o uno (0.. 1) o molti ( \* ). Queste informazioni vengono specificate in due elementi **end** figlio.

In fase di esecuzione, è possibile accedere alle istanze del tipo di entità in un'entità finale di un'associazione tramite proprietà di navigazione o chiavi esterne (se si sceglie di esporre chiavi esterne nelle entità). Con le chiavi esterne esposte, la relazione tra le entità viene gestita con un elemento **ReferentialConstraint** (un elemento figlio dell'elemento **Association** ). È consigliabile esporre sempre chiavi esterne per le relazioni nelle entità.

> [!NOTE]
> In molti-a-molti ( \* : \* ) non è possibile aggiungere chiavi esterne alle entità. In una \* \* relazione: le informazioni di associazione vengono gestite con un oggetto indipendente.

Per informazioni sugli elementi CSDL (**ReferentialConstraint**, **Association**e così via), vedere la [specifica CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).

## <a name="create-and-delete-associations"></a>Creazione ed eliminazione di associazioni

La creazione di un'associazione con la finestra di progettazione EF aggiorna il contenuto del modello del file con estensione edmx. Dopo aver creato un'associazione, è necessario creare i mapping per l'associazione, descritti più avanti in questo argomento.

> [!NOTE]
> In questa sezione si presuppone che siano già state aggiunte le entità per le quali si desidera creare un'associazione al modello.

### <a name="to-create-an-association"></a>Per creare un'associazione

1.  Fare clic con il pulsante destro del mouse su un'area vuota dell'area di progettazione, scegliere **Aggiungi nuovo**e selezionare **associazione.**
2.  Specificare le impostazioni per l'associazione nella finestra di dialogo **Aggiungi associazione** .

    ![Aggiungi associazione](~/ef6/media/addassociation.png)

    > [!NOTE]
    > È possibile scegliere di non aggiungere proprietà di navigazione o proprietà di chiave esterna alle entità finali dell'associazione deselezionando le caselle di controllo **proprietà di navigazione **e **Aggiungi proprietà di chiave esterna all' &lt; &gt; entità nome tipo di entità **. Se si aggiunge una sola proprietà di navigazione, l'associazione sarà attraversabile in una sola direzione. Se non si aggiungono proprietà di navigazione, è necessario scegliere di aggiungere proprietà di chiave esterna per accedere alle entità finali dell'associazione.
    
3.  Fare clic su **OK**.

### <a name="to-delete-an-association"></a>Per eliminare un'associazione

Per eliminare un'associazione, effettuare una delle operazioni seguenti:

-   Fare clic con il pulsante destro del mouse sull'associazione nell'area di progettazione EF e scegliere **Elimina**.

- oppure -

-   Selezionare una o più associazioni e premere il tasto CANC.

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>Includere proprietà di chiave esterna nelle entità (vincoli referenziali)

È consigliabile esporre sempre chiavi esterne per le relazioni nelle entità. Entity Framework utilizza un vincolo referenziale per verificare che una proprietà funga da chiave esterna per una relazione.

Se durante la creazione di una relazione è stata selezionata la casella di controllo ***Aggiungi proprietà chiave esterna all' &lt; &gt; entità nome tipo di entità*** , questo vincolo referenziale è stato aggiunto.

Quando si usa Entity Framework Designer per aggiungere o modificare un vincolo referenziale, progettazione EF aggiunge o modifica un elemento **ReferentialConstraint**   nel contenuto CSDL del file con estensione edmx.

-   Fare doppio clic sull'associazione che si desidera modificare.
    Verrà visualizzata la finestra di dialogo **vincolo referenziale**   .
-   Dall' **Principal**   elenco a discesa principale selezionare l'entità principale nel vincolo referenziale.
    Le proprietà chiave dell'entità vengono aggiunte all'elenco **chiavi principali**   nella finestra di dialogo.
-   Dall' **Dependent**   elenco a discesa dipendente selezionare l'entità dipendente nel vincolo referenziale.
-   Per ogni chiave principale con una chiave dipendente, selezionare una chiave dipendente corrispondente dagli elenchi a discesa nella colonna **chiave dipendente**   .

    ![Vincolo Ref](~/ef6/media/refconstraint.png)

-   Fare clic su **OK**.

## <a name="create-and-edit-association-mappings"></a>Creare e modificare i mapping di associazione

È possibile specificare il modo in cui un'associazione esegue il mapping al database nella finestra **Dettagli mapping**   di EF designer.

> [!NOTE]
> È possibile eseguire il mapping solo dei dettagli per le associazioni per le quali non è specificato un vincolo referenziale. Se viene specificato un vincolo referenziale, viene inclusa una proprietà di chiave esterna nell'entità ed è possibile utilizzare i dettagli di mapping per l'entità per controllare la colonna a cui viene eseguito il mapping della chiave esterna.

### <a name="create-an-association-mapping"></a>Creazione di un mapping di associazione

-   Fare clic con il pulsante destro del mouse su un'associazione nell'area di progettazione e scegliere **mapping tabella**.
    Viene visualizzato il mapping di associazione nella finestra **Dettagli mapping**   .
-   Fare clic su **Aggiungi tabella o vista**.
    Verrà visualizzato un elenco a discesa che include tutte le tabelle presenti nel modello di archiviazione.
-   Selezionare la tabella alla quale l'associazione verrà mappata.
    La finestra **Dettagli mapping**   Visualizza entrambe le estremità dell'associazione e le proprietà chiave per il tipo di entità a ogni **estremità**.
-   Per ogni proprietà chiave, fare clic sul campo **colonna**   e selezionare la colonna a cui viene mappata la proprietà.

    ![Dettagli mapping 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>Modificare un mapping di associazione

-   Fare clic con il pulsante destro del mouse su un'associazione nell'area di progettazione e scegliere **mapping tabella**.
    Viene visualizzato il mapping di associazione nella finestra **Dettagli mapping**   .
-   Fare clic su **mapping a &lt; nome &gt; tabella**.
    Verrà visualizzato un elenco a discesa che include tutte le tabelle presenti nel modello di archiviazione.
-   Selezionare la tabella alla quale l'associazione verrà mappata.
    La finestra **Dettagli mapping**   Visualizza entrambe le estremità dell'associazione e le proprietà chiave per il tipo di entità a ogni estremità.
-   Per ogni proprietà chiave, fare clic sul campo **colonna**   e selezionare la colonna a cui viene mappata la proprietà.

## <a name="edit-and-delete-navigation-properties"></a>Modificare ed eliminare le proprietà di navigazione

Le proprietà di navigazione sono proprietà di collegamento utilizzate per individuare le entità finali di un'associazione in un modello. È possibile creare le proprietà di navigazione quando si crea un'associazione tra due tipi di entità.

#### <a name="to-edit-navigation-properties"></a>Per modificare le proprietà di navigazione

-   Selezionare una proprietà di navigazione nell'area di progettazione EF.
    Le informazioni sulla proprietà di navigazione vengono visualizzate nella finestra **Proprietà**di Visual Studio   .
-   Modificare le impostazioni delle proprietà nella finestra **Proprietà**   .

#### <a name="to-delete-navigation-properties"></a>Per eliminare le proprietà di navigazione

-   Se le chiavi esterne non sono esposte nei tipi di entità nel modello concettuale, è possibile che l'eliminazione di una proprietà di navigazione renda l'associazione corrispondente attraversabile in un'unica direzione o non attraversabile.
-   Fare clic con il pulsante destro del mouse su una proprietà di navigazione nell'area di progettazione EF e scegliere **Elimina**.
