---
title: Rilevamento modifiche EF Core
description: Panoramica del rilevamento delle modifiche per EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 8cfa4590af07ec1715eb48ec0c7acb3426b6a6b4
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983261"
---
# <a name="change-tracking-in-ef-core"></a>Rilevamento modifiche in EF Core

Ogni <xref:Microsoft.EntityFrameworkCore.DbContext> istanza tiene traccia delle modifiche apportate alle entità. Queste entità rilevate, a loro volta, comportano la modifica del database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.

In questo documento viene presentata una panoramica del rilevamento delle modifiche Entity Framework Core (EF Core) e del modo in cui è correlato a query e aggiornamenti.

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Per semplicità, in questo documento vengono usati e i riferimenti a metodi sincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> invece i relativi equivalenti asincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> . La chiamata e l'attesa del metodo asincrono possono essere sostituite se non diversamente specificato.

## <a name="how-to-track-entities"></a>Come tenere traccia delle entità

Le istanze di entità vengono rilevate quando sono:

- Restituito da una query eseguita sul database
- Associato in modo esplicito a DbContext da `Add` ,, `Attach` `Update` o metodi simili
- Rilevato come nuove entità connesse a entità registrate esistenti

Le istanze di entità non vengono più rilevate quando:

- Il DbContext è stato eliminato
- Il rilevamento modifiche è stato cancellato (EF Core 5,0 e versioni successive)
- Le entità vengono scollegate in modo esplicito

DbContext è progettato per rappresentare un'unità di lavoro di breve durata, come descritto in [inizializzazione e configurazione di DbContext](xref:core/dbcontext-configuration/index). Questo significa che l'eliminazione di DbContext è _il modo normale_ per arrestare il rilevamento delle entità. In altre parole, la durata di una DbContext deve essere:

1. Creare l'istanza di DbContext
2. Tenere traccia di alcune entità
3. Apportare alcune modifiche alle entità
4. Chiamare SaveChanges per aggiornare il database
5. Eliminare l'istanza di DbContext

> [!TIP]
> Quando si adotta questo approccio, non è necessario cancellare lo strumento di rilevamento delle modifiche o scollegare esplicitamente le istanze dell'entità. Tuttavia, se è necessario scollegare le entità, la chiamata a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> è più efficiente rispetto alla disconnessione delle entità una alla volta.

## <a name="entity-states"></a>Stati di entità

Ogni entità è associata a un oggetto specificato <xref:Microsoft.EntityFrameworkCore.EntityState> :

- `Detached` le entità non vengono rilevate da <xref:Microsoft.EntityFrameworkCore.DbContext> .
- `Added` le entità sono nuove e non sono ancora state inserite nel database. Ciò significa che verranno inserite quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.
- `Unchanged` le entità _non_ sono state modificate dopo che sono state eseguite query dal database. Tutte le entità restituite dalle query sono inizialmente in questo stato.
- `Modified` le entità sono state modificate dopo l'interrogazione dal database. Ciò significa che verranno aggiornati quando viene chiamato SaveChanges.
- `Deleted` le entità sono presenti nel database, ma sono contrassegnate per essere eliminate quando viene chiamato SaveChanges.

EF Core tiene traccia delle modifiche a livello di proprietà. Se, ad esempio, viene modificato solo un valore di proprietà, un aggiornamento del database modificherà solo tale valore. Tuttavia, le proprietà possono essere contrassegnate solo come modificate quando l'entità stessa è nello stato modificato. Oppure, da una prospettiva alternativa, lo stato modificato indica che almeno un valore della proprietà è stato contrassegnato come modificato.

Nella tabella seguente vengono riepilogati i diversi stati:

| Stato dell'entità     | Rilevato da DbContext | Esistente nel database | Proprietà modificate | Azione su SaveChanges
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | No                   | -                  | -                   | -
| `Added`          | Sì                  | No                 | -                   | Insert
| `Unchanged`      | Sì                  | Sì                | No                  | -
| `Modified`       | Sì                  | Sì                | Sì                 | Aggiornamento
| `Deleted`        | Sì                  | Sì                | -                   | Elimina

> [!NOTE]
> Questo testo usa termini di database relazionali per maggiore chiarezza. I database NoSQL in genere supportano operazioni simili, ma possibilmente con nomi diversi. Per ulteriori informazioni, consultare la documentazione del provider di database.

## <a name="tracking-from-queries"></a>Rilevamento da query

EF Core rilevamento delle modifiche funziona meglio quando la stessa <xref:Microsoft.EntityFrameworkCore.DbContext> istanza viene utilizzata per eseguire query per entità e aggiornarle chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Questo perché EF Core rileva automaticamente lo stato delle entità sottoposte a query e quindi rileva eventuali modifiche apportate a tali entità quando viene chiamato SaveChanges.

Questo approccio presenta diversi vantaggi rispetto alla [verifica esplicita delle istanze di entità](xref:core/change-tracking/explicit-tracking):

- È semplice. Raramente gli Stati dell'entità devono essere modificati in modo esplicito. EF Core si occupa delle modifiche di stato.
- Gli aggiornamenti sono limitati solo ai valori effettivamente modificati.
- I valori delle [proprietà shadow](xref:core/modeling/shadow-properties) vengono conservati e utilizzati in base alle esigenze. Questo è particolarmente importante quando le chiavi esterne vengono archiviate in stato di ombreggiatura.
- I valori originali delle proprietà vengono conservati automaticamente e usati per aggiornamenti efficienti.

## <a name="simple-query-and-update"></a>Semplice query e aggiornamento

Si consideri, ad esempio, un semplice modello di Blog/Post:

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

È possibile utilizzare questo modello per eseguire una query per i Blog e i post e quindi per apportare alcuni aggiornamenti al database:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

La chiamata a SaveChanges comporta gli aggiornamenti del database seguenti, usando SQLite come database di esempio:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

La [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) è un ottimo modo per visualizzare quali entità vengono registrate e quali sono gli Stati. Inserire, ad esempio, il codice seguente nell'esempio precedente prima di chiamare SaveChanges:

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

Genera l'output seguente:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

Si noti in particolare:

- La `Blog.Name` proprietà è contrassegnata come modificata ( `Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'` ) e in questo modo il Blog si trova nello `Modified` stato.
- La `Post.Title` proprietà di post 2 è contrassegnata come modificata ( `Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'` ) e ciò comporta lo stato di questo post `Modified` .
- Gli altri valori di proprietà di post 2 non sono stati modificati e pertanto non sono contrassegnati come modificati. Questo è il motivo per cui questi valori non sono inclusi nell'aggiornamento del database.
- L'altro post non è stato modificato in alcun modo. Questo è il motivo per cui si trova ancora nello `Unchanged` stato e non è incluso nell'aggiornamento del database.

## <a name="query-then-insert-update-and-delete"></a>Eseguire una query e inserire, aggiornare ed eliminare

Gli aggiornamenti come quelli nell'esempio precedente possono essere combinati con inserimenti ed eliminazioni nella stessa unità di lavoro. Ad esempio:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

In questo esempio:

- Un blog e i post correlati vengono sottoposti a query dal database e monitorati
- La `Blog.Name` proprietà è stata modificata
- Viene aggiunto un nuovo post alla raccolta dei post esistenti per il Blog
- Un post esistente è contrassegnato per l'eliminazione chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>

Esaminare di nuovo la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) prima di chiamare SaveChanges indica il modo in cui EF Core tiene traccia delle modifiche:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Si noti che:

- Il Blog è contrassegnato come `Modified` . Verrà generato un aggiornamento del database.
- Il post 2 è contrassegnato come `Deleted` . Verrà generata un'eliminazione del database.
- Un nuovo post con un ID temporaneo è associato al Blog 1 ed è contrassegnato come `Added` . Verrà generato un inserimento di database.

In questo modo vengono restituiti i comandi di database seguenti (usando SQLite) quando viene chiamato SaveChanges:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Per ulteriori informazioni sull'inserimento e l'eliminazione di entità, vedere [Rilevamento esplicito delle entità](xref:core/change-tracking/explicit-tracking) . Per ulteriori informazioni su come EF Core rileva automaticamente le modifiche come questa, vedere rilevamento delle modifiche [e notifiche](xref:core/change-tracking/change-detection) .

> [!TIP]
> Chiamare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> per determinare se sono state apportate modifiche che determineranno l'aggiornamento del SaveChanges al database. Se HasChanges restituisce false, SaveChanges sarà un no-op.
