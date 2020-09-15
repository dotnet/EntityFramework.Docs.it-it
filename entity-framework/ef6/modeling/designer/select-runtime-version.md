---
title: Selezione di Entity Framework versione runtime per i modelli di progettazione EF-EF6
description: Selezione di Entity Framework versione runtime per i modelli di progettazione EF in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 2025ca5e46c7d9a38ba596d57c023a2c1b224129
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073266"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>Selezione di Entity Framework versione runtime per i modelli di progettazione EF
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

A partire da EF6, la schermata seguente è stata aggiunta alla finestra di progettazione EF per consentire di selezionare la versione del runtime di destinazione durante la creazione di un modello. La schermata verrà visualizzata quando la versione più recente di Entity Framework non è ancora installata nel progetto. Se la versione più recente è già installata, verrà usata per impostazione predefinita.

![Seleziona versione runtime](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a>Destinazione di EF6. x

È possibile scegliere EF6 dalla schermata ' scegliere la versione ' per aggiungere il runtime di EF6 al progetto. Una volta aggiunto EF6, questa schermata verrà arrestata nel progetto corrente.

EF6 verrà disabilitato se è già installata una versione precedente di EF (poiché non è possibile fare riferimento a più versioni del runtime dallo stesso progetto). Se l'opzione EF6 non è abilitata, seguire questa procedura per aggiornare il progetto a EF6:

1.  Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet...**
2.  Seleziona **aggiornamenti**
3.  Selezionare **EntityFramework** (assicurarsi che sia in corso l'aggiornamento alla versione desiderata)
4.  Fare clic su **Aggiorna**

 

## <a name="targeting-ef5x"></a>Destinazione di EF5. x

È possibile scegliere EF5 dalla schermata ' scegliere la versione ' per aggiungere il runtime di EF5 al progetto. Una volta aggiunto EF5, viene visualizzata la schermata con l'opzione EF6 disabilitata.

Se è già installata una versione EF4. x del runtime, si noterà che la versione di EF è elencata nella schermata invece che in EF5. In questa situazione è possibile eseguire l'aggiornamento a EF5 attenendosi alla procedura seguente:

1.  Selezione **strumenti- &gt; Gestione pacchetti libreria- &gt; console di gestione pacchetti**
2.  Eseguire **Install-Package EntityFramework-Version 5.0.0**

 

## <a name="targeting-ef4x"></a>Destinazione di EF4. x

È possibile installare il runtime EF4. x nel progetto attenendosi alla procedura seguente:

1.  Selezione **strumenti- &gt; Gestione pacchetti libreria- &gt; console di gestione pacchetti**
2.  Eseguire **Install-Package EntityFramework-Version 4.3.0**
