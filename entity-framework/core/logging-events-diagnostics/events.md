---
title: Eventi .NET-EF Core
description: Eventi .NET definiti da EF Core
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 51c0bba5cf25e1d9ddd1fd9aebea50b9a03481a3
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635692"
---
# <a name="net-events-in-ef-core"></a>Eventi .NET in EF Core

> [!TIP]  
> È possibile [scaricare l'esempio di eventi](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) da GitHub.

Entity Framework Core (EF Core) espone [gli eventi .NET](/dotnet/standard/events/) da utilizzare come callback quando si verificano determinate operazioni nel codice EF core. Gli eventi sono più semplici degli [intercettori](xref:core/logging-events-diagnostics/interceptors) e consentono una registrazione più flessibile. Tuttavia, sono solo sincronizzati e pertanto non possono eseguire operazioni di I/O asincrone non bloccanti.

Gli eventi vengono registrati per ogni `DbContext` istanza. Usare un [listener di diagnostica](xref:core/logging-events-diagnostics/diagnostic-listeners) per ottenere le stesse informazioni, ma per tutte le istanze di DbContext nel processo.

## <a name="events-raised-by-ef-core"></a>Eventi generati da EF Core

Gli eventi seguenti vengono generati da EF Core:

| Event | Versione introdotta | Quando viene generato
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | 5.0 | All'inizio di <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | 5.0 | Alla fine di un'operazione riuscita <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | 5.0 | Alla fine di un errore <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> o <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | 2.1 | Quando un'entità viene rilevata dal contesto
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | 2.1 | Modifica dello stato di un'entità rilevata

### <a name="example-timestamp-state-changes"></a>Esempio: modifiche allo stato del timestamp

Ogni entità rilevata da un DbContext dispone di un oggetto <xref:Microsoft.EntityFrameworkCore.EntityState> . Lo stato indica, ad esempio, `Added` che l'entità verrà inserita nel database.

In questo esempio vengono usati gli <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> eventi e per rilevare la modifica dello stato di un'entità. Viene quindi timbrata l'entità con l'ora corrente che indica quando si è verificata questa modifica. Ciò comporta timestamp che indicano quando l'entità è stata inserita, eliminata e/o ultimo aggiornamento.

I tipi di entità in questo esempio implementano un'interfaccia che definisce le proprietà timestamp:

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

Un metodo nel DbContext dell'applicazione può quindi impostare i timestamp per qualsiasi entità che implementa questa interfaccia:

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

Questo metodo ha la firma appropriata da usare come gestore eventi per gli `Tracked` `StateChanged` eventi e. Il gestore viene registrato per entrambi gli eventi nel costruttore DbContext. Si noti che gli eventi possono essere associati a un DbContext in qualsiasi momento; non è necessario che questo avvenga nel costruttore di contesto.

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

Entrambi gli eventi sono necessari perché le nuove entità generano `Tracked` eventi quando vengono rilevati per la prima volta. `StateChanged` gli eventi vengono generati solo per le entità che cambiano lo stato mentre sono _già_ rilevate.

L' [esempio per questo esempio contiene](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) una semplice applicazione console che apporta modifiche al database blogging:

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

L'output di questo codice mostra le modifiche di stato che si verificano e i timestamp applicati:

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
