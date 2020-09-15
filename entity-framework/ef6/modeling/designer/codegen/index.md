---
title: Modelli di generazione codice di Designer - Entity Framework 6
description: Modelli di generazione codice di Designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/index
ms.openlocfilehash: 4a8225d0186174ea2e2effbc1241389afcbe3921
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070198"
---
# <a name="designer-code-generation-templates"></a>Modelli di generazione codice di Designer
Quando si crea un modello usando Entity Framework Designer, le classi e il contesto derivato vengono generati automaticamente. Oltre alla generazione codice predefinita, sono anche disponibili diversi modelli che possono essere usati per personalizzare il codice generato. Si tratta di modelli di testo T4, che consentono di personalizzare i modelli, se necessario.

Il codice generato per impostazione predefinita dipende dalla versione di Visual Studio in cui si crea il modello:

-   I modelli creati in Visual Studio 2012 e 2013 generano semplici classi di entità POCO e un contesto derivato dall'elemento DbContext semplificato.
-   I modelli creati in Visual Studio 2010 generano classi di entità derivate da EntityObject e un contesto derivato da ObjectContext.
  > [!NOTE]    
  > È consigliabile passare al modello DbContext Generator dopo aver aggiunto il proprio modello.

Questa pagina illustra i modelli disponibili e le istruzioni per aggiungere un modello al proprio.

## <a name="available-templates"></a>Modelli disponibili

Il team di Entity Framework offre i modelli seguenti:

### <a name="dbcontext-generator"></a>Modello DbContext Generator

Questo modello genera semplici classi di entità POCO e un contesto derivato da DbContext usando Entity Framework 6.
È il modello consigliato a meno che non esista un motivo per usare uno degli altri modelli elencati di seguito.
È anche il modello di generazione codice predefinito se si usano le versioni recenti di Visual Studio (Visual Studio 2013 e versioni successive): quando si crea un nuovo modello, questo modello viene usato per impostazione predefinita e i file T4 (con estensione tt) vengono annidati nel file con estensione edmx.

#### <a name="older-versions-of-visual-studio"></a>Versioni precedenti di Visual Studio
- **Visual Studio 2012:** per ottenere i modelli **EF 6.x DbContextGenerator** è necessario installare la versione più recente di **Entity Framework Tools per Visual Studio**. Vedere la pagina [Get Entity Framework](xref:ef6/fundamentals/install) (Ottenere Entity Framework) per altre informazioni.
- **Visual Studio 2010:** i modelli **EF 6.x DbContextGenerator** non sono disponibili per Visual Studio 2010.

#### <a name="dbcontext-generator-for-ef-5x"></a>Modello DbContext Generator per Entity Framework 5.x

Se si usa una versione precedente del pacchetto NuGet di EntityFramework (con una versione principale della 5), usare il modello **EF 5.x DbContext Generator**.

Se si usa Visual Studio 2013 o 2012 questo modello è già installato.

Se si usa Visual Studio 2010, selezionare la scheda **Online** quando si aggiunge il modello per scaricarlo da Visual Studio Gallery. In alternativa, è possibile installarlo prima direttamente da Visual Studio Gallery. I modelli sono inclusi nelle versioni più recenti di Visual Studio. Le versioni disponibili nella raccolta possono essere quindi installate solo in Visual Studio 2010.

- [EF 5.x DbContext Generator per C#](https://visualstudiogallery.msdn.microsoft.com/da740968-02f9-42a9-9ee4-1a9a06d896a2)
- [EF 5.x DbContext Generator per siti Web C#](https://visualstudiogallery.msdn.microsoft.com/5d01a981-91b8-492c-b42c-c771c3f31e03)
- [EF 5.x DbContext Generator per VB.NET](https://visualstudiogallery.msdn.microsoft.com/875c882d-333e-455a-8dae-5353510527dd?src=featured)
- [EF 5.x DbContext Generator per siti Web VB.NET](https://visualstudiogallery.msdn.microsoft.com/d4d7d4cd-c2d0-43e6-8944-12f6ff8f2614)

#### <a name="dbcontext-generator-for-ef-4x"></a>Modello DbContext Generator per Entity Framework 4.x

Se si usa una versione precedente del pacchetto NuGet di EntityFramework (con una versione principale della 4), usare il modello **EF 4.x DbContext Generator**. Questo modello è disponibile nella scheda **Online** quando si aggiunge il modello. In alternativa, è possibile installarlo prima direttamente da Visual Studio Gallery.

- [EF 4.x DbContext Generator per C#](https://visualstudiogallery.msdn.microsoft.com/7812b04c-db36-4817-8a84-e73c452410a2)
- [EF 4.x DbContext Generator per siti Web C#](https://visualstudiogallery.msdn.microsoft.com/de0e9bc6-e86a-4448-8a2e-a1260a53203e)
- [EF 4.x DbContext Generator per VB.NET](https://visualstudiogallery.msdn.microsoft.com/73679ae5-e358-4e76-a538-c7b5e04ac073)
- [EF 4.x DbContext Generator per siti Web VB.NET](https://visualstudiogallery.msdn.microsoft.com/86f5a660-306e-4831-840c-2e4ee7474a92)

### <a name="entityobject-generator"></a>Modello EntityObject Generator

Questo modello genera classi di entità derivate da EntityObject e un contesto derivato da ObjectContext.

> [!NOTE]
> È consigliabile usare il modello DbContext Generator

Per le nuove applicazioni è consigliabile usare il modello DbContext Generator, che consente l'utilizzo dell'API più semplice di DbContext. Il modello EntityObject Generator continua a essere disponibile e può essere usato per supportare le applicazioni esistenti.

**Visual Studio 2010, 2012 &amp; 2013**

Selezionare la scheda **Online** quando si aggiunge il modello per scaricarlo da Visual Studio Gallery. In alternativa, è possibile installarlo prima direttamente da Visual Studio Gallery.

- [EF 6.x EntityObject Generator per C#](https://visualstudiogallery.msdn.microsoft.com/66612113-549c-4a9e-a14a-f629ceb3f89a)
- [EF 6.x EntityObject Generator per siti Wev C#](https://visualstudiogallery.msdn.microsoft.com/076140f3-6dbe-451f-a0e0-16b6d2bd8996)
- [EF 6.x EntityObject Generator per VB.NET](https://visualstudiogallery.msdn.microsoft.com/ff479d55-2c85-43c5-a4d6-21cd659435ea)
- [EF 6.x EntityObject Generator per siti Web VB.NET](https://visualstudiogallery.msdn.microsoft.com/668e2b92-c142-4da2-8e60-866c6346fc6a)

**Modello EntityObject Generator per Entity Framework 5.x**


Se si usa Visual Studio 2012 o 2013, selezionare la scheda **Online** quando si aggiunge il modello per scaricarlo da Visual Studio Gallery. In alternativa, è possibile installarlo prima direttamente da Visual Studio Gallery. I modelli sono inclusi in Visual Studio 2010. Le versioni disponibili nella raccolta possono essere quindi installate solo in Visual Studio 2012 &amp; 2013.

- [EF 5.x EntityObject Generator per C#](https://visualstudiogallery.msdn.microsoft.com/1da40393-b5ec-404a-a000-6a7e6e911339)
- [EF 5.x EntityObject Generator per siti Web C#](https://visualstudiogallery.msdn.microsoft.com/94b48556-fcf0-4b9b-8615-20f9066ae9ac)
- [EF 5.x EntityObject Generator per VB.NET](https://visualstudiogallery.msdn.microsoft.com/92c0129e-40dc-488c-a836-7e30846dfb30)
- [EF 5.x EntityObject Generator per siti Web VB.NET](https://visualstudiogallery.msdn.microsoft.com/5dd7f75c-8c98-4eb7-b4bc-06f0d0b03b41)

Se si vuole semplicemente generare il codice ObjectContext senza modificare il modello, è possibile [ripristinare la generazione codice di EntityObject](xref:ef6/modeling/designer/codegen/legacy-objectcontext).

Se si usa Visual Studio 2010, questo modello è già installato. Se si crea un nuovo modello in Visual Studio 2010, questo modello viene usato per impostazione predefinita, ma i file con estensione tt non sono inclusi nel progetto. Per personalizzare il modello, è necessario aggiungerlo al progetto.

### <a name="self-tracking-entities-ste-generator"></a>Modello Self-Tracking Entities (STE) Generator

Questo modello genera classi di entità con rilevamento automatico e un contesto derivato da ObjectContext. In un'applicazione Entity Framework il rilevamento delle modifiche nelle entità viene controllato da un contesto. Negli scenari a più livelli il contesto potrebbe non essere disponibile sul livello che modifica le entità. Le entità con rilevamento automatico consentono di tenere traccia delle modifiche in qualsiasi livello. Per altre informazioni, vedere [Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) (Entità con rilevamento automatico).

> [!NOTE]
> Il modello STE (entità con rilevamento automatico) non è consigliato

Non è più consigliabile usare il modello STE nelle nuove applicazioni. È tuttavia ancora disponibile per supportare le applicazioni esistenti. Visitare l'[articolo relativo alle entità disconnesse](xref:ef6/fundamentals/disconnected-entities/index) per informazioni su altre opzioni consigliate per scenari a più livelli.

> [!NOTE]
> Non esiste una versione EF 6.x del modello STE.


> [!NOTE]
> Non esiste una versione Visual Studio 2013 del modello STE.

### <a name="visual-studio-2012"></a>Visual Studio 2012

Se si usa Visual Studio 2012, selezionare la scheda **Online** quando si aggiunge il modello per scaricarlo da Visual Studio Gallery. In alternativa, è possibile installarlo prima direttamente da Visual Studio Gallery. I modelli sono inclusi in Visual Studio 2010. Le versioni disponibili nella raccolta possono essere quindi installate solo in Visual Studio 2012.

- [EF 5.x STE Generator per C#](https://visualstudiogallery.msdn.microsoft.com/a3ac10a5-9365-4096-bb58-d9a1ba71db8f)
- [EF 5.x STE Generator per siti Web C#](https://visualstudiogallery.msdn.microsoft.com/1b55ab82-eeb4-47ba-8d35-3c7c8b5f5a8c)
- [EF 5.x STE Generator per VB.NET](https://visualstudiogallery.msdn.microsoft.com/1ba8c6a3-44e9-4e1f-b21e-596f3168474b)
- [EF 5.x STE Generator per siti Web VB.NET](https://visualstudiogallery.msdn.microsoft.com/a9fd5f0a-9af4-4e32-9c09-0e057072152e)

#### <a name="visual-studio-2010"></a>Visual Studio 2010**

Se si usa Visual Studio 2010, questo modello è già installato.

### <a name="poco-entity-generator"></a>Modello POCO Entity Generator

Questo modello genera classi di entità POCO e un contesto derivato da ObjectContext

> [!NOTE]
> È consigliabile usare il modello DbContext Generator

È consigliabile usare il modello DbContext Generator per generare classi POCO in nuove applicazioni. Questo modello consente l'utilizzo della nuova API di DbContext e genera classi POCO più semplici. Il modello POCO Entity Generator continua a essere disponibile e può essere usato per supportare le applicazioni esistenti.

> [!NOTE]
> Non esiste una versione EF 5.x o EF 6.x del modello STE.

> [!NOTE]
> Non esiste una versione Visual Studio 2013 del modello POCO.

#### <a name="visual-studio-2012-amp-visual-studio-2010"></a>Visual Studio 2012 &amp; Visual Studio 2010

Selezionare la scheda **Online** quando si aggiunge il modello per scaricarlo da Visual Studio Gallery. In alternativa, è possibile installarlo prima direttamente da Visual Studio Gallery.

- [EF 4.x POCO Generator per C#](https://visualstudiogallery.msdn.microsoft.com/23df0450-5677-4926-96cc-173d02752313)
- [EF 4.x POCO Generator per siti Web C#](https://visualstudiogallery.msdn.microsoft.com/fe568da5-aa1a-4178-a2a5-48813c707a7f)
- [EF 4.x POCO Generator per VB.NET](https://visualstudiogallery.msdn.microsoft.com/53ecbded-8936-4299-ab04-1e44e5489752)
- [EF 4.x POCO Generator per siti Web VB.NET](https://visualstudiogallery.msdn.microsoft.com/463c5aca-05ad-4cdb-910b-2e4f83269e34)

### <a name="what-are-the-web-sites-templates"></a>Modelli per siti Web

I modelli per siti Web (ad esempio **EF 5.x DbContext Generator per siti Web C\#** ) sono destinati all'uso in progetti di siti Web creati tramite **File -&gt; Nuovo -&gt; Sito Web...** . Sono diversi rispetto alle applicazioni Web create tramite il percorso **File -&gt; Nuovo -&gt; Progetto...** , in cui si usano i modelli standard. Vengono offerti modelli distinti in quanto il sistema di modelli di elemento in Visual Studio li richiede.

## <a name="using-a-template"></a>Uso di un modello

Per iniziare a usare un modello di generazione codice, fare clic con il pulsante destro del mouse su uno spazio vuoto nell'area di progettazione di Entity Framework Designer e selezionare **Aggiungi elemento di generazione codice... **.

![Aggiungere un elemento di generazione codice](~/ef6/media/add-code-gen-item.png)

Se il modello da usare è già stato installato o è stato incluso in Visual Studio, sarà disponibile nella sezione **Codice** o **Dati** nel menu a sinistra.

![Modello installato](~/ef6/media/installed.png)

Se il modello non è stato ancora installato, selezionare **Online** dal menu a sinistra e cercare il modello necessario.

![Ricerca modello](~/ef6/media/search.png) 

Se si usa Visual Studio 2012, i nuovi file con estensione tt verranno annidati nel file con estensione edmx.*

> [!NOTE]
> Per i modelli creati in Visual Studio 2012, è necessario eliminare i modelli usati per la generazione codice predefinita. In caso contrario, si avranno duplicati di classi e contesto generati. I file predefiniti sono **&lt;nome modello&gt;.tt** e **&lt;nome modello&gt;.context.tt**. 

![Modelli di Visual Studio 2012](~/ef6/media/vs2012-templates.png)

Se si usa Visual Studio 2010, i file con estensione tt vengono aggiunti direttamente al progetto.  

![Modelli di Visual Studio 2010](~/ef6/media/vs2010-templates.png)
