---
title: Introduzione con EF Core e Novell-EF Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 14fe61e0ddd648f824bfe3c1c91a58af91b1c2d4
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86453039"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="cc7b4-102">Introduzione con EF Core e Novell</span><span class="sxs-lookup"><span data-stu-id="cc7b4-102">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="cc7b4-103">In questa esercitazione si creerà un'applicazione [Novell. Forms](/xamarin/get-started/what-is-xamarin-forms) che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-103">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="cc7b4-104">È possibile seguire l'esercitazione con Visual Studio in Windows o Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-104">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="cc7b4-105">È possibile visualizzare l'esempio di questo articolo [in GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span><span class="sxs-lookup"><span data-stu-id="cc7b4-105">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc7b4-106">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="cc7b4-106">Prerequisites</span></span>

<span data-ttu-id="cc7b4-107">Installare uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="cc7b4-107">Install one of the below:</span></span>

* <span data-ttu-id="cc7b4-108">[Visual Studio 2019 versione 16,3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:</span><span class="sxs-lookup"><span data-stu-id="cc7b4-108">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="cc7b4-109">**Sviluppo di app per dispositivi mobili con .NET**</span><span class="sxs-lookup"><span data-stu-id="cc7b4-109">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="cc7b4-110">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="cc7b4-110">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="cc7b4-111">In questa [documentazione vengono fornite istruzioni dettagliate](/xamarin/get-started/installation) per l'installazione di ogni piattaforma.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-111">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="cc7b4-112">Scaricare ed eseguire il progetto di esempio</span><span class="sxs-lookup"><span data-stu-id="cc7b4-112">Download and run the sample project</span></span>

<span data-ttu-id="cc7b4-113">Per eseguire ed esplorare questa applicazione di esempio, scaricare il codice in GitHub.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-113">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="cc7b4-114">Al termine del download, aprire il file della soluzione `EFGettingStarted.sln` in Visual Studio o Visual Studio per Mac ed eseguire l'applicazione nella piattaforma di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-114">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="cc7b4-115">Quando l'app viene avviata per la prima volta, il database SQLite locale viene popolato con due voci che rappresentano i Blog.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-115">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Screenshot della pagina dell'elenco dei Blog](_static/xamarin-tutorial-1.png)

<span data-ttu-id="cc7b4-117">Fare clic sul pulsante **Aggiungi** sulla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-117">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="cc7b4-118">Verrà visualizzata una nuova pagina che consente di immettere informazioni su un nuovo blog.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-118">A new page will appear that allows you to enter information about a new blog.</span></span>

![Screenshot della pagina di modifica del nuovo Blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="cc7b4-120">Compilare tutte le informazioni e fare clic su **Salva** sulla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-120">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="cc7b4-121">Il nuovo blog sarà salvato nel database SQLite dell'app e sarà visualizzato nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-121">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="cc7b4-122">È possibile fare clic su una delle voci di Blog nell'elenco e visualizzare tutti i post del Blog.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-122">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Screenshot della pagina di elenco dei post di Blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="cc7b4-124">Fare clic su **Aggiungi** nella barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-124">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="cc7b4-125">Viene visualizzata una pagina che consente di compilare informazioni su un nuovo post di Blog.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-125">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Screenshot della pagina Aggiungi nuova post](_static/xamarin-tutorial-4.png)

<span data-ttu-id="cc7b4-127">Compilare tutte le informazioni e fare clic su **Salva** sulla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-127">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="cc7b4-128">Il nuovo post verrà associato al post di Blog su cui è stato fatto clic in un passaggio precedente e verrà salvato nel database SQLite dell'app e visualizzato nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-128">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="cc7b4-129">Tornare alla pagina dell'elenco dei Blog.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-129">Go back to the blog list page.</span></span> <span data-ttu-id="cc7b4-130">E fare clic su **Elimina tutto** nella barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-130">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="cc7b4-131">Tutti i Blog e i post corrispondenti verranno eliminati dal database SQLite dell'app.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-131">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Screenshot dell'app con tutti i Blog eliminati](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="cc7b4-133">Esplorare il codice</span><span class="sxs-lookup"><span data-stu-id="cc7b4-133">Explore the code</span></span>

<span data-ttu-id="cc7b4-134">Le sezioni seguenti illustrano il codice nel progetto di esempio che legge, crea, aggiorna ed Elimina i dati da un database SQLite usando EF Core con Novell. Forms.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-134">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="cc7b4-135">Si presuppone che l'utente abbia familiarità con gli argomenti di Novell. Forms relativi alla [visualizzazione dei dati](/xamarin/xamarin-forms/app-fundamentals/data-binding/) e all' [esplorazione tra le pagine](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="cc7b4-135">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="cc7b4-136">Pacchetti NuGet di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="cc7b4-136">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="cc7b4-137">Per creare app Novell. Forms con EF Core, è necessario installare il pacchetto per i provider di database EF Core da usare come destinazione in tutti i progetti della soluzione Novell. Forms.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-137">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="cc7b4-138">Questa esercitazione usa il provider SQLite.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-138">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="cc7b4-139">Il pacchetto NuGet seguente è necessario in ogni progetto della soluzione Novell. Forms.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-139">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="cc7b4-140">Classi modello</span><span class="sxs-lookup"><span data-stu-id="cc7b4-140">Model classes</span></span>

<span data-ttu-id="cc7b4-141">Ogni tabella nel database SQLite a cui si accede tramite EF Core viene modellata in una classe.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-141">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="cc7b4-142">In questo esempio vengono usate due classi: `Blog` e, `Post` che è possibile trovare nella `Models` cartella.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-142">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="cc7b4-143">Le classi del modello sono costituite solo da proprietà, che modellano le colonne nel database.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-143">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="cc7b4-144">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="cc7b4-144">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="cc7b4-145">La `Posts` proprietà definisce una relazione padre-figlio tra `Blog` e `Post` .</span><span class="sxs-lookup"><span data-stu-id="cc7b4-145">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="cc7b4-146">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="cc7b4-146">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="cc7b4-147">Le `BlogId` proprietà e sono `Blog` correlate all'oggetto padre `Blog` per l'istanza di `Post` .</span><span class="sxs-lookup"><span data-stu-id="cc7b4-147">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="cc7b4-148">Contesto dati</span><span class="sxs-lookup"><span data-stu-id="cc7b4-148">Data context</span></span>

<span data-ttu-id="cc7b4-149">La `BloggingContext` classe si trova nella `Services` cartella ed eredita dalla `DbContext` classe EF core.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-149">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="cc7b4-150">`DbContext`Viene utilizzato per raggruppare le query e le modifiche del database.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-150">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="cc7b4-151">Entrambe le proprietà in questa classe di tipo `DbSet` vengono usate per operare sulle tabelle sottostanti che rappresentano i Blog e i post.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-151">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="cc7b4-152">`SQLitePCL.Batteries_V2.Init()`È necessario nel costruttore per avviare SQLite in iOS.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-152">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="cc7b4-153">La `OnConfiguring` funzione imposta la posizione del database SQLite nel dispositivo fisico.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-153">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="cc7b4-154">Creazione, lettura, aggiornamento ed eliminazione</span><span class="sxs-lookup"><span data-stu-id="cc7b4-154">Create, read, update & delete</span></span>

<span data-ttu-id="cc7b4-155">Di seguito sono riportate alcune istanze dell'app in cui EF Core viene usato per accedere a SQLite.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-155">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="cc7b4-156">Lettura</span><span class="sxs-lookup"><span data-stu-id="cc7b4-156">Read</span></span>

* <span data-ttu-id="cc7b4-157">Restituisce tutti i record.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-157">Return all records.</span></span>
  * <span data-ttu-id="cc7b4-158">La `OnAppearing` funzione di `BlogsPage.xaml.cs` restituisce tutti i `Blog` record e li archivia in una `List` variabile.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-158">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="cc7b4-159">Restituisce record specifici.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-159">Return specific records.</span></span>
  * <span data-ttu-id="cc7b4-160">La `OnAppearing` funzione di `PostsPage.xaml.cs` restituisce `Post` record che contengono un oggetto specifico `BlogId` .</span><span class="sxs-lookup"><span data-stu-id="cc7b4-160">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="cc7b4-161">Create</span><span class="sxs-lookup"><span data-stu-id="cc7b4-161">Create</span></span>

* <span data-ttu-id="cc7b4-162">Inserire un nuovo record.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-162">Insert a new record.</span></span>
  * <span data-ttu-id="cc7b4-163">La `Save_Clicked` funzione di `AddBlogPage.xaml.cs` inserisce un nuovo `Blog` oggetto nel database SQLite.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-163">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="cc7b4-164">Aggiornamento</span><span class="sxs-lookup"><span data-stu-id="cc7b4-164">Update</span></span>

* <span data-ttu-id="cc7b4-165">Aggiornare un record esistente.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-165">Update an existing record.</span></span>
  * <span data-ttu-id="cc7b4-166">La `Save_Clicked` funzione di `AddPostPage.xaml.cs` Aggiorna un `Blog` oggetto esistente con un nuovo oggetto `Post` .</span><span class="sxs-lookup"><span data-stu-id="cc7b4-166">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="cc7b4-167">Delete</span><span class="sxs-lookup"><span data-stu-id="cc7b4-167">Delete</span></span>

* <span data-ttu-id="cc7b4-168">Eliminare tutti i record con Cascade ai record figlio.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-168">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="cc7b4-169">La `DeleteAll_Clicked` funzione di `BlogsPage.xaml.cs` Elimina tutti i `Blog` record nel database SQLite e sovrappone le eliminazioni a tutti i `Blog` `Post` record figlio.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-169">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="cc7b4-170">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="cc7b4-170">Next steps</span></span>

<span data-ttu-id="cc7b4-171">In questa Guida introduttiva si è appreso come usare un'applicazione Novell. Forms per accedere a un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cc7b4-171">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="cc7b4-172">Altri argomenti Entity Framework Core di interesse per gli sviluppatori Novell:</span><span class="sxs-lookup"><span data-stu-id="cc7b4-172">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="cc7b4-173">Configurazione di un`DbContext`</span><span class="sxs-lookup"><span data-stu-id="cc7b4-173">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="cc7b4-174">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="cc7b4-174">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="cc7b4-175">[Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="cc7b4-175">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
