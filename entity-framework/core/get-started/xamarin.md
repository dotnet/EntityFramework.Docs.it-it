---
title: Introduzione con EF Core e Novell-EF Core
description: Esercitazione introduttiva per l'uso di Novell per creare app per dispositivi mobili con Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 0552038d471e294834bed9e3bf1f05fd74c39192
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062542"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="f080c-103">Introduzione con EF Core e Novell</span><span class="sxs-lookup"><span data-stu-id="f080c-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="f080c-104">In questa esercitazione si creerà un'applicazione [Novell. Forms](/xamarin/get-started/what-is-xamarin-forms) che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f080c-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="f080c-105">È possibile seguire l'esercitazione con Visual Studio in Windows o Visual Studio per Mac.</span><span class="sxs-lookup"><span data-stu-id="f080c-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="f080c-106">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="f080c-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f080c-107">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="f080c-107">Prerequisites</span></span>

<span data-ttu-id="f080c-108">Installare uno dei seguenti:</span><span class="sxs-lookup"><span data-stu-id="f080c-108">Install one of the below:</span></span>

* <span data-ttu-id="f080c-109">[Visual Studio 2019 versione 16,3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:</span><span class="sxs-lookup"><span data-stu-id="f080c-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="f080c-110">**Sviluppo di app per dispositivi mobili con .NET**</span><span class="sxs-lookup"><span data-stu-id="f080c-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="f080c-111">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="f080c-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="f080c-112">In questa [documentazione vengono fornite istruzioni dettagliate](/xamarin/get-started/installation) per l'installazione di ogni piattaforma.</span><span class="sxs-lookup"><span data-stu-id="f080c-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="f080c-113">Scaricare ed eseguire il progetto di esempio</span><span class="sxs-lookup"><span data-stu-id="f080c-113">Download and run the sample project</span></span>

<span data-ttu-id="f080c-114">Per eseguire ed esplorare questa applicazione di esempio, scaricare il codice in GitHub.</span><span class="sxs-lookup"><span data-stu-id="f080c-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="f080c-115">Al termine del download, aprire il file della soluzione `EFGettingStarted.sln` in Visual Studio o Visual Studio per Mac ed eseguire l'applicazione nella piattaforma di propria scelta.</span><span class="sxs-lookup"><span data-stu-id="f080c-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="f080c-116">Quando l'app viene avviata per la prima volta, il database SQLite locale viene popolato con due voci che rappresentano i Blog.</span><span class="sxs-lookup"><span data-stu-id="f080c-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Screenshot della pagina dell'elenco dei Blog](_static/xamarin-tutorial-1.png)

<span data-ttu-id="f080c-118">Fare clic sul pulsante **Aggiungi** sulla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="f080c-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="f080c-119">Verrà visualizzata una nuova pagina che consente di immettere informazioni su un nuovo blog.</span><span class="sxs-lookup"><span data-stu-id="f080c-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![Screenshot della pagina di modifica del nuovo Blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="f080c-121">Compilare tutte le informazioni e fare clic su **Salva** sulla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="f080c-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="f080c-122">Il nuovo blog sarà salvato nel database SQLite dell'app e sarà visualizzato nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="f080c-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="f080c-123">È possibile fare clic su una delle voci di Blog nell'elenco e visualizzare tutti i post del Blog.</span><span class="sxs-lookup"><span data-stu-id="f080c-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Screenshot della pagina di elenco dei post di Blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="f080c-125">Fare clic su **Aggiungi** nella barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="f080c-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="f080c-126">Viene visualizzata una pagina che consente di compilare informazioni su un nuovo post di Blog.</span><span class="sxs-lookup"><span data-stu-id="f080c-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Screenshot della pagina Aggiungi nuova post](_static/xamarin-tutorial-4.png)

<span data-ttu-id="f080c-128">Compilare tutte le informazioni e fare clic su **Salva** sulla barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="f080c-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="f080c-129">Il nuovo post verrà associato al post di Blog su cui è stato fatto clic in un passaggio precedente e verrà salvato nel database SQLite dell'app e visualizzato nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="f080c-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="f080c-130">Tornare alla pagina dell'elenco dei Blog.</span><span class="sxs-lookup"><span data-stu-id="f080c-130">Go back to the blog list page.</span></span> <span data-ttu-id="f080c-131">E fare clic su **Elimina tutto** nella barra degli strumenti.</span><span class="sxs-lookup"><span data-stu-id="f080c-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="f080c-132">Tutti i Blog e i post corrispondenti verranno eliminati dal database SQLite dell'app.</span><span class="sxs-lookup"><span data-stu-id="f080c-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Screenshot dell'app con tutti i Blog eliminati](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="f080c-134">Esplorare il codice</span><span class="sxs-lookup"><span data-stu-id="f080c-134">Explore the code</span></span>

<span data-ttu-id="f080c-135">Le sezioni seguenti illustrano il codice nel progetto di esempio che legge, crea, aggiorna ed Elimina i dati da un database SQLite usando EF Core con Novell. Forms.</span><span class="sxs-lookup"><span data-stu-id="f080c-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="f080c-136">Si presuppone che l'utente abbia familiarità con gli argomenti di Novell. Forms relativi alla [visualizzazione dei dati](/xamarin/xamarin-forms/app-fundamentals/data-binding/) e all' [esplorazione tra le pagine](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="f080c-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f080c-137">Entity Framework Core usa la reflection per richiamare le funzioni che il linker Novell. iOS può rimuovere durante le configurazioni della modalità di **rilascio** .</span><span class="sxs-lookup"><span data-stu-id="f080c-137">Entity Framework Core uses reflection to invoke functions which the Xamarin.iOS linker may strip out while in **Release** mode configurations.</span></span> <span data-ttu-id="f080c-138">È possibile evitare questo in uno dei due modi.</span><span class="sxs-lookup"><span data-stu-id="f080c-138">You can avoid that in one of two ways.</span></span>
> 
> * <span data-ttu-id="f080c-139">Il primo consiste nell'aggiungere `--linkskip System.Core` agli **argomenti mTouch aggiuntivi** nelle opzioni di **compilazione iOS** .</span><span class="sxs-lookup"><span data-stu-id="f080c-139">The first is to add `--linkskip System.Core` to the **Additional mtouch arguments** in the **iOS Build** options.</span></span>
> * <span data-ttu-id="f080c-140">In alternativa, impostare il comportamento del **linker** Novell. iOS su `Don't Link` nelle opzioni di **compilazione iOS** .</span><span class="sxs-lookup"><span data-stu-id="f080c-140">Alternatively set the Xamarin.iOS **Linker behavior** to `Don't Link` in the **iOS Build** options.</span></span>
> <span data-ttu-id="f080c-141">[Questo articolo illustra altre informazioni sul linker Novell. iOS](/xamarin/ios/deploy-test/linker) , che include come impostare il comportamento in Novell. iOS.</span><span class="sxs-lookup"><span data-stu-id="f080c-141">[This article explains more about the Xamarin.iOS linker](/xamarin/ios/deploy-test/linker) including how to set the behavior on Xamarin.iOS.</span></span>
> 

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="f080c-142">Pacchetti NuGet di Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f080c-142">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="f080c-143">Per creare app Novell. Forms con EF Core, è necessario installare il pacchetto per i provider di database EF Core da usare come destinazione in tutti i progetti della soluzione Novell. Forms.</span><span class="sxs-lookup"><span data-stu-id="f080c-143">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="f080c-144">Questa esercitazione usa il provider SQLite.</span><span class="sxs-lookup"><span data-stu-id="f080c-144">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="f080c-145">Il pacchetto NuGet seguente è necessario in ogni progetto della soluzione Novell. Forms.</span><span class="sxs-lookup"><span data-stu-id="f080c-145">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="f080c-146">Classi modello</span><span class="sxs-lookup"><span data-stu-id="f080c-146">Model classes</span></span>

<span data-ttu-id="f080c-147">Ogni tabella nel database SQLite a cui si accede tramite EF Core viene modellata in una classe.</span><span class="sxs-lookup"><span data-stu-id="f080c-147">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="f080c-148">In questo esempio vengono usate due classi: `Blog` e, `Post` che è possibile trovare nella `Models` cartella.</span><span class="sxs-lookup"><span data-stu-id="f080c-148">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="f080c-149">Le classi del modello sono costituite solo da proprietà, che modellano le colonne nel database.</span><span class="sxs-lookup"><span data-stu-id="f080c-149">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="f080c-150">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="f080c-150">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="f080c-151">La `Posts` proprietà definisce una relazione padre-figlio tra `Blog` e `Post` .</span><span class="sxs-lookup"><span data-stu-id="f080c-151">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="f080c-152">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="f080c-152">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="f080c-153">Le `BlogId` proprietà e sono `Blog` correlate all'oggetto padre `Blog` per l'istanza di `Post` .</span><span class="sxs-lookup"><span data-stu-id="f080c-153">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="f080c-154">Contesto dati</span><span class="sxs-lookup"><span data-stu-id="f080c-154">Data context</span></span>

<span data-ttu-id="f080c-155">La `BloggingContext` classe si trova nella `Services` cartella ed eredita dalla `DbContext` classe EF core.</span><span class="sxs-lookup"><span data-stu-id="f080c-155">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="f080c-156">`DbContext`Viene utilizzato per raggruppare le query e le modifiche del database.</span><span class="sxs-lookup"><span data-stu-id="f080c-156">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="f080c-157">Entrambe le proprietà in questa classe di tipo `DbSet` vengono usate per operare sulle tabelle sottostanti che rappresentano i Blog e i post.</span><span class="sxs-lookup"><span data-stu-id="f080c-157">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="f080c-158">`SQLitePCL.Batteries_V2.Init()`È necessario nel costruttore per avviare SQLite in iOS.</span><span class="sxs-lookup"><span data-stu-id="f080c-158">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="f080c-159">La `OnConfiguring` funzione imposta la posizione del database SQLite nel dispositivo fisico.</span><span class="sxs-lookup"><span data-stu-id="f080c-159">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="f080c-160">Creazione, lettura, aggiornamento ed eliminazione</span><span class="sxs-lookup"><span data-stu-id="f080c-160">Create, read, update & delete</span></span>

<span data-ttu-id="f080c-161">Di seguito sono riportate alcune istanze dell'app in cui EF Core viene usato per accedere a SQLite.</span><span class="sxs-lookup"><span data-stu-id="f080c-161">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="f080c-162">Lettura</span><span class="sxs-lookup"><span data-stu-id="f080c-162">Read</span></span>

* <span data-ttu-id="f080c-163">Restituisce tutti i record.</span><span class="sxs-lookup"><span data-stu-id="f080c-163">Return all records.</span></span>
  * <span data-ttu-id="f080c-164">La `OnAppearing` funzione di `BlogsPage.xaml.cs` restituisce tutti i `Blog` record e li archivia in una `List` variabile.</span><span class="sxs-lookup"><span data-stu-id="f080c-164">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="f080c-165">Restituisce record specifici.</span><span class="sxs-lookup"><span data-stu-id="f080c-165">Return specific records.</span></span>
  * <span data-ttu-id="f080c-166">La `OnAppearing` funzione di `PostsPage.xaml.cs` restituisce `Post` record che contengono un oggetto specifico `BlogId` .</span><span class="sxs-lookup"><span data-stu-id="f080c-166">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="f080c-167">Create</span><span class="sxs-lookup"><span data-stu-id="f080c-167">Create</span></span>

* <span data-ttu-id="f080c-168">Inserire un nuovo record.</span><span class="sxs-lookup"><span data-stu-id="f080c-168">Insert a new record.</span></span>
  * <span data-ttu-id="f080c-169">La `Save_Clicked` funzione di `AddBlogPage.xaml.cs` inserisce un nuovo `Blog` oggetto nel database SQLite.</span><span class="sxs-lookup"><span data-stu-id="f080c-169">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="f080c-170">Aggiornamento</span><span class="sxs-lookup"><span data-stu-id="f080c-170">Update</span></span>

* <span data-ttu-id="f080c-171">Aggiornare un record esistente.</span><span class="sxs-lookup"><span data-stu-id="f080c-171">Update an existing record.</span></span>
  * <span data-ttu-id="f080c-172">La `Save_Clicked` funzione di `AddPostPage.xaml.cs` Aggiorna un `Blog` oggetto esistente con un nuovo oggetto `Post` .</span><span class="sxs-lookup"><span data-stu-id="f080c-172">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
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

### <a name="delete"></a><span data-ttu-id="f080c-173">Delete</span><span class="sxs-lookup"><span data-stu-id="f080c-173">Delete</span></span>

* <span data-ttu-id="f080c-174">Eliminare tutti i record con Cascade ai record figlio.</span><span class="sxs-lookup"><span data-stu-id="f080c-174">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="f080c-175">La `DeleteAll_Clicked` funzione di `BlogsPage.xaml.cs` Elimina tutti i `Blog` record nel database SQLite e sovrappone le eliminazioni a tutti i `Blog` `Post` record figlio.</span><span class="sxs-lookup"><span data-stu-id="f080c-175">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="f080c-176">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="f080c-176">Next steps</span></span>

<span data-ttu-id="f080c-177">In questa Guida introduttiva si è appreso come usare un'applicazione Novell. Forms per accedere a un database SQLite usando Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f080c-177">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="f080c-178">Altri argomenti Entity Framework Core di interesse per gli sviluppatori Novell:</span><span class="sxs-lookup"><span data-stu-id="f080c-178">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="f080c-179">Configurazione di un `DbContext`</span><span class="sxs-lookup"><span data-stu-id="f080c-179">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="f080c-180">Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="f080c-180">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="f080c-181">[Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)</span><span class="sxs-lookup"><span data-stu-id="f080c-181">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
