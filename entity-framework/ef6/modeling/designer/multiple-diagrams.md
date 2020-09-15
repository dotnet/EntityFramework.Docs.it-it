---
title: Più diagrammi per modello-EF6
description: Più diagrammi per modello in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: d934a1b709c93fe8e6b57d40dd874b564885b19f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073331"
---
# <a name="multiple-diagrams-per-model"></a>Più diagrammi per modello
> [!NOTE]
> **EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

In questo video e pagina viene illustrato come suddividere un modello in più diagrammi utilizzando il Entity Framework Designer (Entity Framework Designer). Questa funzionalità può essere utilizzata quando il modello diventa troppo grande per la visualizzazione o la modifica.

Nelle versioni precedenti di EF designer era possibile avere un solo diagramma per il file EDMX. A partire da Visual Studio 2012, è possibile usare la finestra di progettazione EF per suddividere il file EDMX in più diagrammi.

## <a name="watch-the-video"></a>Video
In questo video viene illustrato come suddividere un modello in più diagrammi utilizzando il Entity Framework Designer (Entity Framework Designer). Questa funzionalità può essere utilizzata quando il modello diventa troppo grande per la visualizzazione o la modifica.

**Presentato da**: Julia Kornich

**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>Panoramica di EF designer

Quando si crea un modello usando la procedura guidata Entity Data Model della finestra di progettazione di EF, viene creato un file con estensione edmx che viene aggiunto alla soluzione. Questo file definisce la forma delle entità e il modo in cui eseguono il mapping al database.

EF designer è costituito dai componenti seguenti:

-   Area di progettazione visiva per la modifica del modello. È possibile creare, modificare o eliminare entità e associazioni.
-   Finestra del **browser modello**   che fornisce visualizzazioni ad albero del modello.Le entità e le relative associazioni si trovano nella cartella * \[ ModelName \] * . Le tabelle e i vincoli del database si trovano in * \[ ModelName \] *. Cartella dell'archivio.
-   Finestra **Dettagli mapping** per la visualizzazione e la modifica dei mapping. È possibile mappare i tipi di entità o le associazioni a stored procedure, colonne e tabelle di database. 

La finestra superficie di progettazione visiva viene aperta automaticamente al termine della procedura guidata Entity Data Model. Se il browser modello non è visibile, fare clic con il pulsante destro del mouse sull'area di progettazione principale e scegliere **browser modello**.

La schermata seguente mostra un file con estensione edmx aperto nella finestra di progettazione EF. Lo screenshot mostra l'area di progettazione visiva (a sinistra) e la finestra **browser modello**   (a destra).

![Progettazione EF 2](~/ef6/media/efdesigner2.png)

Per annullare un'operazione eseguita in EF designer, fare clic su CTRL + Z.

## <a name="working-with-diagrams"></a>Uso dei diagrammi

Per impostazione predefinita, la finestra di progettazione EF crea un diagramma denominato Diagram1. Se si dispone di un diagramma con un numero elevato di entità e associazioni, è preferibile suddividerle logicamente. A partire da Visual Studio 2012, è possibile visualizzare il modello concettuale in più diagrammi.   

Quando si aggiungono nuovi diagrammi, questi vengono visualizzati nella cartella diagrammi della finestra browser modello. Per rinominare un diagramma: selezionare il diagramma nella finestra browser modello, fare clic una volta sul nome e digitare il nuovo nome. È anche possibile fare clic con il pulsante destro del mouse sul nome del diagramma e scegliere **Rinomina**.

Il nome del diagramma viene visualizzato accanto al nome del file con estensione edmx nell'editor di Visual Studio. Ad esempio Model1. edmx \[ Diagram1 \] .

![Nome diagramma](~/ef6/media/diagramname.png)

Il contenuto dei diagrammi (forma e colore delle entità e delle associazioni) viene archiviato nel file con estensione edmx. Diagram. Per visualizzare questo file, selezionare Esplora soluzioni e aprire il file con estensione edmx. 

![File di diagramma](~/ef6/media/diagramfiles.png)

Non è consigliabile modificare il file con estensione edmx. Diagram manualmente, il contenuto di questo file potrebbe essere sovrascritto da EF designer.
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>Suddivisione di entità e associazioni in un nuovo diagramma

È possibile selezionare entità nel diagramma esistente. tenere premuto MAIUSC per selezionare più entità. Fare clic con il pulsante destro del mouse e selezionare **passa a nuovo diagramma**. Il nuovo diagramma viene creato e le entità selezionate e le relative associazioni vengono spostate nel diagramma.

In alternativa, è possibile fare clic con il pulsante destro del mouse sulla cartella diagrammi in browser modello e selezionare **Aggiungi nuovo diagramma.** È quindi possibile trascinare e rilasciare entità dalla cartella tipi di entità in browser modello nell'area di progettazione.

È anche possibile tagliare o copiare entità (usando i tasti CTRL + X o CTRL + C) da un diagramma e incollare (usando il tasto CTRL + V) nell'altro. Se il diagramma in cui si incolla un'entità contiene già un'entità con lo stesso nome, verrà creata una nuova entità che verrà aggiunta al modello.Ad esempio: Diagram2 contiene l'entità Department. Incollare quindi un altro reparto in Diagram2. L'entità Department1 viene creata e aggiunta al modello concettuale.   

Per includere le entità correlate in un diagramma, fare clic con il pulsante destro del mouse sull'entità e scegliere **Includi correlati**. In questo modo verrà fatta una copia delle entità e delle associazioni correlate nel diagramma specificato.

## <a name="changing-the-color-of-entities"></a>Modifica del colore delle entità

Oltre a suddividere un modello in più diagrammi, è anche possibile modificare i colori delle entità.

Per modificare il colore, selezionare un'entità (o più entità) nell'area di progettazione. Quindi, fare clic con il pulsante destro del mouse e selezionare **Proprietà**. Nella Finestra Proprietà selezionare la proprietà **colore riempimento** . Specificare il colore usando un nome di colore valido (ad esempio, rosso) o un valore RGB valido, ad esempio 255, 128, 128. 

![Cambia colore](~/ef6/media/color.png)

## <a name="summary"></a>Riepilogo

In questo argomento è stato illustrato come suddividere un modello in più diagrammi e come specificare un colore diverso per un'entità usando il Entity Framework Designer. 
