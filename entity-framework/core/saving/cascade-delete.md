---
title: Eliminazione a catena - EF Core
description: Configurazione dei comportamenti di propagazione attivati quando un'entità viene eliminata o interrotta dal rispettivo/principale
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 7c35de900930cf42da0e534df76124b5fb19ca52
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128862"
---
# <a name="cascade-delete"></a>Eliminazione a catena

Entity Framework Core (EF Core) rappresenta le relazioni utilizzando chiavi esterne. Un'entità con una chiave esterna è l'entità figlio o dipendente nella relazione. Il valore di chiave esterna di questa entità deve corrispondere al valore di chiave primaria o a un valore di chiave alternativo dell'entità correlata/padre.

Se l'entità principale/padre viene eliminata, i valori di chiave esterna dei dipendenti/figli non corrisponderanno più alla chiave primaria o alternativa di _qualsiasi_ entità/elemento padre. Si tratta di uno stato non valido e causerà una violazione del vincolo referenziale nella maggior parte dei database.

Sono disponibili due opzioni per evitare la violazione del vincolo referenziale:

1. Impostare i valori FK su null
2. Elimina anche le entità dipendenti/figlio

La prima opzione è valida solo per le relazioni facoltative in cui la proprietà di chiave esterna (e la colonna del database a cui è mappata) deve ammettere i valori null.

La seconda opzione è valida per qualsiasi tipo di relazione ed è nota come "Cascade Delete".

> [!TIP]
> Questo documento descrive le eliminazioni a catena (ed Elimina gli orfani) dal punto di vista dell'aggiornamento del database. Viene utilizzato in modo intensivo i concetti introdotti in [rilevamento modifiche in EF Core](xref:core/change-tracking/index) e la [modifica di chiavi esterne e spostamenti](xref:core/change-tracking/relationship-changes). Assicurarsi di comprendere completamente questi concetti prima di affrontare il materiale qui.

> [!TIP]  
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).

## <a name="when-cascading-behaviors-happen"></a>Quando si verificano i comportamenti a catena

Le eliminazioni a catena sono necessarie quando un'entità dipendente/figlio non può più essere associata al relativo elemento principale/padre corrente. Questo problema può verificarsi perché l'entità o il padre viene eliminato oppure può verificarsi quando l'entità o il padre esiste ancora, ma il dipendente/figlio non è più associato ad esso.

### <a name="deleting-a-principalparent"></a>Eliminazione di un'entità/elemento padre

Si consideri questo modello semplice `Blog` in cui è il principale/padre in una relazione con `Post` , che è l'oggetto dipendente/figlio. `Post.BlogId` è una proprietà di chiave esterna, il cui valore deve corrispondere alla `Post.Id` chiave primaria del post a cui appartiene il Blog.

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

Per convenzione, questa relazione è configurata come obbligatoria, perché la `Post.BlogId` proprietà della chiave esterna è non nullable. Per impostazione predefinita, le relazioni obbligatorie sono configurate per l'utilizzo delle eliminazioni a Per ulteriori informazioni sulle relazioni di modellazione, vedere [relazioni](xref:core/modeling/relationships) .

Quando si elimina un Blog, tutti i post vengono eliminati a cascata. Ad esempio:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

SaveChanges genera la seguente SQL, usando SQL Server come esempio:

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a>Separazione di una relazione

Anziché eliminare il Blog, è possibile, invece, interrompere la relazione tra ogni post e il relativo Blog. Questa operazione può essere eseguita impostando la navigazione `Post.Blog` di riferimento su null per ogni post:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

La relazione può essere interrotta anche rimuovendo ogni post dalla `Blog.Posts` navigazione della raccolta:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

In entrambi i casi il risultato è lo stesso: il Blog non viene eliminato, ma i post che non sono più associati ad alcun Blog vengono eliminati:

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

L'eliminazione di entità che non sono più associate a un'entità/dipendente è nota come "eliminazione di orfani".

> [!TIP]
> Gli orfani a catena e eliminazione sono strettamente correlati. Entrambe comportano l'eliminazione di entità dipendenti/figlio quando la relazione con l'entità/padre richiesta è grave. Per l'eliminazione a catena, questo errore si verifica perché il server principale/padre viene eliminato. Per gli orfani, l'entità principale/padre continua a esistere, ma non è più correlata alle entità dipendenti/figlio.  

## <a name="where-cascading-behaviors-happen"></a>Dove si verificano i comportamenti a catena

I comportamenti a cascata possono essere applicati a:

- Entità rilevate dall'oggetto corrente <xref:Microsoft.EntityFrameworkCore.DbContext>
- Entità nel database che non sono state caricate nel contesto

### <a name="cascade-delete-of-tracked-entities"></a>Eliminazione a catena di entità rilevate

EF Core applica sempre comportamenti di propagazione configurati alle entità registrate. Ciò significa che se l'applicazione carica tutte le entità dipendenti/figlio rilevanti in DbContext, come illustrato negli esempi precedenti, i comportamenti a cascata verranno applicati correttamente indipendentemente dalla configurazione del database.

> [!TIP]
> La tempistica esatta di quando si verificano comportamenti di propagazione delle entità rilevate può essere controllata mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> . Per ulteriori informazioni, vedere [modifica delle chiavi esterne e degli spostamenti](xref:core/change-tracking/relationship-changes) .

### <a name="cascade-delete-in-the-database"></a>Eliminazione a catena nel database

Molti sistemi di database offrono inoltre comportamenti a cascata che vengono attivati quando un'entità viene eliminata nel database. EF Core configura questi comportamenti in base al comportamento di eliminazione a catena nel modello di EF Core quando viene creato un database tramite <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> o EF Core le migrazioni. Se ad esempio si usa il modello precedente, viene creata la tabella seguente per i post quando si usa SQL Server:

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

Si noti che il vincolo FOREIGN KEY che definisce la relazione tra Blog e post è configurato con `ON DELETE CASCADE` .

Se si è certi che il database è configurato in questo modo, è possibile eliminare un Blog _senza prima caricare i post_ e il database si occuperà di eliminare tutti i post correlati a tale Blog. Ad esempio:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

Si noti che non esiste alcuna `Include` per i post, quindi non vengono caricati. SaveChanges in questo caso eliminerà solo il Blog, poiché si tratta dell'unica entità rilevata:

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

Ciò genera un'eccezione se il vincolo FOREIGN KEY nel database non è configurato per le eliminazioni a catena. Tuttavia, in questo caso i post vengono eliminati dal database perché sono stati configurati con al `ON DELETE CASCADE` momento della creazione.

> [!NOTE]
> I database non hanno in genere alcun modo per eliminare automaticamente gli orfani. Questo è dovuto al fatto che mentre EF Core rappresenta le relazioni utilizzando le chiavi esterne e le chiavi esterne, i database dispongono solo di chiavi esterne e non di navigazione. Ciò significa che in genere non è possibile eseguire il troncamento di una relazione senza caricare entrambi i lati in DbContext.

> [!NOTE]
> Il database in memoria EF Core attualmente non supporta le eliminazioni a catena nel database.

> [!WARNING]
> Non configurare l'eliminazione a catena nel database durante l'eliminazione temporanea delle entità. Questo può causare l'eliminazione accidentale di entità anziché l'eliminazione temporanea.

### <a name="database-cascade-limitations"></a>Limitazioni del database a catena

Alcuni database, in particolare SQL Server, presentano limitazioni sui comportamenti Cascade che formano i cicli. Si consideri, ad esempio, il modello seguente:

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

Questo modello ha tre relazioni, tutte obbligatorie e pertanto configurate per l'eliminazione a catena per convenzione:

- L'eliminazione di un Blog provocherà l'eliminazione a catena di tutti i post correlati
- Se si elimina l'autore dei post, i post creati verranno eliminati a cascata
- Eliminando il proprietario di un Blog, il blog verrà eliminato a cascata

Si tratta di un'operazione ragionevole (se un po' draconiane nei criteri di gestione del Blog), ma il tentativo di creare un database di SQL Server con questi Cascade configurati genera l'eccezione seguente:

> Microsoft. Data. SqlClient. SqlException (0x80131904): l'introduzione del vincolo FOREIGN KEY ' FK_Posts_Person_AuthorId ' nella tabella ' post ' può causare cicli o più percorsi Cascade. Specificare ON DELETE NO ACTION o ON UPDATE NO ACTION oppure modificare gli altri vincoli FOREIGN KEY.

Esistono due modi per gestire questa situazione:

1. Modificare una o più relazioni per non eliminare Cascade.
2. Configurare il database senza una o più delle eliminazioni a cascata, quindi assicurarsi che tutte le entità dipendenti siano caricate in modo che EF Core possibile eseguire il comportamento di propagazione.

Prendendo il primo approccio dell'esempio, è possibile rendere facoltativa la relazione di Blog-proprietario assegnando una proprietà di chiave esterna Nullable:

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Una relazione facoltativa consente al Blog di esistere senza proprietario, il che significa che l'eliminazione a catena non verrà più configurata per impostazione predefinita. Ciò significa che non è più presente un ciclo nelle azioni di propagazione e il database può essere creato senza errori in SQL Server.

Invece di adottare il secondo approccio, è possibile lasciare che la relazione di Blog-proprietario sia obbligatoria e configurata per l'eliminazione a catena, ma applicare questa configurazione solo alle entità rilevate e non al database:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

Che cosa accade se si caricano sia una persona che il Blog di cui si è proprietari, quindi si elimina la persona?

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

EF Core eliminerà l'eliminazione del proprietario, in modo che venga eliminato anche il Blog:

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Tuttavia, se il Blog non viene caricato quando il proprietario viene eliminato:

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

Quindi verrà generata un'eccezione a causa della violazione del vincolo di chiave esterna nel database:

> Microsoft. Data. SqlClient. SqlException: istruzione DELETE in conflitto con il vincolo di riferimento "FK_Blogs_People_OwnerId". Il conflitto si è verificato nel database "Scratch", tabella "dbo". Blog ", colonna ' OwnerId '.
L'istruzione è stata interrotta.

## <a name="cascading-nulls"></a>Valori null a catena

Le relazioni facoltative dispongono di proprietà di chiave esterna Nullable mappate a colonne di database Nullable. Questo significa che il valore della chiave esterna può essere impostato su null quando l'entità o il padre corrente viene eliminato o è grave dal dipendente/figlio.

Di seguito vengono esaminati gli esempi relativi al [momento in cui si verificano i comportamenti a cascata](#when-cascading-behaviors-happen), ma questa volta con una relazione facoltativa rappresentata da una proprietà di `Post.BlogId` chiave esterna Nullable:

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Questa proprietà di chiave esterna verrà impostata su null per ogni post quando il relativo Blog viene eliminato. Ad esempio, questo codice, che è lo stesso di prima:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

Determinerà gli aggiornamenti del database seguenti quando viene chiamato SaveChanges:

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

Analogamente, se la relazione è stata interrotta utilizzando uno degli esempi precedenti:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

Oppure:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

I post vengono quindi aggiornati con valori di chiave esterna null quando viene chiamato SaveChanges:

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Per ulteriori informazioni sul modo in cui EF Core gestisce le chiavi esterne e le navigazioni, vedere [modifica](xref:core/change-tracking/relationship-changes) delle chiavi esterne e delle esplorazioni.

> [!NOTE]
> La correzione di relazioni come questa è stata il comportamento predefinito di Entity Framework dalla prima versione in 2008. Prima di EF Core non aveva un nome e non era possibile modificarlo. È ora noto come `ClientSetNull` descritto nella sezione successiva.

I database possono anche essere configurati per la propagazione di valori null come questo quando viene eliminata un'entità o un padre in una relazione facoltativa. Questa operazione è tuttavia molto meno comune rispetto all'uso di eliminazioni a catena nel database. L'utilizzo delle eliminazioni a catena e dei valori null a catena nel database genera quasi sempre i cicli di relazione quando si utilizza SQL Server. Per ulteriori informazioni sulla configurazione dei valori null a catena, vedere la sezione successiva.

## <a name="configuring-cascading-behaviors"></a>Configurazione dei comportamenti a catena

> [!TIP]
> Assicurarsi di leggere le sezioni precedenti prima di venire qui. Le opzioni di configurazione probabilmente non hanno senso se il materiale precedente non è compreso.

I comportamenti a cascata sono configurati per relazione usando il <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> metodo in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> . Ad esempio:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

Per ulteriori informazioni sulla configurazione delle relazioni tra tipi di entità, vedere [relazioni](xref:core/modeling/relationships) .

`OnDelete` accetta un valore dell'enumerazione, che si confonde <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> . Questa enumerazione definisce sia il comportamento di EF Core sulle entità rilevate che la configurazione dell'eliminazione a catena nel database quando EF viene utilizzato per creare lo schema.

### <a name="impact-on-database-schema"></a>Effetto sullo schema del database

La tabella seguente mostra il risultato di ogni `OnDelete` valore nel vincolo FOREIGN KEY creato da EF Core Migrations o <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> .

| DeleteBehavior        | Effetto sullo schema del database
|:----------------------|--------------------------
| Cascade               | ON DELETE CASCADE
| Limitazione              | ON DELETE NO ACTION
| NoAction              | <database default>
| SetNull               | ON DELETE SET NULL
| ClientSetNull         | ON DELETE NO ACTION
| ClientCascade         | ON DELETE NO ACTION
| ClientNoAction        | <database default>

> [!NOTE]
> Questa tabella è confusa e si prevede di rivisitarla in una versione futura. Vedere [#21252 problema di GitHub](https://github.com/dotnet/efcore/issues/21252).

I comportamenti di `ON DELETE NO ACTION` e `ON DELETE RESTRICT` nei database relazionali sono in genere identici o molto simili. Nonostante ciò che `NO ACTION` può implicare, entrambe le opzioni provocano l'applicazione di vincoli referenziali. La differenza, quando ne esiste una, è _quando_ il database controlla i vincoli.  Controllare la documentazione del database per le differenze specifiche tra `ON DELETE NO ACTION` e `ON DELETE RESTRICT` nel sistema di database.

Gli unici valori che determineranno comportamenti a catena nel database sono `Cascade` e `SetNull` . Tutti gli altri valori configureranno il database in modo che non vengano propagate le modifiche.

### <a name="impact-on-savechanges-behavior"></a>Effetti sul comportamento di SaveChanges

Le tabelle nelle sezioni seguenti illustrano cosa accade alle entità dipendenti/figlio quando l'entità o il padre viene eliminato oppure la relazione con le entità dipendenti/figlio è grave. Ogni tabella copre uno dei seguenti:

- Relazioni facoltative (Nullable FK) e richieste (FK non nullable)
- Quando dipendenti/figli vengono caricati e monitorati da DbContext e quando esistono solo nel database

#### <a name="required-relationship-with-dependentschildren-loaded"></a>Relazione obbligatoria con dipendenti/figli caricati

| DeleteBehavior    | All'eliminazione dell'entità/elemento padre             | In base al server principale/padre
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dipendenti eliminati da EF Core            | Dipendenti eliminati da EF Core
| Limitazione          | `InvalidOperationException`              | `InvalidOperationException`
| NoAction          | `InvalidOperationException`              | `InvalidOperationException`
| SetNull           | `SqlException` durante la creazione del database      | `SqlException` durante la creazione del database
| ClientSetNull     | `InvalidOperationException`              | `InvalidOperationException`
| ClientCascade     | Dipendenti eliminati da EF Core            | Dipendenti eliminati da EF Core
| ClientNoAction    | `DbUpdateException`                      | `InvalidOperationException`

Note:

- Il valore predefinito per le relazioni obbligatorie come questa è `Cascade` .
- Se si utilizza un valore diverso da Cascade Delete per le relazioni obbligatorie, verrà generata un'eccezione quando viene chiamato SaveChanges.
  - In genere, si tratta di un `InvalidOperationException` EF core dal momento che lo stato non valido viene rilevato negli elementi figlio/dipendenti caricati.
  - `ClientNoAction` impone EF Core di non controllare i dipendenti della correzione prima di inviarli al database, in questo caso il database genera un'eccezione, che viene quindi sottoposto a wrapper in `DbUpdateException` da SaveChanges.
  - `SetNull` viene rifiutato quando si crea il database perché la colonna chiave esterna non ammette i valori null.
- Poiché i dipendenti/figli vengono caricati, vengono sempre eliminati da EF Core e non sono mai rimasti per eliminare il database.

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a>Relazione obbligatoria con dipendenti/figli non caricati

| DeleteBehavior    | All'eliminazione dell'entità/elemento padre             | In base al server principale/padre
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dipendenti eliminati dal database           | N/D
| Limitazione          | `DbUpdateException`                      | N/D
| NoAction          | `DbUpdateException`                      | N/D
| SetNull           | `SqlException` durante la creazione del database      | N/D
| ClientSetNull     | `DbUpdateException`                      | N/D
| ClientCascade     | `DbUpdateException`                      | N/D
| ClientNoAction    | `DbUpdateException`                      | N/D

Note:

- Il troncamento di una relazione non è valido perché i dipendenti/figli non vengono caricati.
- Il valore predefinito per le relazioni obbligatorie come questa è `Cascade` .
- Se si utilizza un valore diverso da Cascade Delete per le relazioni obbligatorie, verrà generata un'eccezione quando viene chiamato SaveChanges.
  - In genere, si tratta di un oggetto `DbUpdateException` perché i dipendenti/figli non vengono caricati e pertanto lo stato non valido può essere rilevato solo dal database. SaveChanges esegue quindi il wrapping dell'eccezione del database in un oggetto `DbUpdateException` .
  - `SetNull` viene rifiutato quando si crea il database perché la colonna chiave esterna non ammette i valori null.

#### <a name="optional-relationship-with-dependentschildren-loaded"></a>Relazione facoltativa con dipendenti/figli caricati

| DeleteBehavior    | All'eliminazione dell'entità/elemento padre             | In base al server principale/padre
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dipendenti eliminati da EF Core            | Dipendenti eliminati da EF Core
| Limitazione          | FKs dipendente impostato su null da EF Core     | FKs dipendente impostato su null da EF Core
| NoAction          | FKs dipendente impostato su null da EF Core     | FKs dipendente impostato su null da EF Core
| SetNull           | FKs dipendente impostato su null da EF Core     | FKs dipendente impostato su null da EF Core
| ClientSetNull     | FKs dipendente impostato su null da EF Core     | FKs dipendente impostato su null da EF Core
| ClientCascade     | Dipendenti eliminati da EF Core            | Dipendenti eliminati da EF Core
| ClientNoAction    | `DbUpdateException`                      | FKs dipendente impostato su null da EF Core

Note:

- Il valore predefinito per le relazioni facoltative come questa è `ClientSetNull` .
- I dipendenti/figli non vengono mai eliminati, a meno che non `Cascade` `ClientCascade` siano configurati o.
- Tutti gli altri valori determinano l'impostazione del FKs dipendente su null da EF Core...
  - ... `ClientNoAction` con la differenza che indica EF Core di non toccare le chiavi esterne di dipendenti/figli quando l'entità o il padre viene eliminato. Il database genera quindi un'eccezione, che viene incapsulata come `DbUpdateException` da SaveChanges.

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a>Relazione facoltativa con dipendenti/figli non caricati

| DeleteBehavior    | All'eliminazione dell'entità/elemento padre             | In base al server principale/padre
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Dipendenti eliminati dal database           | N/D
| Limitazione          | `DbUpdateException`                      | N/D
| NoAction          | `DbUpdateException`                      | N/D
| SetNull           | FKs dipendente impostato su null per database    | N/D
| ClientSetNull     | `DbUpdateException`                      | N/D
| ClientCascade     | `DbUpdateException`                      | N/D
| ClientNoAction    | `DbUpdateException`                      | N/D

Note:

- Il troncamento di una relazione non è valido perché i dipendenti/figli non vengono caricati.
- Il valore predefinito per le relazioni facoltative come questa è `ClientSetNull` .
- È necessario caricare dipendenti/elementi figlio per evitare un'eccezione di database, a meno che il database non sia stato configurato per le eliminazioni o i valori null.
