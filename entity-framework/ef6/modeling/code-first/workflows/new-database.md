---
title: Code First a un nuovo database-EF6
description: Code First a un nuovo database in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 96d76162383149c045178954196e1294331918a8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070367"
---
# <a name="code-first-to-a-new-database"></a>Code First to a New Database (Code First per un nuovo database)
Questo video e una procedura dettagliata forniscono un'introduzione allo sviluppo di Code First destinati a un nuovo database. Questo scenario include la destinazione di un database che non esiste e Code First creerà oppure un database vuoto a cui Code First aggiungeranno nuove tabelle. Code First consente di definire il modello utilizzando \# le classi C o VB.NET. Facoltativamente, è possibile eseguire una configurazione aggiuntiva usando gli attributi delle classi e delle proprietà o usando un'API Fluent.

## <a name="watch-the-video"></a>Video
In questo video viene fornita un'introduzione allo sviluppo di Code First destinati a un nuovo database. Questo scenario include la destinazione di un database che non esiste e Code First creerà oppure un database vuoto a cui Code First aggiungeranno nuove tabelle. Code First consente di definire il modello utilizzando le classi C# o VB.Net. Facoltativamente, è possibile eseguire una configurazione aggiuntiva usando gli attributi delle classi e delle proprietà o usando un'API Fluent.

**Presentato da**: [Rowan Miller](https://romiller.com/)

**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che sia installato almeno Visual Studio 2010 o Visual Studio 2012.

Se si usa Visual Studio 2010, sarà anche necessario che [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) sia installato.

## <a name="1-create-the-application"></a>1. creare l'applicazione

Per semplificare le operazioni, verrà compilata un'applicazione console di base che usa Code First per eseguire l'accesso ai dati.

-   Aprire Visual Studio
-   **File- &gt; nuovo- &gt; progetto...**
-   Selezionare **Windows** nel menu a sinistra e nell' **applicazione console**
-   Immettere **CodeFirstNewDatabaseSample** come nome
-   Selezionare **OK**.

## <a name="2-create-the-model"></a>2. creare il modello

Definire un modello molto semplice con le classi. Sono state semplicemente definite nel file Program.cs, ma in un'applicazione reale è possibile suddividere le classi in file separati e potenzialmente un progetto separato.

Sotto la definizione della classe Program in Program.cs aggiungere le due classi seguenti.

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

Si noterà che vengono apportate le due proprietà di navigazione (Blog. post e post. Blog) virtuali. In questo modo viene abilitata la funzionalità di caricamento lazy di Entity Framework. Il caricamento lazy significa che il contenuto di queste proprietà verrà caricato automaticamente dal database quando si tenta di accedervi.

## <a name="3-create-a-context"></a>3. creare un contesto

A questo punto è possibile definire un contesto derivato, che rappresenta una sessione con il database, consentendo di eseguire query e salvare i dati. Si definisce un contesto che deriva da System. Data. Entity. DbContext ed espone una TEntity DbSet tipizzata &lt; &gt; per ogni classe del modello.

A questo punto si inizia a usare i tipi del Entity Framework quindi è necessario aggiungere il pacchetto NuGet EntityFramework.

-   **Progetto- &gt; Gestisci pacchetti NuGet...**
    Nota: se non si ha la **gestione dei pacchetti NuGet...** opzione è necessario installare la [versione più recente di NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   Selezionare la scheda **online**
-   Selezionare il pacchetto **EntityFramework**
-   Fai clic su **Install** (Installa).

Aggiungere un'istruzione using per System. Data. Entity nella parte superiore di Program.cs.

``` csharp
using System.Data.Entity;
```

Sotto la classe post in Program.cs aggiungere il seguente contesto derivato.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

Di seguito è riportato un elenco completo degli elementi che Program.cs dovrebbe contenere.

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

Questo è tutto il codice necessario per avviare l'archiviazione e il recupero dei dati. Ovviamente, c'è un po' di più dietro le quinte, che verranno esaminate in un certo momento, ma prima vediamo in azione.

## <a name="4-reading--writing-data"></a>4. lettura & scrittura di dati

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
### <a name="wheres-my-data"></a>Dove sono i dati?

Per convenzione DbContext ha creato automaticamente un database.

-   Se è disponibile un'istanza di SQL Express locale (installata per impostazione predefinita con Visual Studio 2010), Code First ha creato il database nell'istanza
-   Se SQL Express non è disponibile, Code First tenterà di usare il [database locale](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installato per impostazione predefinita con Visual Studio 2012)
-   Il database viene denominato dopo il nome completo del contesto derivato, in questo caso **CodeFirstNewDatabaseSample. BloggingContext**

Queste sono solo le convenzioni predefinite e sono disponibili vari modi per modificare il database usato da Code First. per informazioni su **come DbContext individua l'argomento relativo al modello e alla connessione al database** , sono disponibili altre informazioni.
È possibile connettersi a questo database usando Esplora server in Visual Studio

-   **Visualizza- &gt; Esplora server**
-   Fare clic con il pulsante destro del mouse su **connessioni dati** e scegliere **Aggiungi connessione...**
-   Se non si è connessi a un database da Esplora server prima di selezionare Microsoft SQL Server come origine dati

    ![Seleziona origine dati](~/ef6/media/selectdatasource.png)

-   Connettersi a un database locale o a SQL Express, a seconda di quale installato

È ora possibile esaminare lo schema che Code First creato.

![Schema iniziale](~/ef6/media/schemainitial.png)

DbContext ha elaborato le classi da includere nel modello esaminando le proprietà DbSet definite. USA quindi il set predefinito di Code First convenzioni per determinare i nomi di tabella e colonna, determinare i tipi di dati, trovare chiavi primarie e così via. Più avanti in questa procedura dettagliata si esaminerà come è possibile eseguire l'override di queste convenzioni.

## <a name="5-dealing-with-model-changes"></a>5. gestione delle modifiche al modello

A questo punto è possibile apportare alcune modifiche al modello. quando si apportano queste modifiche, è necessario aggiornare anche lo schema del database. A tale scopo, verrà usata una funzionalità denominata Migrazioni Code First o le migrazioni per brevità.

Le migrazioni consentono di avere un set ordinato di passaggi che descrivono come eseguire l'aggiornamento (e il downgrade) dello schema del database. Ognuno di questi passaggi, noto come migrazione, contiene codice che descrive le modifiche da applicare. 

Il primo passaggio consiste nell'abilitare Migrazioni Code First per la BloggingContext.

-   **Strumenti- &gt; Gestione pacchetti libreria- &gt; console di gestione pacchetti**
-   Eseguire il comando **Enable-Migrations** nella console di Gestione pacchetti
-   Al progetto è stata aggiunta una nuova cartella migrazioni che contiene due elementi:
    -   **Configuration.cs** : questo file contiene le impostazioni che le migrazioni utilizzeranno per la migrazione di BloggingContext. Non è necessario apportare alcuna modifica per questa procedura dettagliata, ma qui è possibile specificare i dati di inizializzazione, registrare i provider per altri database, modificare lo spazio dei nomi in cui vengono generate migrazioni e così via.
    -   ** &lt; timestamp &gt; \_ InitialCreate.cs** : si tratta della prima migrazione, che rappresenta le modifiche che sono già state applicate al database per configurarne l'uso come database vuoto, che include le tabelle Blog e post. Sebbene si consenta di Code First creare automaticamente queste tabelle, ora che è stato scelto di eseguire la migrazione, le migrazioni sono state convertite in una migrazione. Code First ha registrato anche nel database locale che questa migrazione è già stata applicata. Il timestamp nel nome file viene usato a scopo di ordinamento.

    A questo punto è possibile apportare una modifica al modello, aggiungere una proprietà URL alla classe Blog:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   Eseguire il comando **Add-Migration addurl** nella console di gestione pacchetti.
    Il comando Add-Migration controlla le modifiche apportate dall'ultima migrazione e ponteggi una nuova migrazione con le eventuali modifiche trovate. È possibile assegnare un nome alle migrazioni. in questo caso viene chiamata la migrazione ' AddUrl '.
    Il codice con impalcature dice che è necessario aggiungere una colonna URL, che può conservare dati stringa, al dbo. Tabella Blog. Se necessario, è possibile modificare il codice con impalcature, ma ciò non è necessario in questo caso.

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   Eseguire il comando **Update-database** nella console di gestione pacchetti. Questo comando consente di applicare tutte le migrazioni in sospeso al database. La migrazione di InitialCreate è già stata applicata, quindi le migrazioni applicheranno solo la nuova migrazione di AddUrl.
    Suggerimento: è possibile usare l'opzione **-verbose** quando si chiama Update-database per visualizzare il SQL in esecuzione sul database.

La nuova colonna URL verrà ora aggiunta alla tabella Blogs del database:

![Schema con URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. annotazioni dei dati

Fino a questo momento abbiamo solo consentito a EF di individuare il modello usando le convenzioni predefinite, ma in alcuni casi le nostre classi non seguono le convenzioni ed è necessario essere in grado di eseguire ulteriori operazioni di configurazione. Sono disponibili due opzioni: in questa sezione verranno esaminate le annotazioni dei dati e quindi l'API Fluent nella sezione successiva.

-   Aggiungere una classe utente al modello

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   È anche necessario aggiungere un set al contesto derivato

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   Se si è tentato di aggiungere una migrazione, viene segnalato un errore indicante che l'*utente "EntityType" non ha una chiave definita. Definire la chiave per questo EntityType ".* Poiché EF non ha modo di sapere che il nome utente deve essere la chiave primaria per l'utente.
-   Ora verranno usate le annotazioni dei dati, quindi è necessario aggiungere un'istruzione using nella parte superiore di Program.cs

```csharp
using System.ComponentModel.DataAnnotations;
```

-   Annotare ora la proprietà UserName per identificare che si tratta della chiave primaria

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   Usare il comando **Add-Migration adduser** per eseguire il patibolo di una migrazione per applicare queste modifiche al database
-   Eseguire il comando **Update-database** per applicare la nuova migrazione al database

La nuova tabella verrà ora aggiunta al database:

![Schema con utenti](~/ef6/media/schemawithusers.png)

L'elenco completo delle annotazioni supportate da EF è il seguente:

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. Fluent API

Nella sezione precedente è stato esaminato l'uso delle annotazioni dei dati per integrare o ignorare gli elementi rilevati per convenzione. L'altro modo per configurare il modello è tramite l'API Code First Fluent.

La maggior parte della configurazione del modello può essere eseguita usando le annotazioni di dati semplici. L'API Fluent è un modo più avanzato per specificare la configurazione del modello che copre tutto ciò che può fare le annotazioni di dati, oltre a una configurazione più avanzata non possibile con le annotazioni dei dati. Le annotazioni dei dati e l'API Fluent possono essere usate insieme.

Per accedere all'API Fluent, eseguire l'override del metodo OnModelCreating in DbContext. Supponiamo di voler rinominare la colonna in cui è archiviato User. DisplayName per visualizzare il \_ nome.

-   Eseguire l'override del metodo OnModelCreating in BloggingContext con il codice seguente

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   Usare il comando **Add-Migration ChangeDisplayName** per eseguire il patibolo di una migrazione per applicare queste modifiche al database.
-   Eseguire il comando **Update-database** per applicare la nuova migrazione al database.

La colonna DisplayName è stata rinominata in \_ nome visualizzato:

![Schema con nome visualizzato rinominato](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato esaminato Code First sviluppo tramite un nuovo database. È stato definito un modello usando le classi, quindi è stato usato il modello per creare un database e archiviare e recuperare i dati. Una volta creato il database, è stato usato Migrazioni Code First per modificare lo schema man mano che il modello è stato sviluppato. È stato anche illustrato come configurare un modello usando le annotazioni dei dati e l'API Fluent.
