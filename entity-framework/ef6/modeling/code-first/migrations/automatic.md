---
title: Migrazioni Code First automatico-EF6
description: Migrazioni Code First automatico in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: e7bd9ff7d9dcecb7fecf213306047a53fc04135c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072564"
---
# <a name="automatic-code-first-migrations"></a>Migrazioni Code First automatico
Le migrazioni automatiche consentono di usare Migrazioni Code First senza avere un file di codice nel progetto per ogni modifica apportata. Non tutte le modifiche possono essere applicate automaticamente. ad esempio, per rinominare le colonne è necessario utilizzare una migrazione basata su codice.

> [!NOTE]
> Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base. In caso contrario, sarà necessario leggere [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) prima di continuare.

## <a name="recommendation-for-team-environments"></a>Raccomandazione per gli ambienti Team

È possibile alternare le migrazioni automatiche e basate su codice, ma ciò non è consigliato negli scenari di sviluppo del team. Se si fa parte di un team di sviluppatori che usano il controllo del codice sorgente, è consigliabile usare migrazioni puramente automatiche o migrazioni esclusivamente basate su codice. Considerate le limitazioni delle migrazioni automatiche, è consigliabile usare migrazioni basate sul codice in ambienti team.

## <a name="building-an-initial-model--database"></a>Creazione del modello iniziale e del database

Per iniziare a usare le migrazioni sono necessari un progetto e un modello Code First. In questa procedura dettagliata verranno usati i modelli tradizionali **Blog** e **Post**.

-   Creare una nuova applicazione console **MigrationsAutomaticDemo**
-   Aggiungere la versione più recente del pacchetto NuGet **EntityFramework** al progetto
    -   **Strumenti –&gt; Library Package Manager (Gestione pacchetti librerie) –&gt; Console di Gestione pacchetti**
    -   Eseguire il comando **Install-Package EntityFramework**
-   Aggiungere un file **Model.cs** con il codice riportato di seguito. Questo codice definisce una singola classe **Blog** che rappresenta il modello di dominio e una classe **BlogContext** che rappresenta il contesto Code First di Entity Framework

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

      namespace MigrationsAutomaticDemo
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

-   Eseguire l'applicazione e si noterà che viene creato un database **MigrationsAutomaticCodeDemo. BlogContext** .

    ![Database locale database](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Abilitare le migrazioni

È ora possibile apportare ulteriori modifiche al modello.

-   Introdurre una proprietà Url nella classe Blog.

``` csharp
    public string Url { get; set; }
```

Se si esegue di nuovo l'applicazione, viene generata un'eccezione InvalidOperationException con il messaggio *The model backing the 'BlogContext' context has changed since the database was created. Valutare la possibilità di usare migrazioni Code First per aggiornare il database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*

Come suggerisce l'eccezione, è ora possibile iniziare a usare Migrazioni Code First. Poiché si vogliono usare le migrazioni automatiche, si specifica l'opzione **-EnableAutomaticMigrations** .

-   Eseguire il comando **Enable-Migrations-EnableAutomaticMigrations** nella console di gestione pacchetti. questo comando ha aggiunto una cartella **Migrations** al progetto. Questa nuova cartella contiene un file:

-   **La classe Configuration.** Questa classe consente di configurare il comportamento di Migrazioni per il contesto. In questa procedura dettagliata verrà usata solo la configurazione predefinita.
    *Poiché è presente un solo contesto Code First nel progetto, Enable-Migrations ha specificato automaticamente il tipo di contesto a cui si applica questa configurazione.*

 

## <a name="your-first-automatic-migration"></a>Prima migrazione automatica

Migrazioni Code First include due comandi principali.

-   **Add-Migration** esegue lo scaffolding della migrazione successiva in base alle modifiche apportate al modello dalla creazione dell'ultima migrazione
-   **Update-Database** applica tutte le migrazioni in sospeso al database

Si eviterà di usare l'aggiunta della migrazione (a meno che non sia effettivamente necessario) e si concentrerà su come consentire a Migrazioni Code First di calcolare e applicare automaticamente le modifiche. Usare **Update-database** per ottenere migrazioni Code First per eseguire il push delle modifiche al modello (la nuova proprietà **Blog. ur**l) nel database.

-   Eseguire il comando **Update-database** nella console di gestione pacchetti.

Il database **MigrationsAutomaticDemo. BlogContext** è ora aggiornato per includere la colonna **URL** nella tabella **Blogs** .

 

## <a name="your-second-automatic-migration"></a>Seconda migrazione automatica

Apportare un'altra modifica e consentire a Migrazioni Code First di eseguire automaticamente il push delle modifiche apportate al database.

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

A questo punto, usare **Update-database** per rendere aggiornato il database. Specificare questa volta il flag **-Verbose** per poter visualizzare il codice SQL eseguito da Migrazioni Code First.

-   Eseguire il comando **Update-Database –Verbose** nella console di Gestione pacchetti.

## <a name="adding-a-code-based-migration"></a>Aggiunta di una migrazione basata su codice

Si osservi ora che è possibile usare una migrazione basata sul codice per.

-   Aggiungere una proprietà **rating** alla classe **Blog**

``` csharp
    public int Rating { get; set; }
```

È possibile eseguire **Update-database** per effettuare il push di queste modifiche nel database. Tuttavia, viene aggiunta una colonna di **Blog. rating** che non ammette i valori null. se nella tabella sono presenti dati esistenti, verrà assegnato il valore predefinito CLR del tipo di dati per la nuova colonna (rating è Integer, in modo che sia **0**). Si supponga tuttavia di voler specificare un valore predefinito **3** in modo che le righe esistenti della tabella **Blogs** abbiano inizio con una classificazione ragionevole.
Usare il comando Add-Migration per scrivere questa modifica in una migrazione basata sul codice in modo che sia possibile modificarla. Il comando **Add-Migration** consente di assegnare un nome a queste migrazioni, quindi è sufficiente chiamare la nostra **AddBlogRating**.

-   Eseguire il comando **Add-Migration AddBlogRating** nella console di gestione pacchetti.
-   Nella cartella **migrazioni** è ora disponibile una nuova migrazione di **AddBlogRating** . Il nome del file di migrazione è preceduto da un timestamp per facilitare l'ordinamento. Modificare il codice generato per specificare il valore predefinito 3 per Blog. rating (riga 10 nel codice riportato di seguito)

*La migrazione dispone anche di un file code-behind che acquisisce alcuni metadati. Questi metadati consentiranno Migrazioni Code First di replicare le migrazioni automatiche eseguite prima di questa migrazione basata sul codice. Questo è importante se un altro sviluppatore vuole eseguire le migrazioni o quando è il momento di distribuire l'applicazione.*

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

La migrazione modificata è ora pronta. Usare **Update-Database** per aggiornare il database.

-   Eseguire il comando **Update-database** nella console di gestione pacchetti.

## <a name="back-to-automatic-migrations"></a>Torna a migrazioni automatiche

A questo punto è possibile tornare alla migrazione automatica per le modifiche più semplici. Migrazioni Code First si occuperà di eseguire le migrazioni automatiche e basate sul codice nell'ordine corretto, in base ai metadati archiviati nel file code-behind per ogni migrazione basata sul codice.

-   Aggiungere una proprietà post. Abstract al modello

``` csharp
    public string Abstract { get; set; }
```

A questo punto è possibile usare **Update-database** per ottenere migrazioni Code First per eseguire il push di questa modifica nel database usando una migrazione automatica.

-   Eseguire il comando **Update-database** nella console di gestione pacchetti.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato illustrato come utilizzare le migrazioni automatiche per eseguire il push delle modifiche del modello nel database. È stato anche illustrato come eseguire il patibolo ed eseguire migrazioni basate su codice tra le migrazioni automatiche quando è necessario un maggiore controllo.
