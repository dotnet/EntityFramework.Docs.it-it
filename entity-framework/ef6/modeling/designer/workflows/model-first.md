---
title: Model First-EF6
description: Model First in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: b47c5805a63a1ea94baad4fb5eb137b633f6be1d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066031"
---
# <a name="model-first"></a>Model First
Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo Model First con Entity Framework. Model First consente di creare un nuovo modello usando il Entity Framework Designer e quindi di generare uno schema del database dal modello. Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer. Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.

## <a name="watch-the-video"></a>Video
Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo Model First con Entity Framework. Model First consente di creare un nuovo modello usando il Entity Framework Designer e quindi di generare uno schema del database dal modello. Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer. Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.

**Presentato da**: [Rowan Miller](https://romiller.com/)

**Video**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che Visual Studio 2010 o Visual Studio 2012 sia installato.

Se si usa Visual Studio 2010, sarà anche necessario che [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) sia installato.

## <a name="1-create-the-application"></a>1. creare l'applicazione

Per semplificare le operazioni, verrà compilata un'applicazione console di base che usa il Model First per eseguire l'accesso ai dati:

-   Aprire Visual Studio
-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**
-   Immettere **ModelFirstSample** come nome
-   Selezionare **OK**.

## <a name="2-create-model"></a>2. creare un modello

Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.

-   **Progetto- &gt; Aggiungi nuovo elemento...**
-   Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**
-   Immettere **BloggingModel** come nome e fare clic su **OK**. viene avviata la procedura guidata Entity Data Model
-   Selezionare il **modello vuoto** e fare clic su **fine** .

    ![Crea modello vuoto](~/ef6/media/createemptymodel.png)

Il Entity Framework Designer viene aperto con un modello vuoto. A questo punto è possibile iniziare ad aggiungere entità, proprietà e associazioni al modello.

-   Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Proprietà** .
-   Nella finestra Proprietà modificare il **nome del contenitore di entità** in **BloggingContext** 
     *questo è il nome del contesto derivato che verrà generato automaticamente, il contesto rappresenta una sessione con il database, consentendo di eseguire query e salvare i dati*
-   Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Aggiungi nuova &gt; entità...**
-   Immettere **Blog** come nome entità e **BlogId** come nome chiave e fare clic su **OK** .

    ![Aggiungi entità Blog](~/ef6/media/addblogentity.png)

-   Fare clic con il pulsante destro del mouse sulla nuova entità nell'area di progettazione e scegliere **Aggiungi nuova &gt; proprietà scalare**, immettere **nome** come nome della proprietà.
-   Ripetere questo processo per aggiungere una proprietà **URL** .
-   Fare clic con il pulsante destro del mouse sulla proprietà **URL** nell'area di progettazione e selezionare **Proprietà**, nel finestra Proprietà modificare l'impostazione **Nullable** su **true**. in 
     *questo modo è possibile salvare un blog nel database senza assegnargli un URL*
-   Usando le tecniche appena apprese, aggiungere un'entità **post** con una proprietà della chiave **postid**
-   Aggiungere le proprietà scalari del **titolo** e del **contenuto** all'entità **post**

Ora che sono presenti due entità, è possibile aggiungere un'associazione (o relazione) tra di esse.

-   Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Aggiungi nuova &gt; associazione...**
-   Fare in modo che un'estremità della relazione punti al **Blog** con una molteplicità di **uno** e l'altro punto finale a **post** con una molteplicità di **molti**di 
     *questi significa che un Blog ha molti post e un post appartiene a* un Blog
-   Verificare che la casella dell' **entità Aggiungi proprietà di chiave esterna a "post"** sia selezionata e fare clic su **OK** .

    ![Aggiungi associazione MF](~/ef6/media/addassociationmf.png)

È ora disponibile un semplice modello che consente di generare un database da e di usare per leggere e scrivere i dati.

![Modello iniziale](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Passaggi aggiuntivi in Visual Studio 2010

Se si lavora in Visual Studio 2010, è necessario seguire alcuni passaggi aggiuntivi per eseguire l'aggiornamento alla versione più recente di Entity Framework. L'aggiornamento è importante perché consente di accedere a una superficie API migliorata, molto più semplice da usare, nonché le correzioni di bug più recenti.

Prima di tutto, è necessario ottenere la versione più recente di Entity Framework da NuGet.

-   **Progetto- &gt; Gestisci pacchetti NuGet...** 
     *Se non si ha l'opzione **Gestisci pacchetti NuGet...** è necessario installare la [versione più recente di NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) *
-   Selezionare la scheda **online**
-   Selezionare il pacchetto **EntityFramework**
-   Fai clic su **Install** (Installa).

Successivamente, è necessario scambiare il modello per generare il codice che usa l'API DbContext, introdotta nelle versioni successive di Entity Framework.

-   Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e scegliere **Aggiungi elemento di generazione codice...**
-   Selezionare **modelli online** dal menu a sinistra e cercare **DbContext**
-   Selezionare il **Generatore EF 5. x DbContext per C \# **, immettere **BloggingModel** come nome e fare clic su **Aggiungi** .

    ![Modello DbContext](~/ef6/media/dbcontexttemplate.png)

## <a name="3-generating-the-database"></a>3. generazione del database

Dato il modello, Entity Framework possibile calcolare uno schema del database che consentirà di archiviare e recuperare i dati utilizzando il modello.

Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:

-   Se si usa Visual Studio 2010 verrà creato un database di SQL Express.
-   Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .

Procediamo con la generazione del database.

-   Fare clic con il pulsante destro del mouse sull'area di progettazione e selezionare **genera database da modello...**
-   Fare clic su **nuova connessione...** e specificano il database locale o SQL Express, a seconda della versione di Visual Studio in uso, immettere **ModelFirst. blogging** come nome del database.

    ![Connessione al database locale MF](~/ef6/media/localdbconnectionmf.png)

    ![Connessione SQL Express MF](~/ef6/media/sqlexpressconnectionmf.png)

-   Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .
-   Fare clic su **Avanti** . il Entity Framework Designer calcolerà uno script per creare lo schema del database
-   Una volta visualizzato lo script, fare clic su **fine** e lo script verrà aggiunto al progetto e aperto
-   Fare clic con il pulsante destro del mouse sullo script e scegliere **Esegui**. verrà richiesto di specificare il database a cui connettersi, specificare il database locale o SQL Server Express, a seconda della versione di Visual Studio in uso

## <a name="4-reading--writing-data"></a>4. lettura & scrittura di dati

Ora che è disponibile un modello, è possibile usarlo per accedere ai dati. Le classi da usare per accedere ai dati vengono automaticamente generate in base al file EDMX.

*Questa schermata è da Visual Studio 2012. Se si usa Visual Studio 2010, i file BloggingModel.tt e BloggingModel.Context.tt saranno direttamente nel progetto anziché annidati nel file EDMX.*

![Classi generate](~/ef6/media/generatedclasses.png)

Implementare il metodo Main in Program.cs, come illustrato di seguito. Questo codice crea una nuova istanza del contesto e la usa per inserire un nuovo blog. USA quindi una query LINQ per recuperare tutti i Blog dal database ordinati alfabeticamente in base al titolo.

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

È ora possibile eseguire l'applicazione ed eseguirne il test.

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```

## <a name="5-dealing-with-model-changes"></a>5. gestione delle modifiche al modello

A questo punto è possibile apportare alcune modifiche al modello. quando si apportano queste modifiche, è necessario aggiornare anche lo schema del database.

Si inizierà aggiungendo una nuova entità User al modello.

-   Aggiungere un nuovo nome di entità **utente** con **username** come nome della chiave e **stringa** come tipo di proprietà per la chiave

    ![Aggiungi entità utente](~/ef6/media/adduserentity.png)

-   Fare clic con il pulsante destro del mouse sulla proprietà **username** nell'area di progettazione e selezionare **Proprietà**, nel finestra Proprietà modificare l'impostazione **MaxLength** su **50**per 
     *limitare i dati che possono essere archiviati nel nome utente a 50 caratteri*
-   Aggiungere una proprietà scalare **DisplayName** all'entità **User**

A questo punto si dispone di un modello aggiornato e si è pronti per aggiornare il database in modo da includere il nuovo tipo di entità utente.

-   Fare clic con il pulsante destro del mouse sull'area di progettazione e selezionare **genera database da modello...** Entity Framework calcolerà uno script per ricreare uno schema basato sul modello aggiornato.
-   Fare clic su **Fine**
-   È possibile ricevere avvisi relativi alla sovrascrittura dello script DDL esistente e delle parti di mapping e archiviazione del modello, fare clic su **Sì** per entrambi gli avvisi
-   Verrà aperto lo script SQL aggiornato per creare il database  
    *Lo script generato eliminerà tutte le tabelle esistenti e ricreerà lo schema da zero. Questo può funzionare per lo sviluppo locale, ma non è possibile eseguire il push delle modifiche a un database già distribuito. Se è necessario pubblicare le modifiche in un database che è già stato distribuito, sarà necessario modificare lo script o usare uno strumento di confronto dello schema per calcolare uno script di migrazione.*
-   Fare clic con il pulsante destro del mouse sullo script e scegliere **Esegui**. verrà richiesto di specificare il database a cui connettersi, specificare il database locale o SQL Server Express, a seconda della versione di Visual Studio in uso

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato esaminato Model First sviluppo, che ci ha consentito di creare un modello nella finestra di progettazione EF e quindi di generare un database da tale modello. Il modello è stato quindi utilizzato per leggere e scrivere alcuni dati dal database. Infine, il modello è stato aggiornato e quindi ricreato lo schema del database in modo che corrisponda al modello.
