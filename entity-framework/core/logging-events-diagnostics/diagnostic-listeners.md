---
title: Uso di listener di diagnostica-EF Core
description: Uso di DiagnosticListener per il consumo globale di EF Core Diagnostics
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: dba82a910e2b551e692f37d721d41968981849cf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431538"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a>Uso di listener di diagnostica in EF Core

> [!TIP]  
> È possibile [scaricare l'esempio di questo articolo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) da GitHub.

I listener di diagnostica consentono l'ascolto di qualsiasi evento EF Core che si verifica nel processo .NET corrente. La <xref:System.Diagnostics.DiagnosticListener> classe fa parte di un [meccanismo comune in .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) per ottenere informazioni di diagnostica dalle applicazioni in esecuzione.

I listener di diagnostica non sono adatti per ottenere gli eventi da una singola istanza di DbContext. EF Core gli [intercettori](xref:core/logging-events-diagnostics/interceptors) forniscono l'accesso agli stessi eventi con registrazione per contesto.

I listener di diagnostica non sono progettati per la registrazione. Si consiglia di usare la [registrazione semplice](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) per la registrazione.

## <a name="example-observing-diagnostic-events"></a>Esempio: osservazione degli eventi di diagnostica

La risoluzione di eventi EF Core è un processo in due passaggi. Innanzitutto, è necessario creare un [Observer](/dotnet/standard/events/observer-design-pattern) per `DiagnosticListener` se stesso:

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted() 
        => throw new NotImplementedException();
    
    public void OnError(Exception error) 
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

Il `OnNext` metodo cerca il DiagnosticListener che deriva da EF core. Questo listener ha il nome "Microsoft. EntityFrameworkCore", che può essere ottenuto dalla <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> classe come illustrato.

Questo Observer deve quindi essere registrato a livello globale, ad esempio nel metodo dell'applicazione `Main` :

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

In secondo luogo, una volta individuato il EF Core DiagnosticListener, viene creato un nuovo Observer chiave-valore per sottoscrivere gli eventi EF Core effettivi. Esempio:

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted() 
        => throw new NotImplementedException();
    
    public void OnError(Exception error) 
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

Il `OnNext` metodo è questa volta chiamato con una coppia chiave/valore per ogni evento EF core. La chiave è il nome dell'evento, che può essere ottenuto da uno dei seguenti:

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> per gli eventi comuni a tutti i provider di database EF Core
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> per gli eventi comuni a tutti i provider di database relazionali
* Classe simile per gli eventi specifici del provider di database corrente. Ad esempio, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> per il provider di SQL Server.

Il valore della coppia chiave/valore è un tipo di payload specifico dell'evento. Il tipo di payload da prevedere è documentato su ogni evento definito in queste classi di evento.

Il codice precedente, ad esempio, gestisce gli <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> eventi e. Per la prima di queste, il payload è <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> . Per il secondo, è <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .

> [!TIP]
> Viene eseguito l'override di ToString in ogni classe di dati di evento EF Core per generare il messaggio di log equivalente per l'evento. Ad esempio, la chiamata di `ContextInitializedEventData.ToString` genera "Entity Framework Core 5.0.0-RC. 2.20475.6 ha inizializzato" BlogsContext "utilizzando il provider" Microsoft. EntityFrameworkCore. sqlite "con options: None".

L' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) contiene una semplice applicazione console che apporta modifiche al database blogging e stampa gli eventi di diagnostica rilevati.

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion
        
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

L'output di questo codice Mostra gli eventi rilevati:

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
