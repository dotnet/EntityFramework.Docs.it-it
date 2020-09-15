---
title: Migrazioni Code First - EF6
description: Migrazioni Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: b2200a91b087cf6f2dff0803b710e2518193af40
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616960"
---
# <a name="code-first-migrations"></a>Migrazioni Code First
Migrazioni Code First rappresenta il metodo più adatto per far evolvere lo schema del database dell'applicazione quando si usa il flusso di lavoro Code First. Le migrazioni offrono un set di strumenti che consentono di:

1. Creare un database iniziale che può essere usato con il modello di Entity Framework
2. Generare migrazioni per tenere traccia delle modifiche apportate al modello di Entity Framework
2. Mantenere aggiornato il database con le modifiche

La seguente procedura dettagliata offre una panoramica di Migrazioni Code First in Entity Framework. È possibile eseguire l'intera procedura dettagliata o passare all'argomento desiderato. Vengono trattati i seguenti argomenti:

## <a name="building-an-initial-model--database"></a>Creazione del modello iniziale e del database

Per iniziare a usare le migrazioni sono necessari un progetto e un modello Code First. In questa procedura dettagliata verranno usati i modelli tradizionali **Blog** e **Post**.

-   Creare una nuova applicazione console **MigrationsDemo**
-   Aggiungere la versione più recente del pacchetto NuGet **EntityFramework** al progetto
    -   **Strumenti –&gt; Library Package Manager (Gestione pacchetti librerie) –&gt; Console di Gestione pacchetti**
    -   Eseguire il comando **Install-Package EntityFramework**
-   Aggiungere un file **Model.cs** con il codice riportato di seguito. Questo codice definisce una singola classe **Blog** che rappresenta il modello di dominio e una classe **BlogContext** che rappresenta il contesto Code First di Entity Framework

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   È ora possibile usare il modello per eseguire l'accesso ai dati. Aggiornare il file **Program.cs** con il codice riportato di seguito.

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   Eseguire l'applicazione. Si noti che viene creato un database **MigrationsCodeDemo.BlogContext**.

    ![Database Local DB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Abilitare le migrazioni

È ora possibile apportare ulteriori modifiche al modello.

-   Introdurre una proprietà Url nella classe Blog.

``` csharp
    public string Url { get; set; }
```

Se si esegue di nuovo l'applicazione, viene generata un'eccezione InvalidOperationException con il messaggio *The model backing the 'BlogContext' context has changed since the database was created. Valutare la possibilità di usare migrazioni Code First per aggiornare il database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*

Come suggerisce l'eccezione, è ora possibile iniziare a usare Migrazioni Code First. Il primo passaggio consiste nell'abilitare le migrazioni per il contesto.

-   Eseguire il comando **Enable-Migrations** nella console di Gestione pacchetti

    Il comando aggiunge una cartella **Migrazioni** al progetto. La nuova cartella contiene due file:

-   **La classe Configuration.** Questa classe consente di configurare il comportamento di Migrazioni per il contesto. In questa procedura dettagliata verrà usata solo la configurazione predefinita.
    *Poiché è presente un solo contesto Code First nel progetto, Enable-Migrations ha specificato automaticamente il tipo di contesto a cui si applica questa configurazione.*
-   **Una migrazione InitialCreate**. Questa migrazione viene generata poiché è già stato creato automaticamente un database da Code First prima dell'abilitazione delle migrazioni. Il codice in questa migrazione di scaffolding rappresenta gli oggetti che sono già stati creati nel database. In questo caso, si tratta della tabella **Blog** con le colonne **BlogId** e **Name**. Il nome del file include un timestamp che facilita l'ordinamento.
    *Se il database non fosse già stato creato, questa migrazione InitialCreate non sarebbe stata aggiunta al progetto. La prima volta che viene chiamato Add-Migration, viene invece eseguito lo scaffolding del codice per la creazione delle tabelle in una nuova migrazione.*

### <a name="multiple-models-targeting-the-same-database"></a>Più modelli con lo stesso database di destinazione

Quando si usano versioni precedenti a EF6, è possibile usare solo un modello Code First per generare o gestire lo schema di un database. Quando è disponibile una sola tabella **\_\_MigrationsHistory** per ogni database, non è possibile identificare l'appartenenza delle voci ai modelli.

A partire da EF6, la classe **Configuration** include una proprietà **ContextKey**. La proprietà svolge la funzione di identificatore univoco per ogni modello Code First. Una colonna corrispondente nella tabella **\_\_MigrationsHistory** consente a voci di più modelli di condividere la tabella. Per impostazione predefinita, questa proprietà è impostata sul nome completo del contesto.

## <a name="generating--running-migrations"></a>Generazione ed esecuzione di migrazioni

Migrazioni Code First include due comandi principali.

-   **Add-Migration** esegue lo scaffolding della migrazione successiva in base alle modifiche apportate al modello dalla creazione dell'ultima migrazione
-   **Update-Database** applica tutte le migrazioni in sospeso al database

Viene ora eseguito lo scaffolding di una migrazione per la nuova proprietà Url aggiunta. Il comando **Add-Migration** consente di assegnare un nome alle migrazioni, ovvero **AddBlogUrl**.

-   Eseguire il comando **Add-Migration AddBlogUrl** nella console di Gestione pacchetti
-   Nella cartella **Migrazioni** viene inserita la nuova migrazione **AddBlogUrl**. Il nome file della migrazione è preceduto da un timestamp per facilitare l'ordinamento

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
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

È ora possibile apportare modifiche o aggiunte alla migrazione. Usare **Update-Database** per applicare la migrazione al database.

-   Eseguire il comando **Update-Database** nella console di Gestione pacchetti
-   Migrazioni Code First esegue un confronto delle migrazioni della cartella **Migrazioni** con le migrazioni applicate al database. Viene individuata la migrazione **AddBlogUrl** da applicare e viene eseguita la migrazione.

Il database **MigrationsDemo.BlogContext** è ora aggiornato in modo da includere la colonna **Url** nella tabella **Blogs**.

## <a name="customizing-migrations"></a>Personalizzazione delle migrazioni

Nei passaggi precedenti è stata creata ed eseguita una migrazione senza apportare alcuna modifica. Viene ora descritto come modificare il codice generato per impostazione predefinita.

-   È ora possibile apportare alcune modifiche al modello: aggiungere una nuova proprietà **Rating** alla classe **Blog**

``` csharp
    public int Rating { get; set; }
```

-   Aggiungere anche una nuova classe **Post**

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   Si procede anche all'aggiunta delle raccolta **Posts** alla classe **Blog** per creare l'altro elemento della relazione tra **Blog** e **Post**

``` csharp
    public virtual List<Post> Posts { get; set; }
```

Viene usato il comando **Add-Migration** per consentire a Migrazioni Code First di eseguire automaticamente lo scaffolding nella migrazione. La migrazione viene denominata **AddPostClass**.

-   Eseguire il comando **Add-Migration AddPostClass** nella console di Gestione pacchetti.

Sebbene Migrazioni Code First abbia eseguito lo scaffolding di queste modifiche, è possibile modificare ulteriori elementi:

1.  Aggiungere innanzitutto un indice univoco alla colonna **Posts.Title** (aggiunta alle righe 22 e 29 del codice seguente).
2.  Viene anche aggiunta una colonna **Blogs.Rating** non nullable. Se la tabella include dati esistenti, viene assegnato alla tabella il valore predefinito CLR del tipo di dati per la nuova colonna (poiché il tipo di dati di Rating è integer, il valore è **0**). Si supponga tuttavia di voler specificare un valore predefinito **3** in modo che le righe esistenti della tabella **Blogs** abbiano inizio con una classificazione ragionevole.
    (È possibile visualizzare il valore predefinito specificato nella riga 24 del codice riportato di seguito)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

La migrazione modificata è ora pronta. Usare **Update-Database** per aggiornare il database. Specificare questa volta il flag **-Verbose** per poter visualizzare il codice SQL eseguito da Migrazioni Code First.

-   Eseguire il comando **Update-Database –Verbose** nella console di Gestione pacchetti.

## <a name="data-motion--custom-sql"></a>Spostamento di dati/SQL personalizzato

Le operazioni sulle migrazioni eseguite finora non comportano alcuna modifica o spostamento dei dati. Di seguito viene descritto il caso in cui è necessario spostare alcuni dati. Sebbene non sia disponibile alcun supporto nativo per lo spostamento dei dati, è possibile eseguire alcuni comandi SQL arbitrari in qualsiasi posizione all'interno dello script.

-   Aggiungere una proprietà **Post.Abstract** al modello. In seguito verrà prepopolato **Abstract** per i post esistenti usando un testo della parte iniziale della colonna **Content**.

``` csharp
    public string Abstract { get; set; }
```

Viene usato il comando **Add-Migration** per consentire a Migrazioni Code First di eseguire automaticamente lo scaffolding nella migrazione.

-   Eseguire il comando **Add-Migration AddPostAbstract** nella console di Gestione pacchetti.
-   Sebbene la migrazione generata gestisca le modifiche dello schema, si vuole prepopolare anche la colonna **Abstract** usando i primi 100 caratteri del contenuto di ogni post. Per eseguire questa operazione, passare a SQL ed eseguire un'istruzione **UPDATE** dopo aver aggiunto la colonna.
    (L'aggiunta si trova nella riga 12 del codice riportato di seguito)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

La migrazione modificata è ora pronta. Usare **Update-Database** per aggiornare il database. Specificare il flag **-Verbose** per visualizzare il codice SQL eseguito nel database.

-   Eseguire il comando **Update-Database –Verbose** nella console di Gestione pacchetti.

## <a name="migrate-to-a-specific-version-including-downgrade"></a>Eseguire la migrazione a una versione specifica (incluso il downgrade)

Sebbene fino ad ora sia sempre stato eseguito l'aggiornamento alla migrazione più recente, in alcuni casi può essere necessario eseguire l'aggiornamento o il downgrade a una migrazione specifica.

Si supponga di volere eseguire la migrazione del database allo stato in cui si trovava dopo l'esecuzione della migrazione **AddBlogUrl**. È possibile usare l'opzione **-TargetMigration** per eseguire il downgrade alla migrazione specifica.

-   Eseguire il comando **Update-Database -TargetMigration: AddBlogUrl** nella console di Gestione pacchetti.

Il comando esegue lo script di downgrade per le migrazioni **AddBlogAbstract** e **AddPostClass**.

Se si vuole eseguire il rollback a un database vuoto, è possibile usare il comando **Update-Database –TargetMigration: $InitialDatabase**.

## <a name="getting-a-sql-script"></a>Recupero di uno script SQL

Dopo aver controllato le modifiche nel controllo del codice sorgente, se un altro sviluppatore vuole apportare le modifiche nel proprio computer è sufficiente che esegua la sincronizzazione. Dopo aver ottenuto le nuove migrazioni, sarà sufficiente eseguire il comando Update-Database per applicare le modifiche in locale. Tuttavia, se si vuole eseguire il push delle modifiche in un server di test, ed eventualmente in produzione, può essere necessario uno script SQL da inviare al DBA.

-   Eseguire il comando **Update-Database** e specificare il flag **-Script** in modo che le modifiche vengano scritte in uno script anziché essere applicate. Vengono anche specificate una migrazione di origine e una migrazione di destinazione per cui generare lo script. Si vuole che lo script passi da un database vuoto (**$InitialDatabase**) alla versione più recente (migrazione **AddPostAbstract**).
    *Se non si specifica una migrazione di destinazione, Migrazioni userà la migrazione di più recente come destinazione. Se non si specifica una migrazione di origine, Migrazioni userà lo stato corrente del database.*
-   Eseguire il comando **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** nella console di Gestione pacchetti

Migrazioni Code First esegue la pipeline di migrazione scrivendo le modifiche in un file con estensione sql anziché applicarle. Dopo essere stato generato, lo script viene aperto automaticamente in Visual Studio dove è possibile visualizzarlo o salvarlo.

### <a name="generating-idempotent-scripts"></a>Generazione di script idempotenti

A partire da EF6, se si specifica **-SourceMigration $InitialDatabase**, viene generato uno script 'idempotente'. Gli script idempotenti possono eseguire l'aggiornamento del database da qualsiasi versione alla versione più recente o alla versione specificata con **-TargetMigration**. Lo script generato include la logica per controllare la tabella **\_\_MigrationsHistory** e applicare solo le modifiche non sono state applicate precedentemente.

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a>Aggiornamento automatico all'avvio dell'applicazione (Inizializzatore MigrateDatabaseToLatestVersion)

Durante la distribuzione dell'applicazione è possibile scegliere di aggiornare automaticamente il database e applicare eventuali migrazioni in sospeso all'avvio dell'applicazione. A tale scopo, registrare l'inizializzatore di database **MigrateDatabaseToLatestVersion**. Un inizializzatore di database contiene solo la logica usata per assicurarsi che il database sia configurato correttamente. Questa logica viene eseguita la prima volta che il contesto viene usato all'interno del processo dell'applicazione (**AppDomain**).

È possibile aggiornare il file **Program.cs**, come illustrato di seguito, per impostare l'inizializzatore **MigrateDatabaseToLatestVersion** per BlogContext prima che venga usato il contesto (riga 14). Si noti che è anche necessario aggiungere un'istruzione using per lo spazio dei nomi **System.Data.Entity** (riga 5).

*Quando si crea un'istanza di questo inizializzatore è necessario specificare il tipo di contesto (**BlogContext**) e la configurazione delle migrazioni (**Configurazioni**). La configurazione delle migrazioni è la classe aggiunta alla cartella **Migrazioni** quando vengono attivate le migrazioni.*

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

Ad ogni esecuzione, l'applicazione verificherà se il database di destinazione è aggiornato e, in caso negativo, applicherà tutte le migrazioni in sospeso.
