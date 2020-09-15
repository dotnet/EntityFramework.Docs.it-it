---
title: Ripristino di ObjectContext in Entity Framework Designer-EF6
description: Ripristino di ObjectContext in Entity Framework Designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/legacy-objectcontext
ms.openlocfilehash: 9ceb8ef43e4df083fe3cc5e63862ba2eb338f659
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069964"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Ripristino di ObjectContext in Entity Framework Designer
Con la versione precedente di Entity Framework un modello creato con la finestra di progettazione EF genererebbe un contesto derivato da ObjectContext e classi di entità derivate da EntityObject.

A partire da Entity Framework 4.1 è stato consigliato lo scambio a un modello di generazione del codice che genera un contesto che deriva da classi di entità DbContext e POCO.

In Visual Studio 2012 si ottiene il codice DbContext generato per impostazione predefinita per tutti i nuovi modelli creati con la finestra di progettazione EF. I modelli esistenti continueranno a generare codice basato su ObjectContext, a meno che non si decida di eseguire lo scambio nel generatore di codice basato su DbContext.

## <a name="reverting-back-to-objectcontext-code-generation"></a>Ripristino della generazione del codice ObjectContext

### <a name="1-disable-dbcontext-code-generation"></a>1. disabilitare la generazione del codice DbContext

La generazione delle classi DbContext e POCO derivate viene gestita da due file con estensione TT nel progetto. Se si espande il file con estensione edmx in Esplora soluzioni, questi file vengono visualizzati. Eliminare entrambi i file dal progetto.

![File gen del codice](~/ef6/media/codegenfiles.png)

Se si usa VB.NET, è necessario selezionare il pulsante **Mostra tutti i file** per visualizzare i file annidati.

![Mostra tutti i file](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. abilitare nuovamente la generazione del codice ObjectContext

Aprire il modello nella finestra di progettazione EF, fare clic con il pulsante destro del mouse su una sezione vuota dell'area di progettazione e scegliere **Proprietà**.

Nella Finestra Proprietà modificare la **strategia di generazione del codice** da **None** a **default**.

![Strategia di generazione del codice](~/ef6/media/codegenstrategy.png)
