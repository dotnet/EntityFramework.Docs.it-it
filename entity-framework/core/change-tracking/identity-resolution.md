---
title: Risoluzione di identità-EF Core
description: Risoluzione di più istanze di entità in una singola istanza con valori di chiave primaria
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: 24b2fbeea5f740dd2830676bfe8a49720c2b86a9
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024459"
---
# <a name="identity-resolution-in-ef-core"></a>Risoluzione di identità in EF Core

Un oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> può rilevare solo un'istanza di entità con qualsiasi valore di chiave primaria specificato. Ciò significa che più istanze di un'entità con lo stesso valore di chiave devono essere risolte in una singola istanza. Questa operazione è denominata "risoluzione identità". La risoluzione di identità garantisce che Entity Framework Core (EF Core) stia monitorando un grafo coerente senza ambiguità sulle relazioni o sui valori delle proprietà delle entità.

> [!TIP]
> In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi. Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/IdentityResolutionInEFCore).

## <a name="introduction"></a>Introduzione

Il codice seguente esegue una query per un'entità e quindi tenta di associare un'istanza diversa con lo stesso valore di chiave primaria:

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

L'esecuzione di questo codice comporta l'eccezione seguente:

> System. InvalidOperationException: Impossibile rilevare l'istanza del tipo di entità' Blog ' perché è già stata rilevata un'altra istanza con il valore di chiave ' {ID: 1}'. Quando si collegano le entità esistenti, assicurarsi che venga collegata solo un'istanza dell'entità con un valore di chiave specificato.

EF Core richiede una sola istanza perché:

- I valori delle proprietà possono essere diversi tra più istanze. Quando si aggiorna il database, EF Core necessario stabilire quali valori di proprietà utilizzare.
- Le relazioni con altre entità possono essere diverse tra più istanze. Ad esempio, "bloga" può essere correlato a una raccolta di post diversa da "blogB".

L'eccezione precedente viene comunemente rilevata nelle situazioni seguenti:

- Quando si tenta di aggiornare un'entità
- Quando si tenta di tenere traccia di un grafico serializzato di entità
- Quando non si riesce a impostare un valore di chiave che non viene generato automaticamente
- Quando si riutilizza un'istanza di DbContext per più unità di lavoro

Ognuna di queste situazioni viene illustrata nelle sezioni riportate di seguito.

## <a name="updating-an-entity"></a>Aggiornamento di un'entità

Sono disponibili diversi approcci per aggiornare un'entità con nuovi valori, come analizzati in [rilevamento modifiche in EF Core](xref:core/change-tracking/index) e [Rilevamento esplicito delle entità](xref:core/change-tracking/explicit-tracking). Questi approcci sono descritti di seguito nel contesto della risoluzione delle identità. Un punto importante da notare è che ognuno degli approcci utilizza una query o una chiamata a uno di `Update` o `Attach` , ma **_mai entrambi_**.

### <a name="call-update"></a>Chiama aggiornamento

Spesso l'entità da aggiornare non proviene da una query in DbContext che verrà usata per SaveChanges. In un'applicazione Web, ad esempio, è possibile creare un'istanza dell'entità dalle informazioni contenute in una richiesta POST. Il modo più semplice per gestire questo problema consiste nell'usare <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> o <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> . Ad esempio:

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

In questo caso:

- Viene creata solo una singola istanza dell'entità.
- L'istanza dell'entità **non** viene sottoposta a query dal database durante l'esecuzione dell'aggiornamento.
- Tutti i valori delle proprietà verranno aggiornati nel database, indipendentemente dal fatto che siano stati effettivamente modificati o meno.
- Viene eseguito un round trip del database.

### <a name="query-then-apply-changes"></a>Query che applicano le modifiche

In genere non è noto quali valori di proprietà sono stati effettivamente modificati quando un'entità viene creata dalle informazioni in una richiesta POST o simile. Spesso è sufficiente aggiornare tutti i valori nel database, come nell'esempio precedente. Tuttavia, se l'applicazione gestisce molte entità e solo un numero ridotto di tali entità ha modifiche effettive, potrebbe essere utile limitare gli aggiornamenti inviati. Questa operazione può essere eseguita eseguendo una query per tenere traccia delle entità al momento esistenti nel database e quindi applicando le modifiche alle entità rilevate. Ad esempio:

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

In questo caso:

- Viene tenuta traccia solo di una singola istanza dell'entità. quello restituito dal database dalla `Find` query.
- `Update`, `Attach` e così via **non** vengono usati.
- Solo i valori delle proprietà effettivamente modificati vengono aggiornati nel database.
- Vengono eseguiti due round trip del database.

EF Core dispone di alcuni helper per il trasferimento dei valori delle proprietà come questo. Ad esempio, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> copia tutti i valori dall'oggetto specificato e li imposta nell'oggetto rilevato:

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

`SetValues` accetta vari tipi di oggetto, inclusi DTO (Data Transfer Objects) con nomi di proprietà che corrispondono alle proprietà del tipo di entità. Ad esempio:

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

O un dizionario con voci nome/valore per i valori delle proprietà:

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

Per ulteriori informazioni sull'utilizzo di valori di proprietà come questo, vedere [accesso alle entità registrate](xref:core/change-tracking/entity-entries) .

### <a name="use-original-values"></a>Usa valori originali

Fino a questo punto, ogni approccio ha eseguito una query prima di eseguire l'aggiornamento o ha aggiornato tutti i valori delle proprietà indipendentemente dal fatto che siano stati modificati o meno. Per aggiornare solo i valori che sono stati modificati senza eseguire query come parte dell'aggiornamento, sono necessarie informazioni specifiche sui valori della proprietà modificati. Un modo comune per ottenere queste informazioni consiste nell'inviare di nuovo i valori correnti e originali in HTTP post o simili. Ad esempio:

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

In questo codice l'entità con i valori modificati viene inizialmente collegata. Questo fa in modo che EF Core tenga traccia dell'entità nello `Unchanged` stato, ovvero senza alcun valore di proprietà contrassegnato come modificato. Il dizionario dei valori originali viene quindi applicato a questa entità rilevata. Si contrassegna come proprietà modificate con valori correnti e originali diversi. Le proprietà con gli stessi valori correnti e originali non verranno contrassegnate come modificate.

In questo caso:

- Viene rilevata solo una singola istanza dell'entità utilizzando Connetti.
- L'istanza dell'entità **non** viene sottoposta a query dal database durante l'esecuzione dell'aggiornamento.
- L'applicazione dei valori originali garantisce che solo i valori delle proprietà effettivamente modificati vengano aggiornati nel database.
- Viene eseguito un round trip del database.

Come per gli esempi nella sezione precedente, i valori originali non devono essere passati come dizionario. funzionerà anche un'istanza di entità o un DTO.

> [!TIP]
> Sebbene questo approccio abbia caratteristiche accattivanti, richiede l'invio dei valori originali dell'entità da e verso il client Web. Valutare attentamente se questa complessità aggiuntiva è utile per i vantaggi. per molte applicazioni uno degli approcci più semplici è più pragmatico.

## <a name="attaching-a-serialized-graph"></a>Associazione di un grafo serializzato

EF Core funziona con grafici di entità connesse tramite chiavi esterne e proprietà di navigazione, come descritto in [modifica di chiavi esterne e spostamenti](xref:core/change-tracking/relationship-changes). Se questi grafici vengono creati all'esterno di EF Core usando, ad esempio, da un file JSON, possono avere più istanze della stessa entità. Questi duplicati devono essere risolti in istanze singole prima che il grafico possa essere rilevato.

### <a name="graphs-with-no-duplicates"></a>Grafici senza duplicati

Prima di continuare, è importante riconoscere quanto segue:

- I serializzatori spesso dispongono di opzioni per la gestione di cicli e istanze duplicate nel grafo.
- La scelta dell'oggetto usato come radice del grafico può spesso contribuire alla riduzione o alla rimozione di duplicati.

Se possibile, utilizzare le opzioni di serializzazione e scegliere le radici che non comportano duplicati. Ad esempio, il codice seguente usa [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/) per serializzare un elenco di Blog ciascuno con i post associati:

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

Il codice JSON generato da questo codice è:

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

Si noti che nel codice JSON non sono presenti Blog o post duplicati. Ciò significa che le chiamate semplici a `Update` funzioneranno per aggiornare queste entità nel database:

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a>Gestione dei duplicati

Il codice dell'esempio precedente ha serializzato ogni blog con i relativi post associati. Se questo viene modificato per serializzare ogni post con il Blog associato, i duplicati vengono introdotti nel JSON serializzato. Ad esempio:

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

Il codice JSON serializzato è ora simile al seguente:

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

Si noti che il grafico include ora più istanze di Blog con lo stesso valore di chiave, oltre a più istanze post con lo stesso valore di chiave. Il tentativo di tenere traccia di questo grafico come nell'esempio precedente genererà:

> System. InvalidOperationException: Impossibile rilevare l'istanza del tipo di entità "post" perché è già stata rilevata un'altra istanza con il valore di chiave "{ID: 2}". Quando si collegano le entità esistenti, assicurarsi che venga collegata solo un'istanza dell'entità con un valore di chiave specificato.

È possibile risolvere il problema in due modi:

- Usare le opzioni di serializzazione JSON che conservano i riferimenti
- Eseguire la risoluzione dell'identità mentre è in corso il rilevamento del grafico

#### <a name="preserve-references"></a>Mantenere i riferimenti

Json.NET offre la `PreserveReferencesHandling` possibilità di gestire questa operazione. Ad esempio:

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

Il codice JSON risultante è ora simile al seguente:

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

Si noti che questo JSON ha sostituito i duplicati con riferimenti come `"$ref": "5"` che fanno riferimento all'istanza già esistente nel grafico. È possibile tenere traccia di questo grafico utilizzando le semplici chiamate a `Update` , come illustrato in precedenza.

Il <xref:System.Text.Json> supporto nelle librerie di classi di base .NET (BCL) ha un'opzione simile che produce lo stesso risultato. Ad esempio:

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a>Risolvere i duplicati

Se non è possibile eliminare i duplicati nel processo di serializzazione, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> fornisce un modo per gestire questa operazione. TrackGraph funziona come `Add` `Attach` e, `Update` ad eccezione del fatto che genera un callback per ogni istanza di entità prima di tenerne traccia. Questo callback può essere usato per tenere traccia dell'entità o ignorarla. Ad esempio:

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

Per ogni entità nel grafico, il codice seguente:

- Trovare il tipo di entità e il valore della chiave dell'entità
- Ricerca dell'entità con questa chiave nello strumento di rilevamento delle modifiche
  - Se l'entità viene trovata, non viene eseguita alcuna azione aggiuntiva perché l'entità è un duplicato
  - Se l'entità non viene trovata, viene rilevata impostando lo stato su `Modified`

L'output dell'esecuzione del codice è:

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> Questo codice **presuppone che tutti i duplicati siano identici**. In questo modo è possibile scegliere arbitrariamente uno dei duplicati da rilevare durante l'eliminazione degli altri. Se i duplicati possono essere diversi, il codice dovrà decidere come determinare quale usare e come combinare insieme i valori di proprietà e di navigazione.

> [!NOTE]
> Per semplicità, questo codice presuppone che ogni entità disponga di una proprietà di chiave primaria denominata `Id` . Questo può essere codificato in una classe di base astratta o in un'interfaccia. In alternativa, è possibile ottenere la proprietà o le proprietà della chiave primaria dai <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadati in modo che questo codice funzioni con qualsiasi tipo di entità.

## <a name="failing-to-set-key-values"></a>Errore di impostazione dei valori di chiave

I tipi di entità sono spesso configurati per l'utilizzo di [valori di chiave generati automaticamente](xref:core/modeling/generated-properties). Si tratta dell'impostazione predefinita per le proprietà Integer e GUID di chiavi non composte. Tuttavia, se il tipo di entità non è configurato per l'utilizzo di valori di chiave generati automaticamente, è necessario impostare un valore di chiave esplicito prima di tenere traccia dell'entità. Ad esempio, usando il tipo di entità seguente:

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

Si consideri il codice che tenta di rilevare due nuove istanze di entità senza impostare i valori delle chiavi:

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

Questo codice genererà:

> System. InvalidOperationException: Impossibile rilevare l'istanza del tipo di entità' Pet ' perché è già stata rilevata un'altra istanza con il valore di chiave ' {ID: 0}'. Quando si collegano le entità esistenti, assicurarsi che venga collegata solo un'istanza dell'entità con un valore di chiave specificato.

Per risolvere il problema, impostare i valori di chiave in modo esplicito o configurare la proprietà chiave per l'utilizzo di valori di chiave generati. Per ulteriori informazioni, vedere [valori generati](xref:core/modeling/generated-properties) .

## <a name="overusing-a-single-dbcontext-instance"></a>Sovrautilizzo di una singola istanza di DbContext

<xref:Microsoft.EntityFrameworkCore.DbContext> è progettato per rappresentare un'unità di lavoro di breve durata, come descritto in [inizializzazione e configurazione di DbContext](xref:core/dbcontext-configuration/index), ed è stato elaborato in [rilevamento modifiche in EF Core](xref:core/change-tracking/index). Non seguendo queste linee guida è facile eseguire situazioni in cui viene effettuato un tentativo di tenere traccia di più istanze della stessa entità. Esempi comuni:

- Utilizzando la stessa istanza di DbContext per impostare lo stato di test ed eseguire il test. Ciò comporta spesso l'DbContext ancora il rilevamento di un'istanza dell'entità dalla configurazione del test, tentando quindi di alleghiare una nuova istanza nel test. Usare invece un'istanza di DbContext diversa per impostare lo stato di test e il codice di test appropriato.
- Uso di un'istanza di DbContext condivisa in un repository o codice simile. Assicurarsi invece che il repository usi una singola istanza di DbContext per ogni unità di lavoro.

## <a name="identity-resolution-and-queries"></a>Risoluzione di identità e query

La risoluzione delle identità viene eseguita automaticamente quando le entità vengono rilevate da una query. Ciò significa che se un'istanza di entità con un valore di chiave specificato è già rilevata, viene utilizzata l'istanza rilevata esistente anziché creare una nuova istanza. Questa operazione ha una conseguenza importante: se i dati sono stati modificati nel database, questo non verrà riflesso nei risultati della query. Questo è un buon motivo per usare una nuova istanza di DbContext per ogni unità di lavoro, come descritto in [inizializzazione e configurazione di DbContext](xref:core/dbcontext-configuration/index), ed è stata elaborata in [rilevamento modifiche in EF Core](xref:core/change-tracking/index).

> [!IMPORTANT]
> È importante comprendere che EF Core esegue sempre una query LINQ su un DbSet sul database e restituisce solo i risultati in base a ciò che è presente nel database. Tuttavia, per una query di rilevamento, se le entità restituite sono già rilevate, vengono utilizzate le istanze rilevate anziché creare istanze dai dati nel database.

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> in alternativa <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> , è possibile usare se le entità rilevate devono essere aggiornate con i dati più recenti del database. Per ulteriori informazioni, vedere [accesso alle entità registrate](xref:core/change-tracking/entity-entries) .

A differenza delle query di rilevamento, le query senza rilevamento non eseguono la risoluzione delle identità. Ciò significa che le query senza rilevamento possono restituire duplicati proprio come nel caso della serializzazione JSON descritta in precedenza. Questo non è in genere un problema se i risultati della query verranno serializzati e inviati al client.

> [!TIP]
> Non eseguire periodicamente una query senza rilevamento e quindi alleghi le entità restituite allo stesso contesto. Questa operazione sarà più lenta e più difficile da ottenere rispetto all'utilizzo di una query di rilevamento.

Le query senza rilevamento non eseguono la risoluzione delle identità perché questa operazione influisca sulle prestazioni di streaming di un numero elevato di entità da una query. Questo è dovuto al fatto che la risoluzione delle identità richiede il rilevamento di ogni istanza restituita in modo che possa essere utilizzata anziché creare un duplicato in un secondo momento.

A partire da EF Core 5,0, è possibile forzare l'esecuzione di query senza rilevamento per eseguire la risoluzione dell'identità tramite <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> . La query terrà quindi traccia delle istanze restituite (senza verificarle in modo normale) e assicurarsi che non vengano creati duplicati nei risultati della query.

## <a name="overriding-object-equality"></a>Override dell'uguaglianza di oggetti

EF Core usa l' [uguaglianza dei riferimenti](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) durante il confronto di istanze di entità. Questa situazione si verifica anche se i tipi di entità sostituiscono <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> o modificano in altro modo l'uguaglianza degli oggetti. Tuttavia, esiste un'unica posizione in cui l'uguaglianza di override può influisca sul comportamento EF Core: quando le esplorazioni della raccolta usano l'uguaglianza sottoposta a override anziché l'uguaglianza dei riferimenti, e quindi segnalano più istanze come uguali.

Per questo motivo è consigliabile evitare di eseguire l'override dell'uguaglianza delle entità. Se viene usato, assicurarsi di creare le esplorazioni della raccolta che forzano l'uguaglianza di riferimenti. Ad esempio, creare un operatore di confronto di uguaglianza che usa l'uguaglianza dei riferimenti:

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

(A partire da .NET 5, questo è incluso in BCL come <xref:System.Collections.Generic.ReferenceEqualityComparer> ).

Questo operatore di confronto può quindi essere utilizzato durante la creazione di spostamenti della raccolta. Ad esempio:

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a>Confronto tra proprietà chiave

Oltre ai confronti di uguaglianza, è necessario ordinare anche i valori di chiave. Questo è importante per evitare deadlock quando si aggiornano più entità in una singola chiamata a SaveChanges. Tutti i tipi utilizzati per le proprietà di chiave primaria, alternativa o esterna, nonché quelli utilizzati per gli indici univoci, devono implementare <xref:System.IComparable%601> e <xref:System.IEquatable%601> . I tipi normalmente usati come chiavi (int, Guid, String e così via) supportano già tali interfacce. I tipi di chiave personalizzati possono aggiungere queste interfacce.
