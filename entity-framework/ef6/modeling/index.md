---
title: Creazione di un modello - EF6
description: Creazione di un modello in Entity Framework 6
author: divega
ms.date: 07/05/2018
ms.assetid: 4890228E-CEA1-4595-B8AD-CA81253F8767
uid: ef6/modeling/index
ms.openlocfilehash: 417671d5313bb95bb97d5de85b25b09d687c5c3d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620339"
---
# <a name="creating-a-model"></a>Creazione di un modello

Un modello EF archivia i dettagli relativi al mapping delle classi e delle proprietà delle applicazioni alle tabelle e alle colonne di database. Per creare un modello EF, esistono principalmente due metodi:

- **Tramite Code First**: lo sviluppatore per definisce il modello scrivendo codice. EF genera i modelli e i mapping in fase di runtime in base alle classi di entità e a una configurazione di modelli aggiuntiva specificata dallo sviluppatore.

- **Tramite EF Designer**: lo sviluppatore definisce il modello disegnando caselle e linee in EF Designer. Il modello risultante viene archiviato in formato XML in un file con estensione EDMX. Gli oggetti del dominio dell'applicazione vengono in genere generati automaticamente partendo dal modello concettuale.

## <a name="ef-workflows"></a>Flussi di lavoro di EF

Per definire la destinazione di un database esistente o creare un nuovo database è possibile usare entrambi questi approcci, ottenendo come risultato 4 flussi di lavoro diversi.
È necessario capire qual è l'approccio più adatto alle proprie necessità:  

|                                           | Se si vuole scrivere codice                                                                                                                   | Se si vuole usare una finestra di progettazione                                                                                                                        |
|:------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
| **Creazione di un nuovo database**          | [Usare **Code First** per definire il modello tramite codice e quindi generare un database.](xref:ef6/modeling/code-first/workflows/new-database)           | [Usare **Model First** per definire il modello tramite caselle e linee e quindi generare un database.](xref:ef6/modeling/designer/workflows/model-first)   |
| **Accesso a un database esistente** | [Usare **Code First** per creare un modello basato su codice con mapping a un database esistente.](xref:ef6/modeling/code-first/workflows/existing-database) | [Usare **Database First** per creare un modello basato su caselle e linee con mapping a un database esistente.](xref:ef6/modeling/designer/workflows/database-first) |

### <a name="watch-the-video-what-ef-workflow-should-i-use"></a>Video: Quale flusso di lavoro EF usare?

Questo breve video illustra le differenze tra i flussi di lavoro e spiega come individuare quello più adatto alla proprie esigenze.

**Presentato da**: [Rowan Miller](https://romiller.com/)

![Which Workflow Thumb](../media/whichworkflow-thumb.png) [WMV](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.wmv) | [MP4](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_mp4video_ChoseYourWorkflow.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.zip)

Se dopo aver guardato il video non è ancora chiaro se sia meglio usare Code First o EF Designer, è consigliabile apprendere entrambi questi metodi.

## <a name="a-look-under-the-hood"></a>Uno sguardo dietro le quinte

Indipendentemente dal fatto che si usi Code First o EF Designer, un modello EF ha sempre diversi componenti:

- Gli oggetti del dominio o i tipi di entità dell'applicazione. Questi vengono spesso chiamati livello oggetti

- Un modello concettuale costituito da tipi di entità e relazioni specifici del dominio. Questo modello viene descritto tramite [Entity Data Model](xref:ef6/resources/glossary#entity-data-model). A questo livello si fa spesso riferimento con la lettera "C", per _concettuale_.

- Un modello di archiviazione che rappresenta le tabelle, le colonne e le relazioni definite nel database. A questo livello si fa spesso riferimento con la lettera "S", per _storage_ (archiviazione).  

- Un mapping tra il modello concettuale e lo schema del database. Questo mapping viene spesso chiamato mapping "C-S".

Il motore di mapping di EF sfrutta il mapping "C-S" per trasformare le operazioni sulle entità, ad esempio creazione, lettura, aggiornamento ed eliminazione, in operazioni equivalenti sulle tabelle del database.

Il mapping tra il modello concettuale e gli oggetti dell'applicazione è spesso detto mapping "O-C". Rispetto al mapping "C-S", il mapping "O-C" è implicito e implica una relazione uno-a-uno: le entità, le proprietà e le relazioni definite nel modello concettuale devono corrispondere alle forme e ai tipi degli oggetti .NET. A partire da EF4 e versioni successive, il livello degli oggetti può essere composto da oggetti semplici con proprietà senza alcuna dipendenza da EF. A questi si fa in genere riferimento con l'acronimo POCO (Plain-Old CLR Object). Il mapping dei tipi e delle proprietà di questi viene eseguito in base alle convenzioni di corrispondenza dei nomi. In precedenza, in Entity Framework 3.5 erano presenti restrizioni specifiche per il livello degli oggetti. Le entità, ad esempio, dovevano derivare dalla classe EntityObject e avere attributi EF per implementare il mapping "O-C".
