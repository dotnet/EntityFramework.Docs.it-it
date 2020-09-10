---
title: Code First a un database esistente-EF6
description: Code First a un database esistente in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 7cb58495ea287c59f46e804882cdc089d0f09e7b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616930"
---
# <a name="code-first-to-an-existing-database"></a>Code First a un database esistente
Questo video e la procedura dettagliata forniscono un'introduzione allo sviluppo di Code First destinati a un database esistente. Code First consente di definire il modello utilizzando \# le classi C o VB.NET. Facoltativamente, è possibile eseguire una configurazione aggiuntiva usando gli attributi delle classi e delle proprietà oppure usando un'API Fluent.

## <a name="watch-the-video"></a>Video
Questo video è [ora disponibile su Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che **Visual Studio 2012** o **Visual Studio 2013** sia installato.

Sarà inoltre necessario installare la versione **6,1** (o successiva) del **Entity Framework Tools per Visual Studio** . Per informazioni sull'installazione della versione più recente del Entity Framework Tools, vedere [Get Entity Framework](xref:ef6/fundamentals/install) .

## <a name="1-create-an-existing-database"></a>1. creare un database esistente

In genere, quando si fa riferimento a un database esistente, questo verrà già creato, ma per questa procedura dettagliata è necessario creare un database per accedere a.

Procediamo con la generazione del database.

-   Aprire Visual Studio.
-   **Visualizza- &gt; Esplora server**
-   Fare clic con il pulsante destro del mouse su **connessioni dati- &gt; Aggiungi connessione...**
-   Se non si è connessi a un database da **Esplora server** prima di selezionare **Microsoft SQL Server** come origine dati

    ![Seleziona origine dati](~/ef6/media/selectdatasource.png)

-   Connettersi all'istanza del database locale e immettere **Blog** come nome del database

    ![Connessione al database locale](~/ef6/media/localdbconnection.png)

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

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2. creare l'applicazione

Per semplificare le operazioni, verrà creata un'applicazione console di base che usa Code First per eseguire l'accesso ai dati:

-   Aprire Visual Studio.
-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**
-   Immettere **CodeFirstExistingDatabaseSample** come nome
-   Selezionare **OK**.

 

## <a name="3-reverse-engineer-model"></a>3. Reverse Engineering Model

Si utilizzerà il Entity Framework Tools per Visual Studio per consentire la generazione di codice iniziale per eseguire il mapping al database. Questi strumenti generano solo codice che può essere digitato manualmente, se si preferisce.

-   **Progetto- &gt; Aggiungi nuovo elemento...**
-   Selezionare **dati** dal menu a sinistra e quindi **ADO.NET Entity Data Model**
-   Immettere **BloggingContext** come nome e fare clic su **OK** .
-   Viene avviata la **procedura guidata Entity Data Model**
-   Selezionare **Code First da database** e fare clic su **Avanti** .

    ![Procedura guidata One CFE](~/ef6/media/wizardonecfe.png)

-   Selezionare la connessione al database creato nella prima sezione e fare clic su **Avanti** .

    ![Procedura guidata due](~/ef6/media/wizardtwocfe.png)

-   Fare clic sulla casella di controllo accanto a **tabelle** per importare tutte le tabelle e fare clic su **fine** .

    ![Procedura guidata tre CFE](~/ef6/media/wizardthreecfe.png)

Una volta completato il processo di Reverse Engineering, al progetto verrà aggiunto un certo numero di elementi. si osserverà ora cosa è stato aggiunto.

### <a name="configuration-file"></a>File di configurazione

Al progetto è stato aggiunto un file di App.config. il file contiene la stringa di connessione al database esistente.

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*Si noteranno anche altre impostazioni nel file di configurazione. si tratta di impostazioni EF predefinite che indicano a Code First dove creare i database. Poiché viene mappato a un database esistente, queste impostazioni verranno ignorate nell'applicazione.*

### <a name="derived-context"></a>Contesto derivato

È stata aggiunta una classe **BloggingContext** al progetto. Il contesto rappresenta una sessione con il database, consentendo di eseguire query e salvare i dati.
Il contesto espone una **DbSet &lt; TEntity &gt; ** per ogni tipo nel modello. Si noterà anche che il costruttore predefinito chiama un costruttore di base usando la sintassi **Name =** . Questo indica Code First che la stringa di connessione da usare per questo contesto deve essere caricata dal file di configurazione.

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

*Quando si utilizza una stringa di connessione nel file di configurazione, è necessario utilizzare sempre la sintassi **Name =** . Ciò garantisce che se la stringa di connessione non è presente, Entity Framework genererà un'eccezione anziché creare un nuovo database per convenzione.*

### <a name="model-classes"></a>Classi modello

Infine, al progetto sono state aggiunte anche una classe **post** e **Blog** . Queste sono le classi di dominio che costituiscono il modello. Verranno visualizzate le annotazioni dei dati applicate alle classi per specificare la configurazione in cui le convenzioni Code First non si allineano alla struttura del database esistente. Si noterà, ad esempio, l'annotazione **StringLength** in **Blog.Name** e **Blog. URL** poiché la lunghezza massima è **200** nel database (il Code First impostazione predefinita consiste nell'utilizzare la lunghezza massima supportata dal provider di database- **nvarchar (max)** in SQL Server).

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4. lettura & scrittura di dati

Ora che è disponibile un modello, è possibile usarlo per accedere ai dati. Implementare il metodo **Main** in **Program.cs** , come illustrato di seguito. Questo codice crea una nuova istanza del contesto e la usa per inserire un nuovo **Blog**. USA quindi una query LINQ per recuperare tutti i **Blog** dal database ordinati alfabeticamente in base al **titolo**.

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

È ora possibile eseguire l'applicazione e testarla.

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>Che cosa succede se il database cambia?

La procedura guidata Code First per database è progettata per generare un set di punti di partenza per le classi che è possibile modificare e modificare. Se lo schema del database viene modificato, è possibile modificare manualmente le classi o eseguire un altro decodificatore per sovrascrivere le classi.

## <a name="using-code-first-migrations-to-an-existing-database"></a>Utilizzo di Migrazioni Code First a un database esistente

Se si desidera utilizzare Migrazioni Code First con un database esistente, vedere [migrazioni Code First a un database esistente](xref:ef6/modeling/code-first/migrations/existing-database).

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato esaminato Code First sviluppo usando un database esistente. È stato usato il Entity Framework Tools per Visual Studio per decompilare un set di classi di cui è stato eseguito il mapping al database e può essere usato per archiviare e recuperare i dati.
