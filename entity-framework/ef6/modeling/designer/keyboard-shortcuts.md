---
title: Tasti di scelta rapida Entity Framework Designer-EF6
description: Tasti di scelta rapida Entity Framework Designer Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 90589419a52a198747eadee740775e2761bc757c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064887"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Tasti di scelta rapida Entity Framework Designer
Questa pagina fornisce un elenco di shorcuts di tastiera disponibili nelle varie schermate del Entity Framework Tools per Visual Studio.

## <a name="adonet-entity-data-model-wizard"></a>Procedura guidata di Entity Data Model ADO.NET

### <a name="step-one-choose-model-contents"></a>Passaggio uno: scegliere il contenuto del modello

![Procedura guidata uno](~/ef6/media/wizardone.png)

| Tasto di scelta rapida  | Azione                                                     | Note                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | Passa alla schermata successiva                                        | Non disponibile per tutte le selezioni di contenuto del modello. |
| **Alt + f** | Terminare la procedura guidata                                              | Non disponibile per tutte le selezioni di contenuto del modello. |
| **Alt + w** | Spostare lo stato attivo su "cosa deve contenere il modello?" riquadro. |                                                     |

### <a name="step-two-choose-your-connection"></a>Passaggio 2: scegliere la connessione

![Procedura guidata due](~/ef6/media/wizardtwo.png)

| Tasto di scelta rapida  | Azione                                                     | Note                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | Passa alla schermata successiva                                        |                                                         |
| **Alt + p** | Passa alla schermata precedente                                    |                                                         |
| **Alt + w** | Spostare lo stato attivo su "cosa deve contenere il modello?" riquadro. |                                                         |
| **Alt + c** | Aprire la finestra "Proprietà connessione"                    | Consente la definizione di una nuova connessione al database. |
| **ALT + e** | Escludere dati sensibili dalla stringa di connessione          |                                                         |
| **Alt + i** | Includere dati sensibili nella stringa di connessione            |                                                         |
| **Alt + s** | Impostare l'opzione "Salva impostazioni di connessione in App.Config" |                                                         |

### <a name="step-three-choose-your-version"></a>Passaggio 3: scegliere la versione

![Procedura guidata tre](~/ef6/media/wizardthree.png)

| Tasto di scelta rapida  | Azione                                             | Note                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | Passa alla schermata successiva                                |                                                                                       |
| **Alt + p** | Passa alla schermata precedente                            |                                                                                       |
| **Alt + w** | Passa lo stato attivo alla selezione della versione Entity Framework | Consente di specificare una versione diversa di Entity Framework da utilizzare nel progetto. |

### <a name="step-four-choose-your-database-objects-and-settings"></a>Passaggio 4: scegliere le impostazioni e gli oggetti di database

![Procedura guidata quattro](~/ef6/media/wizardfour.png)

| Tasto di scelta rapida  | Azione                                                                                    | Note                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | Terminare la procedura guidata                                                                             |                                                                     |
| **Alt + p** | Passa alla schermata precedente                                                                   |                                                                     |
| **Alt + w** | Passa lo stato attivo al riquadro di selezione degli oggetti di database                                            | Consente di specificare gli oggetti di database da decodificare.    |
| **Alt + s** | Consente di abilitare o disabilitare l'opzione "plurali o singolari-nomi degli oggetti generati"                       |                                                                     |
| **Alt + k** | Impostare l'opzione "Includi colonne di chiavi esterne nel modello"                              | Non disponibile per tutte le selezioni di contenuto del modello.                 |
| **Alt + i** | Consente di impostare l'opzione "Importa stored procedure e funzioni selezionate nel modello di entità" | Non disponibile per tutte le selezioni di contenuto del modello.                 |
| **Alt + m** | Passa lo stato attivo al campo di testo "spazio dei nomi del modello"                                        | Non disponibile per tutte le selezioni di contenuto del modello.                 |
| **Spazio** | Attiva/Nascondi selezione sull'elemento                                                               | Se l'elemento ha elementi figlio, verranno attivato anche tutti gli elementi figlio. |
| **Sinistra**  | Comprimi albero figlio                                                                       |                                                                     |
| **Ok** | Espandi albero figlio                                                                         |                                                                     |
| **Attivo**    | Passa all'elemento precedente nell'albero                                                      |                                                                     |
| **Giù**  | Passa all'elemento successivo nell'albero                                                          |                                                                     |

## <a name="ef-designer-surface"></a>Area di progettazione EF

![Area di progettazione](~/ef6/media/designersurface.png)

| Tasto di scelta rapida                                                                                | Azione                      | Note                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **BARRA SPAZIATRICE/INVIO**                                                                         | Imposta/Nascondi selezione            | Consente di attivare o disabilitare la selezione nell'oggetto con lo stato attivo.                                                                                                                                                                                         |
| **ESC**                                                                                 | Annulla selezione            | Consente di annullare la selezione corrente.                                                                                                                                                                                                      |
| **CTRL + A**                                                                            | Seleziona tutto                  | Seleziona tutte le forme nell'area di progettazione.                                                                                                                                                                                       |
| **Freccia in su**                                                                            | Sposta su                     | Sposta l'entità selezionata verso l'alto di un incremento della griglia. <br/> Se in un elenco, si sposta sul sottocampo di pari livello precedente.                                                                                                                            |
| **Freccia GIÙ**                                                                          | Sposta giù                   | Sposta l'entità selezionata verso il basso di un incremento della griglia. <br/> Se in un elenco, si sposta sul sottocampo di pari livello successivo.                                                                                                                              |
| **Freccia sinistra**                                                                          | Sposta a sinistra                   | Sposta l'entità selezionata a sinistra di un incremento della griglia. <br/> Se in un elenco, si sposta sul sottocampo di pari livello precedente.                                                                                                                          |
| **Freccia destra**                                                                         | Sposta a destra                  | Sposta l'entità selezionata a destra di un incremento della griglia. <br/> Se in un elenco, si sposta sul sottocampo di pari livello successivo.                                                                                                                             |
| **MAIUSC + freccia sinistra**                                                                  | Forma dimensione a sinistra             | Riduce la larghezza dell'entità selezionata di un incremento della griglia.                                                                                                                                                                     |
| **MAIUSC + freccia destra**                                                                 | Forma dimensioni a destra            | Aumenta la larghezza dell'entità selezionata di un incremento della griglia.                                                                                                                                                                   |
| **Home**                                                                                | Primo peer                  | Sposta lo stato attivo e la selezione sul primo oggetto nell'area di progettazione allo stesso livello peer.                                                                                                                                         |
| **Fine**                                                                                 | Ultimo peer                   | Sposta lo stato attivo e la selezione sull'ultimo oggetto nell'area di progettazione allo stesso livello peer.                                                                                                                                          |
| **CTRL + Home**                                                                         | Primo peer (stato attivo)          | Uguale al primo peer, ma sposta lo stato attivo anziché spostare lo stato attivo e la selezione.                                                                                                                                                          |
| **CTRL + Fine**                                                                          | Ultimo peer (stato attivo)           | Uguale all'ultimo peer, ma sposta lo stato attivo anziché spostare lo stato attivo e la selezione.                                                                                                                                                           |
| **Scheda**                                                                                 | Peer successivo                   | Sposta lo stato attivo e la selezione nell'oggetto successivo nell'area di progettazione allo stesso livello peer.                                                                                                                                          |
| **MAIUSC + TAB**                                                                           | Peer precedente               | Sposta lo stato attivo e la selezione nell'oggetto precedente nell'area di progettazione allo stesso livello peer.                                                                                                                                      |
| **ALT + CTRL + TAB**                                                                        | Peer successivo (stato attivo)           | Uguale al peer successivo, ma sposta lo stato attivo anziché spostare lo stato attivo e la selezione.                                                                                                                                                           |
| **ALT + CTRL + MAIUSC + TAB**                                                                  | Peer precedente (stato attivo)       | Uguale al peer precedente, ma sposta lo stato attivo anziché spostare lo stato attivo e la selezione.                                                                                                                                                       |
| **&lt;**                                                                                | Ascend                      | Passa all'oggetto successivo nell'area di progettazione di un livello superiore nella gerarchia. Se non sono presenti forme al di sopra di questa forma nella gerarchia, ovvero l'oggetto viene inserito direttamente nell'area di progettazione, il diagramma viene selezionato. |
| **&gt;**                                                                                | Discendono                     | Passa all'oggetto contenuto successivo nell'area di progettazione di un livello al di sotto di quello presente nella gerarchia. Se non è presente alcun oggetto contenuto, questo è un no-op.                                                                              |
| **CTRL + &lt;**                                                                         | Ascend (stato attivo)              | Uguale al comando Ascend, ma sposta lo stato attivo senza selezione.                                                                                                                                                                          |
| **CTRL + &gt;**                                                                         | Descendi (stato attivo)             | Uguale al comando scend, ma sposta lo stato attivo senza selezione.                                                                                                                                                                         |
| **MAIUSC + fine**                                                                         | Segui alla connessione         | Da un'entità passa a un'entità a cui è connessa questa entità.                                                                                                                                                               |
| **CANC**                                                                                 | Delete                      | Eliminare un oggetto o un connettore dal diagramma.                                                                                                                                                                                     |
| **In**                                                                                 | Insert                      | Aggiunge una nuova proprietà a un'entità quando si seleziona l'intestazione di raggruppamento "proprietà scalari" o una proprietà stessa.                                                                                                           |
| **PG su**                                                                               | Scorri diagramma su           | Scorre l'area di progettazione verso l'alto, in incrementi pari al 75% dell'altezza dell'area di progettazione attualmente visibile.                                                                                                                    |
| **Pag giù**                                                                             | Scorri diagramma verso il basso         | Scorre l'area di progettazione verso il basso.                                                                                                                                                                                                    |
| **MAIUSC + PG in basso**                                                                     | Scorri diagramma a destra        | Scorre l'area di progettazione a destra.                                                                                                                                                                                            |
| **MAIUSC + PG su**                                                                       | Diagramma di scorrimento a sinistra         | Scorre l'area di progettazione verso sinistra.                                                                                                                                                                                             |
| **F2**                                                                                  | Attivare la modalità di modifica             | Tasto di scelta rapida standard per l'immissione della modalità di modifica per un controllo di testo.                                                                                                                                                               |
| **MAIUSC+F10**                                                                         | Visualizza menu di scelta rapida       | Tasto di scelta rapida standard per visualizzare il menu di scelta rapida di un elemento selezionato.                                                                                                                                                          |
| **CTRL + MAIUSC + clic con il pulsante sinistro del mouse**  <br/> **CTRL + MAIUSC + MouseWheel in poi**  | Zoom semantico            | Esegue lo zoom avanti sull'area della visualizzazione diagramma sotto il puntatore del mouse.                                                                                                                                                                 |
| **CTRL + MAIUSC + clic con il pulsante destro del mouse** <br/> **CTRL + MAIUSC + MouseWheel indietro** | Zoom avanti semantico           | Esegue lo zoom indietro dall'area della visualizzazione diagramma sotto il puntatore del mouse. Il diagramma viene ricentrato quando si esegue lo zoom indietro troppo lungo per il centro del diagramma corrente.                                                                          |
| **CTRL + MAIUSC +' +'** <br/> **CTRL + rotellina successiva**                        | Zoom avanti                     | Esegue lo zoom avanti al centro della visualizzazione del diagramma.                                                                                                                                                                                         |
| **CTRL + MAIUSC +'-'** <br/> **CTRL + MouseWheel indietro**                       | Zoom indietro                    | Esegue lo zoom indietro dall'area su cui è stato fatto clic nella visualizzazione diagramma. Il diagramma viene ricentrato quando si esegue lo zoom indietro troppo lungo per il centro del diagramma corrente.                                                                                            |
| **CTRL + MAIUSC + disegnato un rettangolo con il pulsante sinistro del mouse**                  | Area zoom                   | Esegue lo zoom in centrato sull'area selezionata. Quando si tiene premuto il tasto CTRL + MAIUSC, si noterà che il cursore cambia in una lente di ingrandimento, che consente di definire l'area in cui eseguire lo zoom.                        |
| **Tasto del menu di scelta rapida + m'**                                                              | Apri finestra Dettagli mapping | Apre la finestra Dettagli mapping per modificare i mapping per l'entità selezionata                                                                                                                                                               |

## <a name="mapping-details-window"></a>Finestra Dettagli Mapping

![Collegamenti dettagli mapping](~/ef6/media/mappingdetailsshortcuts.png)

| Tasto di scelta rapida                  | Azione         | Note                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **Scheda**                   | Cambia contesto | Passa tra l'area della finestra principale e la barra degli strumenti a sinistra                                                                     |
| **Tasti di direzione**            | Navigazione     | Spostare le righe verso l'alto e verso il basso o a destra e a sinistra tra le colonne nell'area della finestra principale. Spostarsi tra i pulsanti sulla barra degli strumenti a sinistra. |
| **Entrare** <br/> **Spazio** | Select         | Seleziona un pulsante sulla barra degli strumenti a sinistra.                                                                                          |
| **ALT + freccia giù**      | Apri elenco      | Elenco a discesa se è selezionata una cella con un elenco a discesa.                                                                     |
| **Entrare**                 | Selezione elenco    | Seleziona un elemento in un elenco a discesa.                                                                                               |
| **ESC**                   | Chiudi elenco     | Chiude un elenco a discesa.                                                                                                              |

## <a name="visual-studio-navigation"></a>Navigazione in Visual Studio

Entity Framework fornisce anche una serie di azioni che possono disporre di tasti di scelta rapida personalizzati mappati (per impostazione predefinita non viene eseguito il mapping dei collegamenti). Per creare questi tasti di scelta rapida personalizzati, scegliere Opzioni dal menu strumenti.In ambiente scegliere tastiera.Scorrere l'elenco verso il basso fino a quando non è possibile selezionare il comando desiderato, immettere il collegamento nella casella di testo "premere i tasti di scelta rapida" e fare clic su Assegna. I possibili collegamenti sono i seguenti:

| Tasto di scelta rapida                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Add. ComplexProperty. ComplexTypes**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddCodeGenerationItem**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddFunctionImport**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. AddEnumType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Association**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ComplexProperty**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ComplexType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. Entity**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. FunctionImport**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ereditarietà**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. NavigationProperty**               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddNew. ScalarProperty**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddNewDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddtoDiagram**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Close**                                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Collapse**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ConverttoEnum**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. CollapseAll**                     |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. pulsante ExpandAll**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. ExportasImage**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Diagram. LayoutDiagram**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Edit**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. EntityKey**                               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Expand**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. FunctionImportMapping**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GenerateDatabasefromModel**               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. GoToDefinition**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Grid. ShowGrid**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Grid. SnaptoGrid**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.IncludeRelated**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MappingDetails**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ModelBrowser non**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveDiagramstoSeparateFile**              |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down**                     |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Down5**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToBottom**                 |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.ToTop**                    |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveProperties.Up5**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MovetonewDiagram**                        |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Open**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. refactor. MovetoNewComplexType**           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.Refactor.Rename**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.RemovefromDiagram**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Rename**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ScalarPropertyFormat. DisplayName**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. BaseType**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. Entity**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. Property**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Select. sottotipo**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. SelectAll**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAssociation**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinModelBrowser**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.StoredProcedureMapping**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. TableMapping**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.UpdateModelfromDatabase**                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Validate**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 10**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 100**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 125**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 150**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 200**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 25**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 300**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 33**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 400**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 50**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 66**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. 75**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. Custom**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. ZoomIn**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. zoom indietro**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. zoom. ZoomtoFit**                          |
| **View.EntityDataModelBrowser**                                                                |
| **View.EntityDataModelMappingDetails**                                                         |
