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
# <a name="related-data-and-serialization"></a>Dati correlati e serializzazione

Poiché EF Core corregge automaticamente le proprietà di navigazione, è possibile finire con i cicli nell'oggetto grafico. Ad esempio, il caricamento di un blog e dei post correlati risulterà in un oggetto blog che fa riferimento a una raccolta di post. Ognuno di tali post avrà un riferimento al blog.

Alcuni framework di serializzazione non consentono tali cicli. Se, ad esempio, viene trovato un ciclo, Json.NET genererà l'eccezione seguente.

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'. (Newtonsoft.Json.JsonSerializationException: ciclo autoreferenziale rilevato per la proprietà 'Blog' con tipo 'MyApplication.Models.Blog')

Se si usa ASP.NET Core, è possibile configurare Json.NET in modo da ignorare i cicli trovati nell'oggetto grafico. Questa configurazione viene eseguita nel `ConfigureServices(...)` metodo in `Startup.cs` .

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

In alternativa è possibile aggiungere l'attributo `[JsonIgnore]` a una proprietà di navigazione, per indicare a Json.NET di non attraversare la proprietà di navigazione durante la serializzazione.
