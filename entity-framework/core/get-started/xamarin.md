---
title: Introduzione con EF Core e Novell-EF Core
description: Esercitazione introduttiva per l'uso di Novell per creare app per dispositivi mobili con Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: a08c5582b063f4a7748a43811c2bf28e8f3e5c14
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429910"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a>Introduzione con EF Core e Novell

In questa esercitazione si creerà un'applicazione [Novell. Forms](/xamarin/get-started/what-is-xamarin-forms) che esegue l'accesso ai dati in un database SQLite usando Entity Framework Core.

È possibile seguire l'esercitazione con Visual Studio in Windows o Visual Studio per Mac.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) di questo articolo in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Installare uno dei seguenti:

* [Visual Studio 2019 versione 16,3 o successiva](https://www.visualstudio.com/downloads/) con questo carico di lavoro:
  * **Sviluppo di app per dispositivi mobili con .NET**
* [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/)

In questa [documentazione vengono fornite istruzioni dettagliate](/xamarin/get-started/installation) per l'installazione di ogni piattaforma.

## <a name="download-and-run-the-sample-project"></a>Scaricare ed eseguire il progetto di esempio

Per eseguire ed esplorare questa applicazione di esempio, scaricare il codice in GitHub.

Al termine del download, aprire il file della soluzione `EFGettingStarted.sln` in Visual Studio o Visual Studio per Mac ed eseguire l'applicazione nella piattaforma di propria scelta.

Quando l'app viene avviata per la prima volta, il database SQLite locale viene popolato con due voci che rappresentano i Blog.

![Screenshot della pagina dell'elenco dei Blog](_static/xamarin-tutorial-1.png)

Fare clic sul pulsante **Aggiungi** sulla barra degli strumenti.

Verrà visualizzata una nuova pagina che consente di immettere informazioni su un nuovo blog.

![Screenshot della pagina di modifica del nuovo Blog](_static/xamarin-tutorial-2.png)

Compilare tutte le informazioni e fare clic su **Salva** sulla barra degli strumenti. Il nuovo blog sarà salvato nel database SQLite dell'app e sarà visualizzato nell'elenco.

È possibile fare clic su una delle voci di Blog nell'elenco e visualizzare tutti i post del Blog.

![Screenshot della pagina di elenco dei post di Blog](_static/xamarin-tutorial-3.png)

Fare clic su **Aggiungi** nella barra degli strumenti.

Viene visualizzata una pagina che consente di compilare informazioni su un nuovo post di Blog.

![Screenshot della pagina Aggiungi nuova post](_static/xamarin-tutorial-4.png)

Compilare tutte le informazioni e fare clic su **Salva** sulla barra degli strumenti.

Il nuovo post verrà associato al post di Blog su cui è stato fatto clic in un passaggio precedente e verrà salvato nel database SQLite dell'app e visualizzato nell'elenco.

Tornare alla pagina dell'elenco dei Blog. E fare clic su **Elimina tutto** nella barra degli strumenti. Tutti i Blog e i post corrispondenti verranno eliminati dal database SQLite dell'app.

![Screenshot dell'app con tutti i Blog eliminati](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>Esplorare il codice

Le sezioni seguenti illustrano il codice nel progetto di esempio che legge, crea, aggiorna ed Elimina i dati da un database SQLite usando EF Core con Novell. Forms.

Si presuppone che l'utente abbia familiarità con gli argomenti di Novell. Forms relativi alla [visualizzazione dei dati](/xamarin/xamarin-forms/app-fundamentals/data-binding/) e all' [esplorazione tra le pagine](/xamarin/xamarin-forms/app-fundamentals/navigation/).

> [!IMPORTANT]
> Entity Framework Core usa la reflection per richiamare le funzioni che il linker Novell. iOS può rimuovere durante le configurazioni della modalità di **rilascio** . È possibile evitare questo in uno dei due modi.
>
> * Il primo consiste nell'aggiungere `--linkskip System.Core` agli **argomenti mTouch aggiuntivi** nelle opzioni di **compilazione iOS** .
> * In alternativa, impostare il comportamento del **linker** Novell. iOS su `Don't Link` nelle opzioni di **compilazione iOS** .
> [Questo articolo illustra altre informazioni sul linker Novell. iOS](/xamarin/ios/deploy-test/linker) , che include come impostare il comportamento in Novell. iOS.
>

## <a name="entity-framework-core-nuget-packages"></a>Pacchetti NuGet di Entity Framework Core

Per creare app Novell. Forms con EF Core, è necessario installare il pacchetto per i provider di database EF Core da usare come destinazione in tutti i progetti della soluzione Novell. Forms. Questa esercitazione usa il provider SQLite.

Il pacchetto NuGet seguente è necessario in ogni progetto della soluzione Novell. Forms.

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>Classi modello

Ogni tabella nel database SQLite a cui si accede tramite EF Core viene modellata in una classe. In questo esempio vengono usate due classi: `Blog` e, `Post` che è possibile trovare nella `Models` cartella.

Le classi del modello sono costituite solo da proprietà, che modellano le colonne nel database.

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* La `Posts` proprietà definisce una relazione padre-figlio tra `Blog` e `Post` .

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* Le `BlogId` proprietà e sono `Blog` correlate all'oggetto padre `Blog` per l'istanza di `Post` .

## <a name="data-context"></a>Contesto dati

La `BloggingContext` classe si trova nella `Services` cartella ed eredita dalla `DbContext` classe EF core. `DbContext`Viene utilizzato per raggruppare le query e le modifiche del database.

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* Entrambe le proprietà in questa classe di tipo `DbSet` vengono usate per operare sulle tabelle sottostanti che rappresentano i Blog e i post.
* `SQLitePCL.Batteries_V2.Init()`È necessario nel costruttore per avviare SQLite in iOS.
* La `OnConfiguring` funzione imposta la posizione del database SQLite nel dispositivo fisico.

## <a name="create-read-update--delete"></a>Creazione, lettura, aggiornamento ed eliminazione

Di seguito sono riportate alcune istanze dell'app in cui EF Core viene usato per accedere a SQLite.

### <a name="read"></a>Lettura

* Restituisce tutti i record.
  * La `OnAppearing` funzione di `BlogsPage.xaml.cs` restituisce tutti i `Blog` record e li archivia in una `List` variabile.

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* Restituisce record specifici.
  * La `OnAppearing` funzione di `PostsPage.xaml.cs` restituisce `Post` record che contengono un oggetto specifico `BlogId` .

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>Crea

* Inserire un nuovo record.
  * La `Save_Clicked` funzione di `AddBlogPage.xaml.cs` inserisce un nuovo `Blog` oggetto nel database SQLite.

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>Aggiornamento

* Aggiornare un record esistente.
  * La `Save_Clicked` funzione di `AddPostPage.xaml.cs` Aggiorna un `Blog` oggetto esistente con un nuovo oggetto `Post` .
  
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

### <a name="delete"></a>Elimina

* Eliminare tutti i record con Cascade ai record figlio.
  * La `DeleteAll_Clicked` funzione di `BlogsPage.xaml.cs` Elimina tutti i `Blog` record nel database SQLite e sovrappone le eliminazioni a tutti i `Blog` `Post` record figlio.

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come usare un'applicazione Novell. Forms per accedere a un database SQLite usando Entity Framework Core.

Altri argomenti Entity Framework Core di interesse per gli sviluppatori Novell:

* [Configurazione di un `DbContext`](xref:core/dbcontext-configuration/index)
* Vedere altre informazioni sulle [espressioni di query LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Configurare il modello](xref:core/modeling/index) per specificare elementi come la [lunghezza obbligatoria](xref:core/modeling/entity-properties#required-and-optional-properties) e [massima](xref:core/modeling/entity-properties#maximum-length)
