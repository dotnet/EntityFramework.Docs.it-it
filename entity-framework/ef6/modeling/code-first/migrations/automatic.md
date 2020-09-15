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
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="2941a-103">Migrazioni Code First automatico</span><span class="sxs-lookup"><span data-stu-id="2941a-103">Automatic Code First Migrations</span></span>
<span data-ttu-id="2941a-104">Le migrazioni automatiche consentono di usare Migrazioni Code First senza avere un file di codice nel progetto per ogni modifica apportata.</span><span class="sxs-lookup"><span data-stu-id="2941a-104">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="2941a-105">Non tutte le modifiche possono essere applicate automaticamente. ad esempio, per rinominare le colonne è necessario utilizzare una migrazione basata su codice.</span><span class="sxs-lookup"><span data-stu-id="2941a-105">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="2941a-106">Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base.</span><span class="sxs-lookup"><span data-stu-id="2941a-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="2941a-107">In caso contrario, sarà necessario leggere [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="2941a-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="2941a-108">Raccomandazione per gli ambienti Team</span><span class="sxs-lookup"><span data-stu-id="2941a-108">Recommendation for Team Environments</span></span>

<span data-ttu-id="2941a-109">È possibile alternare le migrazioni automatiche e basate su codice, ma ciò non è consigliato negli scenari di sviluppo del team.</span><span class="sxs-lookup"><span data-stu-id="2941a-109">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="2941a-110">Se si fa parte di un team di sviluppatori che usano il controllo del codice sorgente, è consigliabile usare migrazioni puramente automatiche o migrazioni esclusivamente basate su codice.</span><span class="sxs-lookup"><span data-stu-id="2941a-110">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="2941a-111">Considerate le limitazioni delle migrazioni automatiche, è consigliabile usare migrazioni basate sul codice in ambienti team.</span><span class="sxs-lookup"><span data-stu-id="2941a-111">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="2941a-112">Creazione del modello iniziale e del database</span><span class="sxs-lookup"><span data-stu-id="2941a-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="2941a-113">Per iniziare a usare le migrazioni sono necessari un progetto e un modello Code First.</span><span class="sxs-lookup"><span data-stu-id="2941a-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="2941a-114">In questa procedura dettagliata verranno usati i modelli tradizionali **Blog** e **Post**.</span><span class="sxs-lookup"><span data-stu-id="2941a-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="2941a-115">Creare una nuova applicazione console **MigrationsAutomaticDemo**</span><span class="sxs-lookup"><span data-stu-id="2941a-115">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="2941a-116">Aggiungere la versione più recente del pacchetto NuGet **EntityFramework** al progetto</span><span class="sxs-lookup"><span data-stu-id="2941a-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="2941a-117">**Strumenti –&gt; Library Package Manager (Gestione pacchetti librerie) –&gt; Console di Gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="2941a-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="2941a-118">Eseguire il comando **Install-Package EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="2941a-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="2941a-119">Aggiungere un file **Model.cs** con il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="2941a-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="2941a-120">Questo codice definisce una singola classe **Blog** che rappresenta il modello di dominio e una classe **BlogContext** che rappresenta il contesto Code First di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2941a-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

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

-   <span data-ttu-id="2941a-121">È ora possibile usare il modello per eseguire l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="2941a-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="2941a-122">Aggiornare il file **Program.cs** con il codice riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="2941a-122">Update the **Program.cs** file with the code shown below.</span></span>

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

-   <span data-ttu-id="2941a-123">Eseguire l'applicazione e si noterà che viene creato un database **MigrationsAutomaticCodeDemo. BlogContext** .</span><span class="sxs-lookup"><span data-stu-id="2941a-123">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![Database locale database](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="2941a-125">Abilitare le migrazioni</span><span class="sxs-lookup"><span data-stu-id="2941a-125">Enabling Migrations</span></span>

<span data-ttu-id="2941a-126">È ora possibile apportare ulteriori modifiche al modello.</span><span class="sxs-lookup"><span data-stu-id="2941a-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="2941a-127">Introdurre una proprietà Url nella classe Blog.</span><span class="sxs-lookup"><span data-stu-id="2941a-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="2941a-128">Se si esegue di nuovo l'applicazione, viene generata un'eccezione InvalidOperationException con il messaggio *The model backing the 'BlogContext' context has changed since the database was created. Valutare la possibilità di usare migrazioni Code First per aggiornare il database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*</span><span class="sxs-lookup"><span data-stu-id="2941a-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="2941a-129">Come suggerisce l'eccezione, è ora possibile iniziare a usare Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="2941a-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="2941a-130">Poiché si vogliono usare le migrazioni automatiche, si specifica l'opzione **-EnableAutomaticMigrations** .</span><span class="sxs-lookup"><span data-stu-id="2941a-130">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="2941a-131">Eseguire il comando **Enable-Migrations-EnableAutomaticMigrations** nella console di gestione pacchetti. questo comando ha aggiunto una cartella **Migrations** al progetto.</span><span class="sxs-lookup"><span data-stu-id="2941a-131">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="2941a-132">Questa nuova cartella contiene un file:</span><span class="sxs-lookup"><span data-stu-id="2941a-132">This new folder contains one file:</span></span>

-   <span data-ttu-id="2941a-133">**La classe Configuration.**</span><span class="sxs-lookup"><span data-stu-id="2941a-133">**The Configuration class.**</span></span> <span data-ttu-id="2941a-134">Questa classe consente di configurare il comportamento di Migrazioni per il contesto.</span><span class="sxs-lookup"><span data-stu-id="2941a-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="2941a-135">In questa procedura dettagliata verrà usata solo la configurazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2941a-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="2941a-136">*Poiché è presente un solo contesto Code First nel progetto, Enable-Migrations ha specificato automaticamente il tipo di contesto a cui si applica questa configurazione.*</span><span class="sxs-lookup"><span data-stu-id="2941a-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="2941a-137">Prima migrazione automatica</span><span class="sxs-lookup"><span data-stu-id="2941a-137">Your First Automatic Migration</span></span>

<span data-ttu-id="2941a-138">Migrazioni Code First include due comandi principali.</span><span class="sxs-lookup"><span data-stu-id="2941a-138">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="2941a-139">**Add-Migration** esegue lo scaffolding della migrazione successiva in base alle modifiche apportate al modello dalla creazione dell'ultima migrazione</span><span class="sxs-lookup"><span data-stu-id="2941a-139">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="2941a-140">**Update-Database** applica tutte le migrazioni in sospeso al database</span><span class="sxs-lookup"><span data-stu-id="2941a-140">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="2941a-141">Si eviterà di usare l'aggiunta della migrazione (a meno che non sia effettivamente necessario) e si concentrerà su come consentire a Migrazioni Code First di calcolare e applicare automaticamente le modifiche.</span><span class="sxs-lookup"><span data-stu-id="2941a-141">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="2941a-142">Usare **Update-database** per ottenere migrazioni Code First per eseguire il push delle modifiche al modello (la nuova proprietà **Blog. ur**l) nel database.</span><span class="sxs-lookup"><span data-stu-id="2941a-142">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="2941a-143">Eseguire il comando **Update-database** nella console di gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="2941a-143">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="2941a-144">Il database **MigrationsAutomaticDemo. BlogContext** è ora aggiornato per includere la colonna **URL** nella tabella **Blogs** .</span><span class="sxs-lookup"><span data-stu-id="2941a-144">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="2941a-145">Seconda migrazione automatica</span><span class="sxs-lookup"><span data-stu-id="2941a-145">Your Second Automatic Migration</span></span>

<span data-ttu-id="2941a-146">Apportare un'altra modifica e consentire a Migrazioni Code First di eseguire automaticamente il push delle modifiche apportate al database.</span><span class="sxs-lookup"><span data-stu-id="2941a-146">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="2941a-147">Aggiungere anche una nuova classe **Post**</span><span class="sxs-lookup"><span data-stu-id="2941a-147">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="2941a-148">Si procede anche all'aggiunta delle raccolta **Posts** alla classe **Blog** per creare l'altro elemento della relazione tra **Blog** e **Post**</span><span class="sxs-lookup"><span data-stu-id="2941a-148">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="2941a-149">A questo punto, usare **Update-database** per rendere aggiornato il database.</span><span class="sxs-lookup"><span data-stu-id="2941a-149">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="2941a-150">Specificare questa volta il flag **-Verbose** per poter visualizzare il codice SQL eseguito da Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="2941a-150">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="2941a-151">Eseguire il comando **Update-Database –Verbose** nella console di Gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="2941a-151">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="2941a-152">Aggiunta di una migrazione basata su codice</span><span class="sxs-lookup"><span data-stu-id="2941a-152">Adding a Code Based Migration</span></span>

<span data-ttu-id="2941a-153">Si osservi ora che è possibile usare una migrazione basata sul codice per.</span><span class="sxs-lookup"><span data-stu-id="2941a-153">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="2941a-154">Aggiungere una proprietà **rating** alla classe **Blog**</span><span class="sxs-lookup"><span data-stu-id="2941a-154">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="2941a-155">È possibile eseguire **Update-database** per effettuare il push di queste modifiche nel database.</span><span class="sxs-lookup"><span data-stu-id="2941a-155">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="2941a-156">Tuttavia, viene aggiunta una colonna di **Blog. rating** che non ammette i valori null. se nella tabella sono presenti dati esistenti, verrà assegnato il valore predefinito CLR del tipo di dati per la nuova colonna (rating è Integer, in modo che sia **0**).</span><span class="sxs-lookup"><span data-stu-id="2941a-156">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="2941a-157">Si supponga tuttavia di voler specificare un valore predefinito **3** in modo che le righe esistenti della tabella **Blogs** abbiano inizio con una classificazione ragionevole.</span><span class="sxs-lookup"><span data-stu-id="2941a-157">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="2941a-158">Usare il comando Add-Migration per scrivere questa modifica in una migrazione basata sul codice in modo che sia possibile modificarla.</span><span class="sxs-lookup"><span data-stu-id="2941a-158">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="2941a-159">Il comando **Add-Migration** consente di assegnare un nome a queste migrazioni, quindi è sufficiente chiamare la nostra **AddBlogRating**.</span><span class="sxs-lookup"><span data-stu-id="2941a-159">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="2941a-160">Eseguire il comando **Add-Migration AddBlogRating** nella console di gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="2941a-160">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="2941a-161">Nella cartella **migrazioni** è ora disponibile una nuova migrazione di **AddBlogRating** .</span><span class="sxs-lookup"><span data-stu-id="2941a-161">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="2941a-162">Il nome del file di migrazione è preceduto da un timestamp per facilitare l'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="2941a-162">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="2941a-163">Modificare il codice generato per specificare il valore predefinito 3 per Blog. rating (riga 10 nel codice riportato di seguito)</span><span class="sxs-lookup"><span data-stu-id="2941a-163">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="2941a-164">*La migrazione dispone anche di un file code-behind che acquisisce alcuni metadati. Questi metadati consentiranno Migrazioni Code First di replicare le migrazioni automatiche eseguite prima di questa migrazione basata sul codice. Questo è importante se un altro sviluppatore vuole eseguire le migrazioni o quando è il momento di distribuire l'applicazione.*</span><span class="sxs-lookup"><span data-stu-id="2941a-164">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

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

<span data-ttu-id="2941a-165">La migrazione modificata è ora pronta. Usare **Update-Database** per aggiornare il database.</span><span class="sxs-lookup"><span data-stu-id="2941a-165">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="2941a-166">Eseguire il comando **Update-database** nella console di gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="2941a-166">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="2941a-167">Torna a migrazioni automatiche</span><span class="sxs-lookup"><span data-stu-id="2941a-167">Back to Automatic Migrations</span></span>

<span data-ttu-id="2941a-168">A questo punto è possibile tornare alla migrazione automatica per le modifiche più semplici.</span><span class="sxs-lookup"><span data-stu-id="2941a-168">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="2941a-169">Migrazioni Code First si occuperà di eseguire le migrazioni automatiche e basate sul codice nell'ordine corretto, in base ai metadati archiviati nel file code-behind per ogni migrazione basata sul codice.</span><span class="sxs-lookup"><span data-stu-id="2941a-169">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="2941a-170">Aggiungere una proprietà post. Abstract al modello</span><span class="sxs-lookup"><span data-stu-id="2941a-170">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="2941a-171">A questo punto è possibile usare **Update-database** per ottenere migrazioni Code First per eseguire il push di questa modifica nel database usando una migrazione automatica.</span><span class="sxs-lookup"><span data-stu-id="2941a-171">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="2941a-172">Eseguire il comando **Update-database** nella console di gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="2941a-172">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="2941a-173">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="2941a-173">Summary</span></span>

<span data-ttu-id="2941a-174">In questa procedura dettagliata è stato illustrato come utilizzare le migrazioni automatiche per eseguire il push delle modifiche del modello nel database.</span><span class="sxs-lookup"><span data-stu-id="2941a-174">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="2941a-175">È stato anche illustrato come eseguire il patibolo ed eseguire migrazioni basate su codice tra le migrazioni automatiche quando è necessario un maggiore controllo.</span><span class="sxs-lookup"><span data-stu-id="2941a-175">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
