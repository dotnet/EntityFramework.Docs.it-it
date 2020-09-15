---
title: Database First-EF6
description: Database First in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 412d83b37ed5f3e2b863dca0913746e61ecdef76
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073175"
---
# <a name="database-first"></a>Database First
Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo Database First con Entity Framework. Database First consente di decompilare un modello da un database esistente. Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer. Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.

## <a name="watch-the-video"></a>Video
In questo video viene fornita un'introduzione allo sviluppo Database First tramite Entity Framework. Database First consente di decompilare un modello da un database esistente. Il modello viene archiviato in un file EDMX (estensione edmx) e può essere visualizzato e modificato nella Entity Framework Designer. Le classi con cui si interagisce nell'applicazione vengono generate automaticamente dal file EDMX.

**Presentato da**: [Rowan Miller](https://romiller.com/)

**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che sia installato almeno Visual Studio 2010 o Visual Studio 2012.

Se si usa Visual Studio 2010, sarà anche necessario che [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) sia installato.

 

## <a name="1-create-an-existing-database"></a>1. creare un database esistente

In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.

Il server di database installato con Visual Studio è diverso a seconda della versione di Visual Studio installata:

-   Se si usa Visual Studio 2010 verrà creato un database di SQL Express.
-   Se si usa Visual Studio 2012, si creerà un database del database [locale](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .

 

Procediamo con la generazione del database.

-   Aprire Visual Studio
-   **Visualizza- &gt; Esplora server**
-   Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**
-   Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati

    ![Seleziona origine dati](~/ef6/media/selectdatasource.png)

-   Connettersi al database locale o a SQL Express, a seconda di quale installato e immettere **DatabaseFirst. blogging** come nome del database

    ![Connessione SQL Express DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![Connessione database locale DF](~/ef6/media/localdbconnectiondf.png)

-   Selezionare **OK** . verrà richiesto se si desidera creare un nuovo database, selezionare **Sì** .

    ![Finestra di dialogo Crea database](~/ef6/media/createdatabasedialog.png)

-   Il nuovo database verrà ora visualizzato in Esplora server, fare clic con il pulsante destro del mouse su di esso e scegliere **nuova query** .
-   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);
```

## <a name="2-create-the-application"></a>2. creare l'applicazione

Per semplificare le operazioni, verrà compilata un'applicazione console di base che usa il Database First per eseguire l'accesso ai dati:

-   Aprire Visual Studio
-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**
-   Immettere **DatabaseFirstSample** come nome
-   Selezionare **OK**.

 

## <a name="3-reverse-engineer-model"></a>3. Reverse Engineering Model

Per creare il modello, verrà usato Entity Framework Designer, incluso come parte di Visual Studio.

-   **Progetto- &gt; Aggiungi nuovo elemento...**
-   Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**
-   Immettere **BloggingModel** come nome e fare clic su **OK** .
-   Viene avviata la **procedura guidata Entity Data Model**
-   Selezionare **genera da database** e fare clic su **Avanti** .

    ![Passaggio 1 della procedura guidata](~/ef6/media/wizardstep1.png)

-   Selezionare la connessione al database creato nella prima sezione, immettere **BloggingContext** come nome della stringa di connessione e fare clic su **Avanti** .

    ![Passaggio 2 della procedura guidata](~/ef6/media/wizardstep2.png)

-   Fare clic sulla casella di controllo accanto a' Tables ' per importare tutte le tabelle e fare clic su' Finish '

    ![Passaggio 3 della procedura guidata](~/ef6/media/wizardstep3.png)

 

Una volta completato il processo di Reverse Engineering, il nuovo modello viene aggiunto al progetto e aperto per la visualizzazione nel Entity Framework Designer. Al progetto è stato aggiunto anche un file di App.config con i dettagli della connessione per il database.

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

 

## <a name="4-reading--writing-data"></a>4. lettura & scrittura di dati

Ora che è disponibile un modello, è possibile usarlo per accedere ai dati. Le classi da usare per accedere ai dati vengono automaticamente generate in base al file EDMX.

*Questa schermata è da Visual Studio 2012. Se si usa Visual Studio 2010, i file BloggingModel.tt e BloggingModel.Context.tt saranno direttamente nel progetto anziché annidati nel file EDMX.*

![Classi generate DF](~/ef6/media/generatedclassesdf.png)

 

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
 

## <a name="5-dealing-with-database-changes"></a>5. gestione delle modifiche apportate al database

A questo punto è possibile apportare alcune modifiche allo schema del database. quando si apportano queste modifiche, è necessario aggiornare il modello in modo da riflettere le modifiche.

Il primo passaggio consiste nel apportare alcune modifiche allo schema del database. Verrà aggiunta una tabella users allo schema.

-   Fare clic con il pulsante destro del mouse sul database **DatabaseFirst. blogging** in Esplora server e selezionare **nuova query** .
-   Copiare il codice SQL seguente nella nuova query, quindi fare clic con il pulsante destro del mouse sulla query e scegliere **Esegui** .

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

Ora che lo schema è stato aggiornato, è necessario aggiornare il modello con tali modifiche.

-   Fare clic con il pulsante destro del mouse su un punto vuoto del modello nella finestra di progettazione EF e selezionare "Aggiorna modello da database". verrà avviata l'aggiornamento guidato
-   Nella scheda Aggiungi della procedura guidata di aggiornamento selezionare la casella accanto a tabelle per indicare che si desidera aggiungere nuove tabelle dallo schema.
    *La scheda Aggiorna Mostra tutte le tabelle esistenti nel modello di cui verrà verificata la presenza di modifiche durante l'aggiornamento. Le schede Elimina visualizzano tutte le tabelle che sono state rimosse dallo schema e verranno rimosse anche dal modello come parte dell'aggiornamento. Le informazioni su queste due schede vengono rilevate automaticamente e vengono fornite solo a scopo informativo. non è possibile modificare le impostazioni.*

    ![Aggiornamento guidato](~/ef6/media/refreshwizard.png)

-   Fare clic su fine nell'aggiornamento guidato

 

Il modello è ora aggiornato per includere una nuova entità utente mappata alla tabella users aggiunta al database.

![Modello aggiornato](~/ef6/media/modelupdated.png)

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato esaminato Database First sviluppo, che ci ha consentito di creare un modello nella finestra di progettazione EF basata su un database esistente. Il modello è stato quindi utilizzato per leggere e scrivere alcuni dati dal database. Infine, il modello è stato aggiornato in modo da riflettere le modifiche apportate allo schema del database.
