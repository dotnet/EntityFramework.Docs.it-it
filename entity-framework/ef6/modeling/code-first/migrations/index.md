---
title: Migrazioni Code First - EF6
description: Migrazioni Code First in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: 7b58ed82202e5a0b7e8676020e32deaac39bcf70
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064978"
---
# <a name="code-first-migrations"></a><span data-ttu-id="f6ab6-103">Migrazioni Code First</span><span class="sxs-lookup"><span data-stu-id="f6ab6-103">Code First Migrations</span></span>
<span data-ttu-id="f6ab6-104">Migrazioni Code First rappresenta il metodo più adatto per far evolvere lo schema del database dell'applicazione quando si usa il flusso di lavoro Code First.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-104">Code First Migrations is the recommended way to evolve your application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="f6ab6-105">Le migrazioni offrono un set di strumenti che consentono di:</span><span class="sxs-lookup"><span data-stu-id="f6ab6-105">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="f6ab6-106">Creare un database iniziale che può essere usato con il modello di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f6ab6-106">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="f6ab6-107">Generare migrazioni per tenere traccia delle modifiche apportate al modello di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f6ab6-107">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="f6ab6-108">Mantenere aggiornato il database con le modifiche</span><span class="sxs-lookup"><span data-stu-id="f6ab6-108">Keep your database up to date with those changes</span></span>

<span data-ttu-id="f6ab6-109">Nella procedura dettagliata seguente viene fornita una panoramica delle Migrazioni Code First in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-109">The following walkthrough will provide an overview of Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="f6ab6-110">È possibile eseguire l'intera procedura dettagliata o passare all'argomento desiderato.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-110">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="f6ab6-111">Vengono trattati i seguenti argomenti:</span><span class="sxs-lookup"><span data-stu-id="f6ab6-111">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="f6ab6-112">Creazione del modello iniziale e del database</span><span class="sxs-lookup"><span data-stu-id="f6ab6-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="f6ab6-113">Per iniziare a usare le migrazioni sono necessari un progetto e un modello Code First.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="f6ab6-114">In questa procedura dettagliata verranno usati i modelli tradizionali **Blog** e **Post**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="f6ab6-115">Creare una nuova applicazione console **MigrationsDemo**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-115">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="f6ab6-116">Aggiungere la versione più recente del pacchetto NuGet **EntityFramework** al progetto</span><span class="sxs-lookup"><span data-stu-id="f6ab6-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="f6ab6-117">**Strumenti –&gt; Library Package Manager (Gestione pacchetti librerie) –&gt; Console di Gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="f6ab6-118">Eseguire il comando **Install-Package EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="f6ab6-119">Aggiungere un file **Model.cs** con il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="f6ab6-120">Questo codice definisce una singola classe **Blog** che rappresenta il modello di dominio e una classe **BlogContext** che rappresenta il contesto Code First di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f6ab6-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

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

-   <span data-ttu-id="f6ab6-121">È ora possibile usare il modello per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="f6ab6-122">Aggiornare il file **Program.cs** con il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-122">Update the **Program.cs** file with the code shown below.</span></span>

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

-   <span data-ttu-id="f6ab6-123">Eseguire l'applicazione. Si noti che viene creato un database **MigrationsCodeDemo.BlogContext**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-123">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![Database Local DB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="f6ab6-125">Abilitare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="f6ab6-125">Enabling Migrations</span></span>

<span data-ttu-id="f6ab6-126">È ora possibile apportare ulteriori modifiche al modello.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="f6ab6-127">Introdurre una proprietà Url nella classe Blog.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="f6ab6-128">Se si esegue di nuovo l'applicazione, viene generata un'eccezione InvalidOperationException con il messaggio *The model backing the 'BlogContext' context has changed since the database was created. Valutare la possibilità di usare migrazioni Code First per aggiornare il database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*</span><span class="sxs-lookup"><span data-stu-id="f6ab6-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="f6ab6-129">Come suggerisce l'eccezione, è ora possibile iniziare a usare Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="f6ab6-130">Il primo passaggio consiste nell'abilitare le migrazioni per il contesto.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-130">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="f6ab6-131">Eseguire il comando **Enable-Migrations** nella console di Gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="f6ab6-131">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="f6ab6-132">Il comando aggiunge una cartella **Migrazioni** al progetto.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-132">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="f6ab6-133">La nuova cartella contiene due file:</span><span class="sxs-lookup"><span data-stu-id="f6ab6-133">This new folder contains two files:</span></span>

-   <span data-ttu-id="f6ab6-134">**La classe Configuration.**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-134">**The Configuration class.**</span></span> <span data-ttu-id="f6ab6-135">Questa classe consente di configurare il comportamento di Migrazioni per il contesto.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-135">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="f6ab6-136">In questa procedura dettagliata verrà usata solo la configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-136">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="f6ab6-137">*Poiché è presente un solo contesto Code First nel progetto, Enable-Migrations ha specificato automaticamente il tipo di contesto a cui si applica questa configurazione.*</span><span class="sxs-lookup"><span data-stu-id="f6ab6-137">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="f6ab6-138">**Una migrazione InitialCreate**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-138">**An InitialCreate migration**.</span></span> <span data-ttu-id="f6ab6-139">Questa migrazione viene generata poiché è già stato creato automaticamente un database da Code First prima dell'abilitazione delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-139">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="f6ab6-140">Il codice in questa migrazione di scaffolding rappresenta gli oggetti che sono già stati creati nel database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-140">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="f6ab6-141">In questo caso, si tratta della tabella **Blog** con le colonne **BlogId** e **Name**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-141">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="f6ab6-142">Il nome del file include un timestamp che facilita l'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-142">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="f6ab6-143">*Se il database non fosse già stato creato, questa migrazione InitialCreate non sarebbe stata aggiunta al progetto. La prima volta che viene chiamato Add-Migration, viene invece eseguito lo scaffolding del codice per la creazione delle tabelle in una nuova migrazione.*</span><span class="sxs-lookup"><span data-stu-id="f6ab6-143">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="f6ab6-144">Più modelli con lo stesso database di destinazione</span><span class="sxs-lookup"><span data-stu-id="f6ab6-144">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="f6ab6-145">Quando si usano versioni precedenti a EF6, è possibile usare solo un modello Code First per generare o gestire lo schema di un database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-145">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="f6ab6-146">Quando è disponibile una sola tabella **\_\_MigrationsHistory** per ogni database, non è possibile identificare l'appartenenza delle voci ai modelli.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-146">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="f6ab6-147">A partire da EF6, la classe **Configuration** include una proprietà **ContextKey**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-147">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="f6ab6-148">La proprietà svolge la funzione di identificatore univoco per ogni modello Code First.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-148">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="f6ab6-149">Una colonna corrispondente nella tabella **\_\_MigrationsHistory** consente a voci di più modelli di condividere la tabella.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-149">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="f6ab6-150">Per impostazione predefinita, questa proprietà è impostata sul nome completo del contesto.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-150">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="f6ab6-151">Generazione ed esecuzione di migrazioni</span><span class="sxs-lookup"><span data-stu-id="f6ab6-151">Generating & Running Migrations</span></span>

<span data-ttu-id="f6ab6-152">Migrazioni Code First include due comandi principali.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-152">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="f6ab6-153">**Add-Migration** esegue lo scaffolding della migrazione successiva in base alle modifiche apportate al modello dalla creazione dell'ultima migrazione</span><span class="sxs-lookup"><span data-stu-id="f6ab6-153">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="f6ab6-154">**Update-Database** applica tutte le migrazioni in sospeso al database</span><span class="sxs-lookup"><span data-stu-id="f6ab6-154">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="f6ab6-155">Viene ora eseguito lo scaffolding di una migrazione per la nuova proprietà Url aggiunta.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-155">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="f6ab6-156">Il comando **Add-Migration** consente di assegnare un nome alle migrazioni, ovvero **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-156">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="f6ab6-157">Eseguire il comando **Add-Migration AddBlogUrl** nella console di Gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="f6ab6-157">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="f6ab6-158">Nella cartella **Migrazioni** viene inserita la nuova migrazione **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-158">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="f6ab6-159">Il nome file della migrazione è preceduto da un timestamp per facilitare l'ordinamento</span><span class="sxs-lookup"><span data-stu-id="f6ab6-159">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

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

<span data-ttu-id="f6ab6-160">È ora possibile apportare modifiche o aggiunte alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-160">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="f6ab6-161">Usare **Update-Database** per applicare la migrazione al database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-161">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="f6ab6-162">Eseguire il comando **Update-Database** nella console di Gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="f6ab6-162">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="f6ab6-163">Migrazioni Code First esegue un confronto delle migrazioni della cartella **Migrazioni** con le migrazioni applicate al database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-163">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="f6ab6-164">Viene individuata la migrazione **AddBlogUrl** da applicare e viene eseguita la migrazione.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-164">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="f6ab6-165">Il database **MigrationsDemo.BlogContext** è ora aggiornato in modo da includere la colonna **Url** nella tabella **Blogs**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-165">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="f6ab6-166">Personalizzazione delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="f6ab6-166">Customizing Migrations</span></span>

<span data-ttu-id="f6ab6-167">Nei passaggi precedenti è stata creata ed eseguita una migrazione senza apportare alcuna modifica.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-167">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="f6ab6-168">Viene ora descritto come modificare il codice generato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-168">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="f6ab6-169">È ora possibile apportare alcune modifiche al modello: aggiungere una nuova proprietà **Rating** alla classe **Blog**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-169">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="f6ab6-170">Aggiungere anche una nuova classe **Post**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-170">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="f6ab6-171">Si procede anche all'aggiunta delle raccolta **Posts** alla classe **Blog** per creare l'altro elemento della relazione tra **Blog** e **Post**</span><span class="sxs-lookup"><span data-stu-id="f6ab6-171">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="f6ab6-172">Viene usato il comando **Add-Migration** per consentire a Migrazioni Code First di eseguire automaticamente lo scaffolding nella migrazione.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-172">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="f6ab6-173">La migrazione viene denominata **AddPostClass**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-173">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="f6ab6-174">Eseguire il comando **Add-Migration AddPostClass** nella console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-174">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="f6ab6-175">Sebbene Migrazioni Code First abbia eseguito lo scaffolding di queste modifiche, è possibile modificare ulteriori elementi:</span><span class="sxs-lookup"><span data-stu-id="f6ab6-175">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="f6ab6-176">Aggiungere innanzitutto un indice univoco alla colonna **Posts.Title** (aggiunta alle righe 22 e 29 del codice seguente).</span><span class="sxs-lookup"><span data-stu-id="f6ab6-176">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="f6ab6-177">Viene anche aggiunta una colonna **Blogs.Rating** non nullable.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-177">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="f6ab6-178">Se la tabella include dati esistenti, viene assegnato alla tabella il valore predefinito CLR del tipo di dati per la nuova colonna (poiché il tipo di dati di Rating è integer, il valore è **0**).</span><span class="sxs-lookup"><span data-stu-id="f6ab6-178">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="f6ab6-179">Si supponga tuttavia di voler specificare un valore predefinito **3** in modo che le righe esistenti della tabella **Blogs** abbiano inizio con una classificazione ragionevole.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-179">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="f6ab6-180">(È possibile visualizzare il valore predefinito specificato nella riga 24 del codice riportato di seguito)</span><span class="sxs-lookup"><span data-stu-id="f6ab6-180">(You can see the default value specified on line 24 of the code below)</span></span>

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

<span data-ttu-id="f6ab6-181">La migrazione modificata è ora pronta. Usare **Update-Database** per aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-181">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="f6ab6-182">Specificare questa volta il flag **-Verbose** per poter visualizzare il codice SQL eseguito da Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-182">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="f6ab6-183">Eseguire il comando **Update-Database –Verbose** nella console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-183">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="f6ab6-184">Spostamento di dati/SQL personalizzato</span><span class="sxs-lookup"><span data-stu-id="f6ab6-184">Data Motion / Custom SQL</span></span>

<span data-ttu-id="f6ab6-185">Le operazioni sulle migrazioni eseguite finora non comportano alcuna modifica o spostamento dei dati. Di seguito viene descritto il caso in cui è necessario spostare alcuni dati.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-185">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="f6ab6-186">Sebbene non sia disponibile alcun supporto nativo per lo spostamento dei dati, è possibile eseguire alcuni comandi SQL arbitrari in qualsiasi posizione all'interno dello script.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-186">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="f6ab6-187">Aggiungere una proprietà **Post.Abstract** al modello.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-187">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="f6ab6-188">In seguito verrà prepopolato **Abstract** per i post esistenti usando un testo della parte iniziale della colonna **Content**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-188">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="f6ab6-189">Viene usato il comando **Add-Migration** per consentire a Migrazioni Code First di eseguire automaticamente lo scaffolding nella migrazione.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-189">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="f6ab6-190">Eseguire il comando **Add-Migration AddPostAbstract** nella console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-190">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="f6ab6-191">Sebbene la migrazione generata gestisca le modifiche dello schema, si vuole prepopolare anche la colonna **Abstract** usando i primi 100 caratteri del contenuto di ogni post.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-191">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="f6ab6-192">Per eseguire questa operazione, passare a SQL ed eseguire un'istruzione **UPDATE** dopo aver aggiunto la colonna.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-192">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="f6ab6-193">(L'aggiunta si trova nella riga 12 del codice riportato di seguito)</span><span class="sxs-lookup"><span data-stu-id="f6ab6-193">(Adding in line 12 in the code below)</span></span>

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

<span data-ttu-id="f6ab6-194">La migrazione modificata è ora pronta. Usare **Update-Database** per aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-194">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="f6ab6-195">Specificare il flag **-Verbose** per visualizzare il codice SQL eseguito nel database.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-195">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="f6ab6-196">Eseguire il comando **Update-Database –Verbose** nella console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-196">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="f6ab6-197">Eseguire la migrazione a una versione specifica (incluso il downgrade)</span><span class="sxs-lookup"><span data-stu-id="f6ab6-197">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="f6ab6-198">Sebbene fino ad ora sia sempre stato eseguito l'aggiornamento alla migrazione più recente, in alcuni casi può essere necessario eseguire l'aggiornamento o il downgrade a una migrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-198">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="f6ab6-199">Si supponga di volere eseguire la migrazione del database allo stato in cui si trovava dopo l'esecuzione della migrazione **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-199">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="f6ab6-200">È possibile usare l'opzione **-TargetMigration** per eseguire il downgrade alla migrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-200">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="f6ab6-201">Eseguire il comando **Update-Database -TargetMigration: AddBlogUrl** nella console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-201">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="f6ab6-202">Il comando esegue lo script di downgrade per le migrazioni **AddBlogAbstract** e **AddPostClass**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-202">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="f6ab6-203">Se si vuole eseguire il rollback a un database vuoto, è possibile usare il comando **Update-Database –TargetMigration: $InitialDatabase**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-203">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="f6ab6-204">Recupero di uno script SQL</span><span class="sxs-lookup"><span data-stu-id="f6ab6-204">Getting a SQL Script</span></span>

<span data-ttu-id="f6ab6-205">Dopo aver controllato le modifiche nel controllo del codice sorgente, se un altro sviluppatore vuole apportare le modifiche nel proprio computer è sufficiente che esegua la sincronizzazione.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-205">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="f6ab6-206">Dopo aver ottenuto le nuove migrazioni, sarà sufficiente eseguire il comando Update-Database per applicare le modifiche in locale.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-206">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="f6ab6-207">Tuttavia, se si vuole eseguire il push delle modifiche in un server di test, ed eventualmente in produzione, può essere necessario uno script SQL da inviare al DBA.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-207">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="f6ab6-208">Eseguire il comando **Update-Database** e specificare il flag **-Script** in modo che le modifiche vengano scritte in uno script anziché essere applicate.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-208">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="f6ab6-209">Vengono anche specificate una migrazione di origine e una migrazione di destinazione per cui generare lo script.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-209">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="f6ab6-210">Si vuole che lo script passi da un database vuoto (**$InitialDatabase**) alla versione più recente (migrazione **AddPostAbstract**).</span><span class="sxs-lookup"><span data-stu-id="f6ab6-210">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="f6ab6-211">*Se non si specifica una migrazione di destinazione, Migrazioni userà la migrazione di più recente come destinazione. Se non si specifica una migrazione di origine, Migrazioni userà lo stato corrente del database.*</span><span class="sxs-lookup"><span data-stu-id="f6ab6-211">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="f6ab6-212">Eseguire il comando **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** nella console di Gestione pacchetti</span><span class="sxs-lookup"><span data-stu-id="f6ab6-212">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="f6ab6-213">Migrazioni Code First esegue la pipeline di migrazione scrivendo le modifiche in un file con estensione sql anziché applicarle.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-213">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="f6ab6-214">Dopo essere stato generato, lo script viene aperto automaticamente in Visual Studio dove è possibile visualizzarlo o salvarlo.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-214">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="f6ab6-215">Generazione di script idempotenti</span><span class="sxs-lookup"><span data-stu-id="f6ab6-215">Generating Idempotent Scripts</span></span>

<span data-ttu-id="f6ab6-216">A partire da EF6, se si specifica **-SourceMigration $InitialDatabase**, viene generato uno script 'idempotente'.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-216">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="f6ab6-217">Gli script idempotenti possono eseguire l'aggiornamento del database da qualsiasi versione alla versione più recente o alla versione specificata con **-TargetMigration**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-217">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="f6ab6-218">Lo script generato include la logica per controllare la tabella **\_\_MigrationsHistory** e applicare solo le modifiche non sono state applicate precedentemente.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-218">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="f6ab6-219">Aggiornamento automatico all'avvio dell'applicazione (Inizializzatore MigrateDatabaseToLatestVersion)</span><span class="sxs-lookup"><span data-stu-id="f6ab6-219">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="f6ab6-220">Durante la distribuzione dell'applicazione è possibile scegliere di aggiornare automaticamente il database e applicare eventuali migrazioni in sospeso all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-220">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="f6ab6-221">A tale scopo, registrare l'inizializzatore di database **MigrateDatabaseToLatestVersion**.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-221">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="f6ab6-222">Un inizializzatore di database contiene solo la logica usata per assicurarsi che il database sia configurato correttamente.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-222">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="f6ab6-223">Questa logica viene eseguita la prima volta che il contesto viene usato all'interno del processo dell'applicazione (**AppDomain**).</span><span class="sxs-lookup"><span data-stu-id="f6ab6-223">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="f6ab6-224">È possibile aggiornare il file **Program.cs**, come illustrato di seguito, per impostare l'inizializzatore **MigrateDatabaseToLatestVersion** per BlogContext prima che venga usato il contesto (riga 14).</span><span class="sxs-lookup"><span data-stu-id="f6ab6-224">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="f6ab6-225">Si noti che è anche necessario aggiungere un'istruzione using per lo spazio dei nomi **System.Data.Entity** (riga 5).</span><span class="sxs-lookup"><span data-stu-id="f6ab6-225">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="f6ab6-226">*Quando si crea un'istanza di questo inizializzatore è necessario specificare il tipo di contesto (**BlogContext**) e la configurazione delle migrazioni (**Configurazioni**). La configurazione delle migrazioni è la classe aggiunta alla cartella **Migrazioni** quando vengono attivate le migrazioni.*</span><span class="sxs-lookup"><span data-stu-id="f6ab6-226">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

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

<span data-ttu-id="f6ab6-227">Ad ogni esecuzione, l'applicazione verificherà se il database di destinazione è aggiornato e, in caso negativo, applicherà tutte le migrazioni in sospeso.</span><span class="sxs-lookup"><span data-stu-id="f6ab6-227">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>
