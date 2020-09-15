---
title: Dati correlati e serializzazione-EF Core
description: Informazioni sul modo in cui i cicli nei dati correlati con Entity Framework Core possono influenzare i Framework di serializzazione
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 6b28a1fc79a8c23e5249d36386c8ed06805dbe30
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078939"
---
# <a name="related-data-and-serialization"></a><span data-ttu-id="35451-103">Dati correlati e serializzazione</span><span class="sxs-lookup"><span data-stu-id="35451-103">Related data and serialization</span></span>

<span data-ttu-id="35451-104">Poiché EF Core corregge automaticamente le proprietà di navigazione, è possibile finire con i cicli nell'oggetto grafico.</span><span class="sxs-lookup"><span data-stu-id="35451-104">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="35451-105">Ad esempio, il caricamento di un blog e dei post correlati risulterà in un oggetto blog che fa riferimento a una raccolta di post.</span><span class="sxs-lookup"><span data-stu-id="35451-105">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="35451-106">Ognuno di tali post avrà un riferimento al blog.</span><span class="sxs-lookup"><span data-stu-id="35451-106">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="35451-107">Alcuni framework di serializzazione non consentono tali cicli.</span><span class="sxs-lookup"><span data-stu-id="35451-107">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="35451-108">Se, ad esempio, viene trovato un ciclo, Json.NET genererà l'eccezione seguente.</span><span class="sxs-lookup"><span data-stu-id="35451-108">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="35451-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: ciclo autoreferenziale rilevato per la proprietà 'Blog' con tipo 'MyApplication.Models.Blog')</span><span class="sxs-lookup"><span data-stu-id="35451-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="35451-110">Se si usa ASP.NET Core, è possibile configurare Json.NET in modo da ignorare i cicli trovati nell'oggetto grafico.</span><span class="sxs-lookup"><span data-stu-id="35451-110">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="35451-111">Questa configurazione viene eseguita nel `ConfigureServices(...)` metodo in `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="35451-111">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="35451-112">In alternativa è possibile aggiungere l'attributo `[JsonIgnore]` a una proprietà di navigazione, per indicare a Json.NET di non attraversare la proprietà di navigazione durante la serializzazione.</span><span class="sxs-lookup"><span data-stu-id="35451-112">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
