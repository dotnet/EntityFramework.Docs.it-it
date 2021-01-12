---
title: Funzionalità aggiuntive di Rilevamento modifiche-EF Core
description: Varie funzionalità e scenari che coinvolgono EF Core rilevamento delle modifiche
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: db1e32948b2a60ad1b85e300bbbccd54d49a84e5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129740"
---
# <a name="additional-change-tracking-features"></a>Funzionalità aggiuntive di Rilevamento modifiche

Questo documento illustra le varie funzionalità e gli scenari che coinvolgono il rilevamento delle modifiche.

> [!TIP]
> In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi. Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).

## <a name="add-verses-addasync"></a>Aggiungi verse AddAsync

Entity Framework Core (EF Core) fornisce metodi asincroni ogni volta che l'utilizzo di tale metodo può comportare un'interazione del database. Vengono inoltre forniti metodi sincroni per evitare overhead quando si utilizzano database che non supportano l'accesso asincrono ad alte prestazioni.

<xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> non accedono normalmente al database, poiché questi metodi intrinsecamente iniziano a tenere traccia delle entità. Tuttavia, alcune forme di generazione del valore _possono_ accedere al database per generare un valore di chiave. L'unico generatore di valori che esegue questa operazione e viene fornito con EF Core è <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> . L'uso di questo generatore è insolito; non viene mai configurato per impostazione predefinita. Ciò significa che la maggior parte delle applicazioni deve usare `Add` e non `AddAsync` .

Altri metodi simili, `Update` ad esempio, `Attach` e `Remove` non hanno overload asincroni perché non generano mai nuovi valori di chiave e quindi non devono mai accedere al database.

## <a name="addrange-updaterange-attachrange-and-removerange"></a>AddRange, UpdateRange, AttachRange e RemoveRange

<xref:Microsoft.EntityFrameworkCore.DbSet%601> e <xref:Microsoft.EntityFrameworkCore.DbContext> forniscono versioni alternative di `Add` , `Update` , `Attach` e `Remove` che accettano più istanze in un'unica chiamata. Questi metodi vengono chiamati `AddRange` rispettivamente,, `UpdateRange` `AttachRange` e `RemoveRange` .

Questi metodi vengono forniti come praticità. L'uso di un metodo "range" ha la stessa funzionalità di più chiamate al metodo non di intervallo equivalente. Non esiste alcuna differenza di prestazioni significativa tra i due approcci.

> [!NOTE]
> Si tratta di un metodo diverso da EF6, in cui AddRange e aggiungono entrambi automaticamente DetectChanges, ma la chiamata di Aggiungi più volte ha causato la chiamata di DetectChanges più volte anziché una volta. Questo rendeva il AddRange più efficiente in EF6. In EF Core, nessuno di questi metodi chiama automaticamente DetectChanges.

## <a name="dbcontext-verses-dbset-methods"></a>Metodi DbSet di DbContext in versi

Molti metodi, tra cui `Add` , `Update` , `Attach` e `Remove` , hanno implementazioni sia in <xref:Microsoft.EntityFrameworkCore.DbSet%601> che in <xref:Microsoft.EntityFrameworkCore.DbContext> . Questi metodi hanno _esattamente lo stesso comportamento per i_ normali tipi di entità. Questo è dovuto al fatto che il tipo CLR dell'entità è mappato a un solo tipo di entità nel modello di EF Core. Pertanto, il tipo CLR definisce in modo completo il punto in cui l'entità si integra nel modello e quindi il DbSet da utilizzare può essere determinato in modo implicito.

L'eccezione a questa regola è quando si usano tipi di entità di tipo condiviso, introdotti in EF Core 5,0, principalmente per entità di join molti-a-molti. Quando si usa un tipo di entità di tipo condiviso, è necessario prima creare un DbSet per il tipo di modello EF Core in uso. `Add` `Update` `Attach` È quindi possibile usare metodi come,, e `Remove` in DbSet senza ambiguità in merito a quali EF Core tipo di modello è in uso.

Per impostazione predefinita, i tipi di entità di tipo condiviso vengono utilizzati per le entità di join nelle relazioni molti-a-molti. Un tipo di entità di tipo condiviso può anche essere configurato in modo esplicito per l'utilizzo in una relazione molti-a-molti. Il codice seguente, ad esempio, viene configurato `Dictionary<string, int>` come tipo di entità join:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

[Cambiando le chiavi esterne e le](xref:core/change-tracking/relationship-changes) esplorazioni viene illustrato come associare due entità monitorando una nuova istanza di entità di join. Il codice seguente esegue questa operazione per il `Dictionary<string, int>` tipo di entità di tipo condiviso usato per l'entità di join:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_verses_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_verses_DbSet_methods_1)]

Si noti che <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> viene usato per creare un DbSet per il `PostTag` tipo di entità. Questo DbSet può quindi essere usato per chiamare `Add` con la nuova istanza di entità di join.

> [!IMPORTANT]
> Il tipo CLR utilizzato per i tipi di entità join per convenzione può cambiare nelle versioni future per migliorare le prestazioni. Non dipendere da un tipo di entità join specifico, a meno che non sia stato configurato in modo esplicito come avviene per `Dictionary<string, int>` nel codice precedente.

## <a name="property-verses-field-access"></a>Accesso al campo delle proprietà verse

A partire da EF Core 3,0, l'accesso alle proprietà dell'entità utilizza per impostazione predefinita il campo sottostante della proprietà. Questa operazione è efficace ed evita l'attivazione di effetti collaterali dalla chiamata di getter e setter di proprietà. Ad esempio, questo è il modo in cui il caricamento lazy è in grado di evitare l'attivazione di cicli infiniti. Per ulteriori informazioni sulla configurazione dei campi di supporto nel modello, vedere la pagina relativa ai campi sottoposti a [backup](xref:core/modeling/backing-field) .

A volte può essere utile per EF Core generare effetti collaterali quando modifica i valori delle proprietà. Ad esempio, quando si data binding alle entità, l'impostazione di una proprietà può generare notifiche per U.I. che non si verificano quando si imposta direttamente il campo. Questa operazione può essere eseguita cambiando <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> per:

- Tutti i tipi di entità nel modello utilizzando <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Tutte le proprietà e le navigazioni di un tipo di entità specifico usando <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Una proprietà specifica con <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Una navigazione specifica con <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>

Le modalità di accesso alle proprietà `Field` e `PreferField` determineranno EF Core accedere al valore della proprietà tramite il campo sottostante. Analogamente, `Property` e `PreferProperty` comporterà EF Core accedere al valore della proprietà tramite il metodo di richiamo e impostazione.

Se `Field` `Property` si utilizzano o e EF Core non è in grado di accedere al valore rispettivamente tramite il metodo Get o Setter del campo o della proprietà, EF Core genererà un'eccezione. Ciò garantisce che EF Core usi sempre l'accesso al campo o alla proprietà quando si ritiene che sia.

D'altra parte, `PreferField` `PreferProperty` se non è possibile usare l'accesso preferito, le modalità e eseguiranno il fallback rispettivamente all'uso della proprietà o del campo sottostante. `PreferField` è il valore predefinito di EF Core 3,0 e versioni successive. Ciò significa che EF Core utilizzerà i campi ogni volta che è possibile, ma non avrà esito negativo se è necessario accedere a una proprietà tramite il getter o il setter.

`FieldDuringConstruction` e `PreferFieldDuringConstruction` configurano EF core per l'uso dei campi di backup _solo quando si creano istanze di entità_. In questo modo è possibile eseguire le query senza effetti collaterali getter e setter, mentre le modifiche successive alle proprietà di EF Core provocheranno questi effetti collaterali. `PreferFieldDuringConstruction` è il valore predefinito precedente alla EF Core 3,0.

Le diverse modalità di accesso alle proprietà sono riepilogate nella tabella seguente:

| PropertyAccessMode              | Preferenza | Preferenza per la creazione di entità | Fallback | Fallback creazione di entità
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | Campo      | Campo                        | Genera un'eccezione   | Genera un'eccezione
| `Property`                      | Proprietà   | Proprietà                     | Genera un'eccezione   | Genera un'eccezione
| `PreferField`                   | Campo      | Campo                        | Proprietà | Proprietà
| `PreferProperty`                | Proprietà   | Proprietà                     | Campo    | Campo
| `FieldDuringConstruction`       | Proprietà   | Campo                        | Campo    | Genera un'eccezione
| `PreferFieldDuringConstruction` | Proprietà   | Campo                        | Campo    | Proprietà

## <a name="temporary-values"></a>Valori temporanei

EF Core crea valori di chiave temporanea durante il rilevamento di nuove entità che avranno valori di chiave reali generati dal database quando viene chiamato SaveChanges. Per una panoramica del modo in cui vengono usati questi valori temporanei, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .

### <a name="accessing-temporary-values"></a>Accesso ai valori temporanei

A partire da EF Core 3,0, i valori temporanei vengono archiviati in rilevamento modifiche e non vengono impostati direttamente sulle istanze di entità. Tuttavia, questi valori temporanei vengono esposti quando _si_ usano i vari meccanismi per [accedere alle entità registrate](xref:core/change-tracking/entity-entries). Il codice seguente, ad esempio, accede a un valore temporaneo usando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

L'output di questo codice è:

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> può essere usato per verificare la presenza di valori temporanei.

### <a name="manipulating-temporary-values"></a>Modifica di valori temporanei

A volte è utile usare in modo esplicito i valori temporanei. Ad esempio, è possibile creare una raccolta di nuove entità in un client Web e quindi serializzarle nuovamente nel server. I valori di chiave esterna rappresentano un modo per configurare le relazioni tra queste entità. Il codice seguente usa questo approccio per associare un grafico di nuove entità in base alla chiave esterna, consentendo allo stesso tempo la generazione di valori di chiave reali quando viene chiamato SaveChanges.

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

Si noti che:

- I numeri negativi vengono utilizzati come valori di chiave temporanei. Questa operazione non è obbligatoria, ma è una convenzione comune per evitare conflitti di chiave.
- Alla `Post.BlogId` Proprietà FK viene assegnato lo stesso valore negativo del PK del Blog associato.
- I valori di PK sono contrassegnati come temporanei impostando il <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> rilevamento di ogni entità. Questa operazione è necessaria perché viene considerato un valore di chiave reale per qualsiasi valore di chiave fornito dall'applicazione.

Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) prima di chiamare SaveChanges si indica che i valori PK sono contrassegnati come temporanei e che i post sono associati ai Blog corretti, inclusa la correzione delle spostamenti:

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

Dopo la chiamata a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , questi valori temporanei sono stati sostituiti dai valori reali generati dal database:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a>Uso dei valori predefiniti

EF Core consente a una proprietà di ottenere il relativo valore predefinito dal database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo. Analogamente a quanto avviene con i valori di chiave generati, EF Core utilizzerà solo un valore predefinito del database se non è stato impostato in modo esplicito alcun valore. Si consideri, ad esempio, il tipo di entità seguente:

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

La `ValidFrom` proprietà è configurata in modo da ottenere un valore predefinito dal database:

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

Quando si inserisce un'entità di questo tipo, EF Core consente al database di generare il valore a meno che non sia stato impostato un valore esplicito. Ad esempio:

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

Esaminando la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) viene mostrato che il primo token è stato `ValidFrom` generato dal database, mentre il secondo token usava il valore impostato in modo esplicito:

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> Per utilizzare i valori predefiniti del database, è necessario configurare il vincolo di valore predefinito per la colonna di database. Questa operazione viene eseguita automaticamente da EF Core migrazioni quando si usa <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> o <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> . Assicurarsi di creare il vincolo predefinito sulla colonna in un altro modo quando non si usano migrazioni di EF Core.

### <a name="using-nullable-properties"></a>Uso delle proprietà Nullable

EF Core è in grado di determinare se è stata impostata una proprietà confrontando il valore della proprietà con il valore predefinito CLR per quel tipo. Questa operazione funziona correttamente nella maggior parte dei casi, ma significa che non è possibile inserire in modo esplicito il valore predefinito CLR nel database. Si consideri ad esempio un'entità con una proprietà Integer:

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

Se la proprietà è configurata con il valore predefinito del database-1:

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

L'intenzione è che verrà usato il valore predefinito-1 ogni volta che un valore esplicito non è impostato. Tuttavia, l'impostazione del valore su 0 (valore predefinito CLR per numeri interi) non è distinguibile per EF Core di non impostare alcun valore. Ciò significa che non è possibile inserire 0 per questa proprietà. Ad esempio:

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

Si noti che l'istanza `Count` di in cui è stato impostato in modo esplicito su 0 ottiene comunque il valore predefinito dal database, che non è quello previsto. Un modo semplice per gestire questo problema consiste nel rendere `Count` Nullable la proprietà:

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

Il valore predefinito di CLR è null, anziché 0, che indica che il valore 0 verrà inserito quando viene impostato in modo esplicito:

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a>Uso dei campi di supporto Nullable

> [!NOTE]
> Questo modello di campo di supporto Nullable è supportato da EF Core 5,0 e versioni successive.

Il problema di rendere la proprietà Nullable che può non essere concettualmente nullable nel modello di dominio. La forzatura della proprietà come Nullable compromette pertanto il modello.

A partire da EF Core 5,0, la proprietà può essere lasciata non nullable e solo il campo sottostante ammette i valori null. Ad esempio:

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

Questo consente l'inserimento di CLR default (0) se la proprietà è impostata in modo esplicito su 0, senza dover esporre la proprietà come nullable nel modello di dominio. Ad esempio:

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a>Campi di supporto Nullable per le proprietà bool

Questo modello è particolarmente utile quando si usano le proprietà bool con le impostazioni predefinite generate dall'archivio. Poiché il valore predefinito di CLR per `bool` è "false", significa che "false" non può essere inserito in modo esplicito con il modello normale. Si consideri, ad esempio, un `User` tipo di entità:

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

La `IsAuthorized` proprietà è configurata con il valore predefinito del database "true":

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

La `IsAuthorized` proprietà può essere impostata su "true" o "false" in modo esplicito prima dell'inserimento. in caso contrario, il valore predefinito del database verrà utilizzato:

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

L'output di SaveChanges quando si usa SQLite indica che per Mac viene usato il valore predefinito del database, mentre i valori espliciti sono impostati per Alice e Baxter:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a>Solo impostazioni predefinite schema

A volte è utile avere impostazioni predefinite nello schema del database creato da EF Core migrazioni senza EF Core mai usare questi valori per gli inserimenti. Questa operazione può essere eseguita configurando la proprietà come <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> ad esempio:

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
