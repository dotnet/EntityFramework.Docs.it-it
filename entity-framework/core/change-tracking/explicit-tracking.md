---
title: Rilevamento esplicito di entità-EF Core
description: Rilevamento esplicito di entità con DbContext mediante aggiunta, associazione, aggiornamento e rimozione
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 28a6ec3e3c25dad70882b8681f78744a5979efe6
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129744"
---
# <a name="explicitly-tracking-entities"></a>Rilevamento esplicito di entità

Ogni <xref:Microsoft.EntityFrameworkCore.DbContext> istanza tiene traccia delle modifiche apportate alle entità. Queste entità rilevate, a loro volta, comportano la modifica del database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.

Il rilevamento delle modifiche Entity Framework Core (EF Core) funziona meglio quando la stessa <xref:Microsoft.EntityFrameworkCore.DbContext> istanza viene utilizzata per eseguire query per entità e aggiornarle chiamando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Questo perché EF Core rileva automaticamente lo stato delle entità sottoposte a query e quindi rileva eventuali modifiche apportate a tali entità quando viene chiamato SaveChanges. Questo approccio è trattato in [Rilevamento modifiche EF Core](xref:core/change-tracking/index).

> [!TIP]
> In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi. Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Per semplicità, in questo documento vengono usati e i riferimenti a metodi sincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> invece i relativi equivalenti asincroni, ad esempio <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> . La chiamata e l'attesa del metodo asincrono possono essere sostituite se non diversamente specificato.

## <a name="introduction"></a>Introduzione

Le entità possono essere esplicitamente associate a un oggetto in modo <xref:Microsoft.EntityFrameworkCore.DbContext> che il contesto rilevi tali entità. Questa operazione è utile principalmente nei casi seguenti:

1. Creazione di nuove entità che verranno inserite nel database.
2. Ricollegare le entità disconnesse precedentemente sottoposte a query da un'istanza di DbContext _diversa_ .

Il primo di questi saranno necessari per la maggior parte delle applicazioni ed è primario gestito dai <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> metodi.

Il secondo è necessario solo per le applicazioni che modificano le entità o le relazioni **_mentre le entità non vengono rilevate_**. Ad esempio, un'applicazione Web può inviare entità al client Web in cui l'utente apporta modifiche e invia le entità di nuovo. Queste entità sono denominate "disconnesse" perché sono state originariamente sottoposte a query da un DbContext, ma sono state quindi disconnesse da tale contesto quando vengono inviate al client.

L'applicazione Web deve ora ricollegare queste entità in modo che vengano rilevate nuovamente e indichino le modifiche apportate in modo che <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> possano apportare aggiornamenti appropriati al database. Questa operazione viene gestita principalmente dai <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> metodi e.

> [!TIP]
> Il montaggio delle entità nella _stessa istanza di DbContext_ su cui sono state eseguite query non dovrebbe essere normalmente necessario. Non eseguire periodicamente una query senza rilevamento e quindi alleghi le entità restituite allo stesso contesto. Questa operazione risulterà più lenta rispetto all'utilizzo di una query di rilevamento e potrebbe anche causare problemi quali i valori delle proprietà shadow mancanti, rendendo più difficile ottenere il diritto.

### <a name="generated-verses-explicit-key-values"></a>Valori di chiave espliciti dei verse generati

Per impostazione predefinita, le [proprietà di chiave](xref:core/modeling/keys) Integer e GUID sono configurate per l'utilizzo di [valori di chiave generati automaticamente](xref:core/modeling/generated-properties). Questo è un **vantaggio importante per il rilevamento delle modifiche: un valore di chiave non impostato indica che l'entità è "New"**. Per "New", significa che non è stato ancora inserito nel database.

Nelle sezioni seguenti vengono usati due modelli. Il primo è configurato in modo da **non** usare i valori di chiave generati:

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

I valori di chiave non generati (ovvero impostati in modo esplicito) vengono visualizzati per primi in ogni esempio perché tutti gli elementi sono molto espliciti e facili da seguire. Questo viene seguito da un esempio in cui vengono usati i valori di chiave generati:

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

Si noti che le proprietà chiave in questo modello non richiedono alcuna configurazione aggiuntiva perché l'uso di valori di chiave generati è l' [impostazione predefinita per le chiavi Integer semplici](xref:core/modeling/generated-properties).

## <a name="inserting-new-entities"></a>Inserimento di nuove entità

### <a name="explicit-key-values"></a>Valori di chiave espliciti

È necessario tenere traccia di un'entità nello `Added` stato da inserire da <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Le entità vengono in genere inserite nello stato aggiunto chiamando uno dei <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> metodi,, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> o equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> .

> [!TIP]
> Questi metodi funzionano nello stesso modo nel contesto del rilevamento delle modifiche. Per ulteriori informazioni, vedere [ulteriori rilevamento modifiche funzionalità](xref:core/change-tracking/miscellaneous) .

Ad esempio, per iniziare a tenere traccia di un nuovo Blog:

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

Esaminando la [visualizzazione di debug di rilevamento modifiche](xref:core/change-tracking/debug-views) dopo questa chiamata viene indicato che il contesto sta monitorando la nuova entità nello `Added` stato:

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Tuttavia, i metodi Add non funzionano solo su una singola entità. Iniziano effettivamente a tenere traccia _di un intero grafico di entità correlate_, inserendole tutte nello `Added` stato. Ad esempio, per inserire un nuovo blog e i nuovi post associati:

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

Il contesto ora tiene traccia di tutte le entità come `Added` :

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Si noti che sono stati impostati valori espliciti per le `Id` proprietà chiave degli esempi precedenti. Questo perché il modello è stato configurato in modo da usare i valori di chiave impostati in modo esplicito, anziché i valori di chiave generati automaticamente. Quando non si usano chiavi generate, è necessario impostare in modo esplicito le proprietà chiave _prima_ di chiamare `Add` . Questi valori di chiave vengono quindi inseriti quando viene chiamato SaveChanges. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

Tutte queste entità vengono registrate nello `Unchanged` stato dopo il completamento di SaveChanges, dal momento che queste entità sono ora presenti nel database:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a>Valori di chiave generati

Come indicato in precedenza, le [proprietà di chiave](xref:core/modeling/keys) Integer e GUID sono configurate per l'utilizzo dei [valori di chiave generati automaticamente](xref:core/modeling/generated-properties) per impostazione predefinita. Ciò significa che l'applicazione _non deve impostare alcun valore di chiave in modo esplicito_. Ad esempio, per inserire un nuovo blog e pubblicarli tutti con i valori di chiave generati:

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

Come per i valori di chiave espliciti, il contesto ora tiene traccia di tutte le entità come `Added` :

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

Si noti che in questo caso sono stati generati [valori di chiave temporanei](xref:core/change-tracking/miscellaneous#temporary-values) per ogni entità. Questi valori vengono utilizzati da EF Core finché non viene chiamato SaveChanges, a quel punto i valori di chiave reale vengono letti dal database. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Dopo il completamento di SaveChanges, tutte le entità sono state aggiornate con i relativi valori di chiave reale e vengono rilevate nello `Unchanged` stato poiché ora corrispondono allo stato nel database:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Si tratta esattamente dello stesso stato finale dell'esempio precedente in cui venivano utilizzati valori di chiave espliciti.

> [!TIP]
> Un valore di chiave esplicita può comunque essere impostato anche quando si usano valori di chiave generati. EF Core tenterà quindi di inserire usando questo valore di chiave. Alcune configurazioni di database, tra cui SQL Server con colonne Identity, non supportano tali inserimenti e generano.

## <a name="attaching-existing-entities"></a>Associazione di entità esistenti

### <a name="explicit-key-values"></a>Valori di chiave espliciti

Le entità restituite dalle query vengono registrate nello `Unchanged` stato. Lo `Unchanged` stato indica che l'entità non è stata modificata dopo essere stata sottoposta a query. Un'entità disconnessa, forse restituita da un client Web in una richiesta HTTP, può essere inserita in questo stato usando <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> o i metodi equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> . Ad esempio, per iniziare a tenere traccia di un blog esistente:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> Gli esempi qui illustrano come creare entità in modo esplicito con `new` per semplicità. In genere, le istanze dell'entità proverranno da un'altra origine, ad esempio la deserializzazione da un client o la creazione dai dati in un post HTTP.

Il controllo della [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo questa chiamata indica che l'entità è registrata nello `Unchanged` stato:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Proprio come `Add` , `Attach` imposta effettivamente un intero grafico di entità connesse sullo `Unchanged` stato. Ad esempio, per alleghi un blog esistente e i post esistenti associati:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

Il contesto ora tiene traccia di tutte le entità come `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

La chiamata a SaveChanges in questo punto non avrà alcun effetto. Tutte le entità sono contrassegnate come `Unchanged` , pertanto non c'è nulla da aggiornare nel database.

### <a name="generated-key-values"></a>Valori di chiave generati

Come indicato in precedenza, le [proprietà di chiave](xref:core/modeling/keys) Integer e GUID sono configurate per l'utilizzo dei [valori di chiave generati automaticamente](xref:core/modeling/generated-properties) per impostazione predefinita. Questo è un vantaggio importante quando si utilizzano le entità disconnesse: un valore di chiave non impostato indica che l'entità non è ancora stata inserita nel database. Questo consente allo strumento di rilevamento delle modifiche di rilevare automaticamente le nuove entità e inserirle nello `Added` stato. Si consideri ad esempio l'associazione di questo grafico di un blog e post:

```c#
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
```

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

Il Blog ha un valore chiave pari a 1, a indicare che esiste già nel database. Per due dei post sono inoltre impostati i valori di chiave, ma il terzo non lo è. In EF Core il valore della chiave verrà visualizzato come 0, il valore predefinito di CLR per un numero intero. Il risultato è EF Core contrassegnare la nuova entità come `Added` anziché `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

La chiamata a SaveChanges a questo punto non esegue alcuna operazione con le `Unchanged` entità, ma inserisce la nuova entità nel database. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Il punto importante da notare è che, con i valori di chiave generati, EF Core è in grado di **distinguere automaticamente le nuove entità esistenti in un grafo disconnesso**. In breve, quando si usano chiavi generate, EF Core inserisce sempre un'entità quando tale entità non dispone di un valore di chiave impostato.

## <a name="updating-existing-entities"></a>Aggiornamento di entità esistenti

### <a name="explicit-key-values"></a>Valori di chiave espliciti

<xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> e i metodi equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> si comportano esattamente come i `Attach` metodi descritti in precedenza, ad eccezione del fatto che le entità vengono inserite in `Modfied` anziché nello `Unchanged` stato. Ad esempio, per iniziare a tenere traccia di un blog esistente come `Modified` :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

Il controllo della [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo questa chiamata indica che il contesto sta tenendo traccia dell'entità nello `Modified` stato:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

Proprio come con `Add` e `Attach` , `Update` in realtà contrassegna _un intero grafico_ di entità correlate come `Modified` . Ad esempio, per associare un blog esistente a un post esistente associato come `Modified` :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

Il contesto ora tiene traccia di tutte le entità come `Modified` :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

La chiamata a SaveChanges a questo punto provocherà l'invio degli aggiornamenti al database per tutte le entità. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a>Valori di chiave generati

Analogamente a `Attach` , i valori di chiave generati hanno lo stesso vantaggio principale per `Update` : un valore di chiave non impostata indica che l'entità è nuova e non è ancora stata inserita nel database. Come con `Attach` , consente all'DbContext di rilevare automaticamente le nuove entità e inserirle nello `Added` stato. Si consideri ad esempio la chiamata `Update` a con questo grafico di un blog e post:

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

Come per l' `Attach` esempio, il post senza valore di chiave viene rilevato come nuovo e impostato sullo `Added` stato. Le altre entità sono contrassegnate come `Modified` :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

La chiamata a questa fase provocherà l' `SaveChanges` invio degli aggiornamenti al database per tutte le entità esistenti, mentre viene inserita la nuova entità. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Si tratta di un modo molto semplice per generare aggiornamenti e inserimenti da un grafo disconnesso. Tuttavia, il risultato è che gli aggiornamenti o gli inserimenti vengono inviati al database per ogni proprietà di ogni entità rilevata, anche quando alcuni valori di proprietà potrebbero non essere stati modificati. Non è troppo spaventato da questo; per molte applicazioni con piccoli grafici, può trattarsi di un modo semplice e pragmatico di generare gli aggiornamenti. Detto questo, altri modelli più complessi possono a volte causare aggiornamenti più efficienti, come descritto in [risoluzione delle identità in EF Core](xref:core/change-tracking/identity-resolution).

## <a name="deleting-existing-entities"></a>Eliminazione di entità esistenti

Per eliminare un'entità da SaveChanges, è necessario tenere traccia `Deleted` dello stato. Le entità vengono in genere inserite nello `Deleted` stato chiamando uno dei <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> metodi, o equivalenti su <xref:Microsoft.EntityFrameworkCore.DbSet%601> . Ad esempio, per contrassegnare un post esistente come `Deleted` :

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

Il controllo della [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) dopo questa chiamata indica che il contesto sta tenendo traccia dell'entità nello `Deleted` stato:

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

Questa entità verrà eliminata quando viene chiamato SaveChanges. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Dopo il completamento di SaveChanges, l'entità eliminata viene scollegata da DbContext poiché non esiste più nel database. La visualizzazione debug è quindi vuota perché non è stata tenuta traccia delle entità.

## <a name="deleting-dependentchild-entities"></a>Eliminazione di entità dipendenti/figlio

L'eliminazione di entità dipendenti/figlio da un grafo è più semplice dell'eliminazione di entità/entità padre. Per ulteriori informazioni, vedere la sezione successiva e [modificare le chiavi esterne e le navigazioni](xref:core/change-tracking/relationship-changes) .

È insolito chiamare `Remove` su un'entità creata con `new` . Inoltre, a differenza `Add` `Attach` di e `Update` , non è insolito chiamare `Remove` su un'entità che non è già stata rilevata nello `Unchanged` `Modified` stato o. È invece tipico tenere traccia di una singola entità o di un grafico di entità correlate e quindi chiamare `Remove` sulle entità che devono essere eliminate. Questo grafico delle entità rilevate viene in genere creato da uno dei seguenti valori:

1. Esecuzione di una query per le entità
2. Utilizzo dei `Attach` `Update` metodi o su un grafico di entità disconnesse, come descritto nelle sezioni precedenti.

Ad esempio, il codice nella sezione precedente è più probabile che ottenga un post da un client e quindi esegua una simile operazione:

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

Questo comportamento è identico a quello dell'esempio precedente, perché la chiamata `Remove` su un'entità non rilevata ne causa la prima connessione e quindi contrassegnata come `Deleted` .

In esempi più realistici, un grafico delle entità viene prima collegato, quindi alcune di queste entità sono contrassegnate come eliminate. Ad esempio:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

Tutte le entità sono contrassegnate come `Unchanged` , ad eccezione di quella su cui `Remove` è stato chiamato:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

Questa entità verrà eliminata quando viene chiamato SaveChanges. Ad esempio, quando si usa SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Dopo il completamento di SaveChanges, l'entità eliminata viene scollegata da DbContext poiché non esiste più nel database. Le altre entità rimangono nello `Unchanged` stato:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a>Eliminazione di entità/entità padre

Ogni relazione che connette due tipi di entità dispone di un'entità finale o di un elemento padre e un'entità finale dipendente o figlio. L'entità dipendente/figlio è quella con la proprietà della chiave esterna. In una relazione uno-a-molti, l'entità/elemento padre si trova sul lato "uno" e l'oggetto dipendente/figlio si trova sul lato "molti". Per ulteriori informazioni, vedere [relazioni](xref:core/modeling/relationships) .

Negli esempi precedenti è stato eliminato un post, che è un'entità dipendente/figlio nella relazione uno-a-molti dei post di Blog. Questo comportamento è relativamente semplice perché la rimozione di un'entità dipendente/figlio non ha alcun effetto sulle altre entità. D'altra parte, l'eliminazione di un'entità principale/padre deve avere un effetto anche sulle entità dipendenti/figlio. Se non si esegue questa operazione, un valore di chiave esterna farà riferimento a un valore di chiave primaria che non esiste più. Si tratta di uno stato del modello non valido e viene restituito un errore di vincolo referenziale nella maggior parte dei database.

Questo stato del modello non valido può essere gestito in due modi:

1. Impostazione dei valori FK su null. Ciò indica che i dipendenti/elementi figlio non sono più correlati ad alcuna entità/elemento padre. Si tratta dell'impostazione predefinita per le relazioni facoltative in cui la chiave esterna deve ammettere i valori null. L'impostazione di FK su null non è valida per le relazioni obbligatorie, in cui la chiave esterna è in genere non nullable.
2. Eliminazione dei dipendenti/figlio. Si tratta dell'impostazione predefinita per le relazioni obbligatorie ed è valida anche per le relazioni facoltative.

Per informazioni dettagliate sul rilevamento delle modifiche e sulle relazioni, vedere [modifica delle chiavi esterne e degli spostamenti](xref:core/change-tracking/relationship-changes) .

### <a name="optional-relationships"></a>Relazioni facoltative

La `Post.BlogId` proprietà di chiave esterna ammette i valori null nel modello usato. Ciò significa che la relazione è facoltativa e, di conseguenza, il comportamento predefinito di EF Core consiste nell'impostare le `BlogId` proprietà di chiave esterna su null quando il Blog viene eliminato. Ad esempio:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

Esaminando la [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo la chiamata a `Remove` viene mostrato che, come previsto, il Blog è ora contrassegnato come `Deleted` :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

Più interessante, tutti i post correlati sono ora contrassegnati come `Modified` . Questo è dovuto al fatto che la proprietà di chiave esterna in ogni entità è stata impostata su null. La chiamata a SaveChanges aggiorna il valore di chiave esterna per ogni post a null nel database, prima di eliminare il Blog:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

Dopo il completamento di SaveChanges, l'entità eliminata viene scollegata da DbContext poiché non esiste più nel database. Le altre entità sono ora contrassegnate `Unchanged` con valori di chiave esterna null, che corrispondono allo stato del database:

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a>Relazioni obbligatorie

Se la `Post.BlogId` proprietà della chiave esterna è non nullable, la relazione tra Blog e post diventa "obbligatoria". In questa situazione, per impostazione predefinita, EF Core eliminerà le entità dipendenti/figlio quando l'entità o il padre viene eliminato. Ad esempio, l'eliminazione di un Blog con i post correlati come nell'esempio precedente:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

Esaminando la [visualizzazione di debug del rilevamento modifiche](xref:core/change-tracking/debug-views) dopo la chiamata a `Remove` viene mostrato che, come previsto, il Blog è di nuovo contrassegnato come `Deleted` :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
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

Più interessante, in questo caso, tutti i post correlati sono stati contrassegnati come `Deleted` . La chiamata a SaveChanges comporta l'eliminazione del Blog e di tutti i post correlati dal database:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

Dopo il completamento di SaveChanges, tutte le entità eliminate vengono scollegate dal DbContext poiché non esistono più nel database. L'output della visualizzazione debug è quindi vuoto.

> [!NOTE]
> Questo documento graffia solo la superficie di utilizzo delle relazioni in EF Core. Per ulteriori informazioni sulla modellazione delle relazioni e la [modifica delle chiavi esterne e](xref:core/change-tracking/relationship-changes) delle esplorazioni, vedere [relazioni](xref:core/modeling/relationships) per ulteriori informazioni sull'aggiornamento o l'eliminazione di entità dipendenti/figlio quando si chiama SaveChanges.

## <a name="custom-tracking-with-trackgraph"></a>Rilevamento personalizzato con TrackGraph

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> funziona come `Add` `Attach` e, `Update` ad eccezione del fatto che genera un callback per ogni istanza di entità prima di tenerne traccia. In questo modo è possibile usare la logica personalizzata per determinare come tenere traccia delle singole entità in un grafico.

Si consideri, ad esempio, la regola EF Core utilizza per tenere traccia delle entità con valori di chiave generati: se il valore Kye è zero, l'entità è nuova e deve essere inserita. Si estenderà questa regola per indicare se il valore della chiave è negativo, quindi l'entità deve essere eliminata. In questo modo è possibile modificare i valori di chiave primaria nelle entità di un grafo disconnesso per contrassegnare le entità eliminate:

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

Il grafo disconnesso può quindi essere rilevato usando TrackGraph:

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

Per ogni entità nel grafico, il codice sopra riportato controlla il valore della chiave primaria _prima di tenere traccia dell'entità_. Per i valori di chiave non impostati (zero), il codice esegue le operazioni EF Core normalmente. Ovvero, se la chiave non è impostata, l'entità è contrassegnata come `Added` . Se la chiave è impostata e il valore è non negativo, l'entità è contrassegnata come `Modified` . Tuttavia, se viene trovato un valore di chiave negativo, il relativo valore reale non negativo viene ripristinato e l'entità viene rilevata come `Deleted` .

L'output dell'esecuzione del codice è:

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> Per semplicità, questo codice presuppone che ogni entità disponga di una proprietà di chiave primaria integer denominata `Id` . Questo può essere codificato in una classe di base astratta o in un'interfaccia. In alternativa, è possibile ottenere la proprietà o le proprietà della chiave primaria dai <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadati in modo che questo codice funzioni con qualsiasi tipo di entità.

TrackGraph ha due overload. Nell'overload semplice utilizzato sopra, EF Core determina quando arrestare l'attraversamento del grafo. In particolare, viene arrestata la visita di nuove entità correlate da un'entità specificata quando tale entità è già rilevata o quando il callback non avvia il rilevamento dell'entità.

L'overload avanzato, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> , dispone di un callback che restituisce un valore bool. Se il callback restituisce false, l'attraversamento del grafo si interrompe, in caso contrario continua. È necessario prestare attenzione per evitare cicli infiniti quando si utilizza questo overload.

L'overload avanzato consente inoltre di fornire lo stato a TrackGraph e questo stato viene quindi passato a ogni callback.
