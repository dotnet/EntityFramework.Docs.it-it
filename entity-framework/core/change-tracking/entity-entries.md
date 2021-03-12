---
title: Accesso alle entità rilevate-EF Core
description: Uso di EntityEntry, DbContext. entrys e DbSet. local per accedere alle entità registrate
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: 758d21f44dfeb8b1de2702165df0d705edfb91b6
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024511"
---
# <a name="accessing-tracked-entities"></a>Accesso alle entità rilevate

Sono disponibili quattro API principali per l'accesso alle entità registrate da un <xref:Microsoft.EntityFrameworkCore.DbContext> :

- <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> Restituisce un' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> istanza per un'istanza di entità specificata.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> restituisce <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> le istanze per tutte le entità rilevate o per tutte le entità rilevate di un determinato tipo.
- <xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> trovano una singola entità in base alla chiave primaria, esaminando prima di tutto le entità rilevate, quindi eseguendo una query sul database, se necessario.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Restituisce le entità effettive (non le istanze EntityEntry) per le entità del tipo di entità rappresentato da DbSet.

Ognuno di questi elementi viene descritto più dettagliatamente nelle sezioni seguenti.

> [!TIP]
> In questo documento si presuppone che gli Stati dell'entità e le nozioni di base del EF Core rilevamento delle modifiche siano compresi. Per ulteriori informazioni su questi argomenti, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AccessingTrackedEntities).

## <a name="using-dbcontextentry-and-entityentry-instances"></a>Uso di istanze di DbContext. entry e EntityEntry

Per ogni entità rilevata, Entity Framework Core (EF Core) tiene traccia di:

- Stato generale dell'entità. Questo è uno tra `Unchanged` , `Modified` , `Added` o. `Deleted` per ulteriori informazioni, vedere [rilevamento modifiche in EF Core](xref:core/change-tracking/index) .
- Relazioni tra le entità rilevate. Ad esempio, il Blog a cui appartiene un post.
- Valori correnti delle proprietà.
- I "valori originali" delle proprietà, quando queste informazioni sono disponibili. I valori originali sono i valori delle proprietà esistenti quando è stata eseguita una query sull'entità dal database.
- I valori delle proprietà sono stati modificati da quando sono stati sottoposti a query.
- Altre informazioni sui valori delle proprietà, ad esempio se il valore è [temporaneo](xref:core/change-tracking/miscellaneous#temporary-values)o meno.

Se si passa un'istanza di entità a <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> , si ottiene un oggetto che <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> fornisce accesso a queste informazioni per l'entità specificata. Ad esempio:

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

Le sezioni seguenti illustrano come usare un EntityEntry per accedere e modificare lo stato dell'entità, nonché lo stato delle proprietà e degli spostamenti dell'entità.

### <a name="working-with-the-entity"></a>Utilizzo dell'entità

L'utilizzo più comune di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> è l'accesso alla corrente <xref:Microsoft.EntityFrameworkCore.EntityState> di un'entità. Ad esempio:

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

Il metodo Entry può essere usato anche su entità non ancora rilevate. Questa operazione _non avvia il rilevamento dell'entità_; lo stato dell'entità è ancora `Detatched` . Tuttavia, il EntityEntry restituito può essere usato per modificare lo stato dell'entità, a quel punto l'entità verrà rilevata nello stato specificato. Il codice seguente, ad esempio, avvierà il rilevamento di un'istanza di Blog come `Added` :

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> Diversamente da quanto avviene in EF6, l'impostazione dello stato di una singola entità non comporterà il rilevamento di tutte le entità connesse. In questo modo si imposta lo stato come operazione di livello inferiore rispetto alla chiamata a `Add` , `Attach` o `Update` , che opera su un intero grafico di entità.

La tabella seguente riepiloga i modi in cui usare un EntityEntry per lavorare con un'intera entità:

| Membro EntityEntry                                                                                         | Descrizione
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | Ottiene e imposta l'oggetto <xref:Microsoft.EntityFrameworkCore.EntityState> dell'entità.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | Ottiene l'istanza dell'entità.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | Oggetto <xref:Microsoft.EntityFrameworkCore.DbContext> che tiene traccia dell'entità.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadati per il tipo di entità.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | Indica se per l'entità è stato impostato il valore di chiave.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | Sovrascrive i valori di proprietà con i valori letti dal database.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | Forza il rilevamento delle modifiche solo per questa entità. vedere [rilevamento delle modifiche e notifiche](xref:core/change-tracking/change-detection).

### <a name="working-with-a-single-property"></a>Utilizzo di una singola proprietà

Diversi overload di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> consentono l'accesso alle informazioni relative a una singola proprietà di un'entità. Ad esempio, usando un'API di tipo Fluent fortemente tipizzata:

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

Il nome della proprietà può invece essere passato come stringa. Ad esempio:

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

Il restituito <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> può quindi essere utilizzato per accedere alle informazioni sulla proprietà. Ad esempio, può essere usato per ottenere e impostare il valore corrente della proprietà in questa entità:

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

Entrambi i metodi di proprietà usati sopra restituiscono un'istanza generica fortemente tipizzata <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> . L'uso di questo tipo generico è preferibile perché consente l'accesso ai valori delle proprietà senza i [tipi di valore Boxing](/dotnet/csharp/programming-guide/types/boxing-and-unboxing). Tuttavia, se il tipo di entità o proprietà non è noto in fase di compilazione, è possibile ottenere invece un oggetto non generico <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> :

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

Questo consente l'accesso alle informazioni sulle proprietà per qualsiasi proprietà indipendentemente dal tipo, a scapito dei tipi di valore Boxing. Ad esempio:

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

Nella tabella seguente sono riepilogate le informazioni sulle proprietà esposte da PropertyEntry:

| Membro PropertyEntry                               | Descrizione
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | Ottiene e imposta il valore corrente della proprietà.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | Ottiene e imposta il valore originale della proprietà, se disponibile.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | Un riferimento all'oggetto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> per l'entità.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadati per la proprietà.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | Indica se questa proprietà è contrassegnata come modificata e consente la modifica di questo stato.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | Indica se questa proprietà è contrassegnata come [temporanea](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)e consente la modifica di questo stato.

Note:

- Il valore originale di una proprietà è il valore che la proprietà aveva quando l'entità veniva sottoposta a query dal database. Tuttavia, i valori originali non sono disponibili se l'entità è stata disconnessa e quindi collegata in modo esplicito a un altro DbContext, ad esempio con `Attach` o `Update` . In questo caso, il valore originale restituito sarà uguale al valore corrente.
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aggiornerà solo le proprietà contrassegnate come modificate. Impostare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> su true per forzare EF core ad aggiornare un valore di proprietà specificato o impostarlo su false per evitare che EF Core aggiorni il valore della proprietà.
- [I valori temporanei](xref:core/change-tracking/miscellaneous) vengono in genere generati da EF Core [generatori di valori](xref:core/modeling/generated-properties). Impostando il valore corrente di una proprietà, il valore temporaneo viene sostituito con il valore specificato e la proprietà viene contrassegnata come non temporanea. Impostare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> su true per forzare un valore come temporaneo anche dopo che è stato impostato in modo esplicito.

### <a name="working-with-a-single-navigation"></a>Utilizzo di una singola navigazione

Diversi overload di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> consentono l'accesso alle informazioni su una singola esplorazione.

Le esplorazioni dei riferimenti a una singola entità correlata sono accessibili tramite i <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> metodi. Le esplorazioni di riferimento puntano ai lati "uno" delle relazioni uno-a-molti ed entrambi i lati di una relazione uno-a-uno. Ad esempio:

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

Le esplorazioni possono essere anche raccolte di entità correlate quando vengono usate per i lati "molti" delle relazioni uno-a-molti e molti-a-molti. I <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> metodi vengono usati per accedere alle spostamenti della raccolta. Ad esempio:

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

Alcune operazioni sono comuni per tutte le navigazioni. È possibile accedervi sia per le esplorazioni di riferimento che per quelle della raccolta usando il <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> metodo. Si noti che per accedere a tutte le navigazioni è disponibile solo l'accesso non generico. Ad esempio:

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

Nella tabella seguente sono riepilogate le modalità di utilizzo di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :

| Membro NavigationEntry                                                                                    | Descrizione
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | Ottiene e imposta il valore corrente della navigazione. Si tratta dell'intera raccolta per le esplorazioni della raccolta.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadati per la navigazione.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | Ottiene o imposta un valore che indica se l'entità o la raccolta correlata è stata caricata completamente dal database.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | Carica l'entità o la raccolta correlata dal database. vedere [caricamento esplicito di dati correlati](xref:core/querying/related-data/explicit).
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | Il EF Core di query utilizzerebbe per caricare questa navigazione come un oggetto `IQueryable` che può essere ulteriormente composto. vedere [caricamento esplicito di dati correlati](xref:core/querying/related-data/explicit).

### <a name="working-with-all-properties-of-an-entity"></a>Uso di tutte le proprietà di un'entità

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> Restituisce un oggetto <xref:System.Collections.Generic.IEnumerable%601> di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> per ogni proprietà dell'entità. Questa operazione può essere utilizzata per eseguire un'azione per ogni proprietà dell'entità. Ad esempio, per impostare qualsiasi proprietà DateTime su `DateTime.Now` :

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

Inoltre, EntityEntry contiene diversi metodi per ottenere e impostare contemporaneamente tutti i valori delle proprietà. Questi metodi usano la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> classe, che rappresenta una raccolta di proprietà e i relativi valori. È possibile ottenere PropertyValue per i valori correnti o originali oppure per i valori attualmente archiviati nel database. Ad esempio:

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

Questi oggetti PropertyValue non sono molto utili. Tuttavia, possono essere combinati per eseguire operazioni comuni necessarie per la modifica delle entità. Questa operazione è utile quando si utilizzano oggetti di trasferimento dati e quando si risolvono i [conflitti di concorrenza ottimistica](xref:core/saving/concurrency). Le sezioni seguenti illustrano alcuni esempi.

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a>Impostazione dei valori correnti o originali da un'entità o DTO

I valori correnti o originali di un'entità possono essere aggiornati copiando i valori da un altro oggetto. Si consideri, ad esempio, un `BlogDto` oggetto di trasferimento dati (dto) con le stesse proprietà del tipo di entità:

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

Questa operazione può essere utilizzata per impostare i valori correnti di un'entità rilevata utilizzando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

Questa tecnica viene talvolta utilizzata quando si aggiorna un'entità con valori ottenuti da una chiamata al servizio o da un client in un'applicazione a più livelli. Si noti che l'oggetto utilizzato non deve essere dello stesso tipo dell'entità purché disponga di proprietà i cui nomi corrispondono a quelli dell'entità. Nell'esempio precedente viene usata un'istanza di DTO `BlogDto` per impostare i valori correnti di un'entità rilevata `Blog` .

Si noti che le proprietà verranno contrassegnate come modificate solo se il valore impostato è diverso dal valore corrente.

#### <a name="setting-current-or-original-values-from-a-dictionary"></a>Impostazione dei valori correnti o originali da un dizionario

Nell'esempio precedente i valori sono stati impostati da un'istanza entity o DTO. Lo stesso comportamento è disponibile quando i valori delle proprietà vengono archiviati come coppie nome/valore in un dizionario. Ad esempio:

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a>Impostazione dei valori correnti o originali dal database

I valori correnti o originali di un'entità possono essere aggiornati con i valori più recenti del database chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> o <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> e utilizzando l'oggetto restituito per impostare i valori correnti o originali o entrambi. Ad esempio:

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Creazione di un oggetto clonato contenente i valori correnti, originali o di database

L'oggetto PropertyValues restituito da CurrentValues, OriginalValues o GetDatabaseValues può essere usato per creare un clone dell'entità usando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> . Ad esempio:

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

Si noti che `ToObject` restituisce una nuova istanza di che non viene rilevata da DbContext. Anche l'oggetto restituito non dispone di relazioni impostate su altre entità.

L'oggetto clonato può essere utile per risolvere i problemi relativi agli aggiornamenti simultanei al database, soprattutto quando data binding a oggetti di un determinato tipo. Per ulteriori informazioni, vedere [concorrenza ottimistica](xref:core/saving/concurrency) .

### <a name="working-with-all-navigations-of-an-entity"></a>Uso di tutte le navigazioni di un'entità

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> Restituisce un oggetto <xref:System.Collections.Generic.IEnumerable%601> di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> per ogni navigazione dell'entità. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> ed <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> eseguono la stessa operazione, ma sono limitati rispettivamente alla navigazione di riferimento o di raccolta. Questa operazione può essere utilizzata per eseguire un'azione per ogni navigazione dell'entità. Ad esempio, per forzare il caricamento di tutte le entità correlate:

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a>Utilizzo di tutti i membri di un'entità

Le proprietà regolari e le proprietà di navigazione hanno uno stato e un comportamento diversi. È quindi normale elaborare le esplorazioni e non le navigazioni separatamente, come illustrato nelle sezioni precedenti. Tuttavia, in alcuni casi può essere utile eseguire un'operazione con qualsiasi membro dell'entità, indipendentemente dal fatto che si tratti di una normale proprietà o di una navigazione. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> sono forniti a questo scopo. Ad esempio:

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

L'esecuzione di questo codice in un Blog dall'esempio genera l'output seguente:

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> La [visualizzazione di debug di change tracker Mostra](xref:core/change-tracking/debug-views) informazioni come questa. La visualizzazione di debug per l'intero strumento di rilevamento modifiche viene generata dall'utente singolo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> di ogni entità rilevata.

## <a name="find-and-findasync"></a>Find e FindAsync

<xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> sono progettati per una ricerca efficiente di una singola entità quando la relativa chiave primaria è nota. Trova prima controlla se l'entità è già stata rilevata e, in caso affermativo, restituisce l'entità immediatamente. Una query di database viene eseguita solo se l'entità non viene rilevata localmente. Si consideri, ad esempio, il codice che chiama find due volte per la stessa entità:

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

L'output di questo codice (inclusa la registrazione EF Core) quando si usa SQLite è:

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

Si noti che la prima chiamata non trova l'entità localmente e pertanto esegue una query sul database. Viceversa, la seconda chiamata restituisce la stessa istanza senza eseguire query sul database perché è già stato rilevato.

Find restituisce null se un'entità con la chiave specificata non viene rilevata localmente e non esiste nel database.

### <a name="composite-keys"></a>Chiavi composte

Find può essere usato anche con le chiavi composite. Si consideri ad esempio un' `OrderLine` entità con una chiave composta costituita dall'ID dell'ordine e dall'ID prodotto:

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

La chiave composta deve essere configurata in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> per definire le parti chiave _e il relativo ordine_. Ad esempio:

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

Si noti che `OrderId` è la prima parte della chiave e `ProductId` è la seconda parte della chiave. Questo ordine deve essere utilizzato quando si passano i valori di chiave da trovare. Ad esempio:

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a>Uso di ChangeTracker. entrys per accedere a tutte le entità rilevate

Fino a questo momento è stato eseguito l'accesso solo a un singolo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> alla volta. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> Restituisce un EntityEntry per ogni entità attualmente rilevata da DbContext. Ad esempio:

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

Questo codice genera l'output seguente:

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

Si noti che vengono restituite le voci relative ai Blog e ai post. I risultati possono invece essere filtrati in base a un tipo di entità specifico usando l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> Overload Generico:

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

L'output di questo codice mostra che vengono restituiti solo i post:

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

Inoltre, l'utilizzo dell'overload generico restituisce <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> istanze generiche. Questo è ciò che consente l'accesso di tipo Fluent alla `Id` Proprietà in questo esempio.

Il tipo generico usato per il filtro non deve essere un tipo di entità mappato; è invece possibile usare un'interfaccia o un tipo di base non mappato. Ad esempio, se tutti i tipi di entità nel modello implementano un'interfaccia che definisce la relativa proprietà chiave:

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

Questa interfaccia può quindi essere usata per usare la chiave di qualsiasi entità rilevata in modo fortemente tipizzato. Ad esempio:

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a>Uso di DbSet. local per eseguire query sulle entità rilevate

EF Core query vengono sempre eseguite nel database e restituiscono solo le entità che sono state salvate nel database. <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> fornisce un meccanismo per eseguire una query su DbContext per le entità rilevate locali.

Poiché `DbSet.Local` viene usato per eseguire query sulle entità rilevate, è tipico caricare le entità in DbContext e quindi usare le entità caricate. Questo vale soprattutto per data binding, ma può essere utile anche in altre situazioni. Nel codice seguente, ad esempio, viene innanzitutto eseguita una query sul database per tutti i Blog e i post. Il <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> metodo di estensione viene utilizzato per eseguire la query con i risultati rilevati dal contesto senza essere restituiti direttamente all'applicazione. ( `ToList` L'uso di o simile ha lo stesso effetto ma con l'overhead della creazione dell'elenco restituito, che non è necessario in questo caso). Nell'esempio viene quindi usato `DbSet.Local` per accedere alle entità rilevate localmente:

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

Si noti che, a differenza <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> di, `DbSet.Local` restituisce direttamente le istanze dell'entità. Un EntityEntry può, ovviamente, essere ottenuto sempre per l'entità restituita chiamando <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .

### <a name="the-local-view"></a>Visualizzazione locale

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Restituisce una visualizzazione delle entità rilevate localmente che riflette la corrente <xref:Microsoft.EntityFrameworkCore.EntityState> di tali entità. In particolare, ciò significa che:

- `Added` sono incluse le entità. Si noti che questo non avviene per le normali query di EF Core, poiché `Added` le entità non esistono ancora nel database e pertanto non vengono mai restituite da una query di database.
- `Deleted` le entità sono escluse. Si noti che questo non è il caso per le normali query di EF Core, poiché `Deleted` le entità _sono_ ancora presenti nel database e pertanto vengono restituite dalle query di database.

Ciò significa che viene visualizzato `DbSet.Local` sui dati che riflettono lo stato concettuale corrente dell'entità Graph, con le `Added` entità incluse ed `Deleted` entità escluse. Questo corrisponde allo stato del database previsto dopo la chiamata a SaveChanges.

Si tratta in genere della visualizzazione ideale per data binding, perché presenta all'utente i dati che lo conoscono in base alle modifiche apportate dall'applicazione.

Il codice seguente illustra questa operazione contrassegnando un post come `Deleted` e quindi aggiungendo un nuovo post, contrassegnando come `Added` :

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

L'output di questo codice è:

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

Si noti che il post eliminato viene rimosso dalla visualizzazione locale e il post aggiunto è incluso.

### <a name="using-local-to-add-and-remove-entities"></a>Uso di local per aggiungere e rimuovere entità

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> restituisce un'istanza di <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>. Si tratta di un'implementazione di <xref:System.Collections.Generic.ICollection%601> che genera e risponde alle notifiche quando le entità vengono aggiunte e rimosse dalla raccolta. Questo è lo stesso concetto di <xref:System.Collections.ObjectModel.ObservableCollection%601> , ma implementato come una proiezione su voci di rilevamento delle modifiche EF core esistenti, anziché come una raccolta indipendente.

Le notifiche della visualizzazione locale sono collegate al rilevamento delle modifiche DbContext in modo che la visualizzazione locale rimanga sincronizzata con il DbContext. In particolare:

- L'aggiunta di una nuova entità a `DbSet.Local` ne determina il rilevamento da parte di DbContext, in genere nello `Added` stato. Se l'entità ha già un valore di chiave generato, viene rilevata come in `Unchanged` alternativa.
- La rimozione di un'entità da `DbSet.Local` ne determina la contrassegno come `Deleted` .
- Un'entità che viene rilevata da DbContext verrà automaticamente visualizzata nella `DbSet.Local` raccolta. Se, ad esempio, si esegue una query per importare automaticamente più entità, la visualizzazione locale verrà aggiornata.
- Un'entità contrassegnata come `Deleted` verrà rimossa automaticamente dalla raccolta locale.

Ciò significa che la visualizzazione locale può essere usata per modificare le entità rilevate semplicemente aggiungendo e rimuovendo dalla raccolta. Ad esempio, è possibile modificare il codice di esempio precedente per aggiungere e rimuovere post dalla raccolta locale:

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

L'output rimane invariato rispetto all'esempio precedente, perché le modifiche apportate alla visualizzazione locale vengono sincronizzate con DbContext.

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a>Uso della visualizzazione locale per Windows Form o WPF data binding

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> costituisce la base per data binding per EF Core entità. Tuttavia, sia Windows Form che WPF funzionano meglio se usati con il tipo specifico di raccolta di notifica che prevedono. La visualizzazione locale supporta la creazione di questi tipi di raccolta specifici:

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> Restituisce un oggetto <xref:System.Collections.ObjectModel.ObservableCollection%601> per Data Binding WPF.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> Restituisce un oggetto <xref:System.ComponentModel.BindingList%601> per Windows Form Data Binding.

Ad esempio:

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

Per ulteriori informazioni sui data binding WPF con EF Core, vedere [Introduzione a WPF](xref:core/get-started/wpf) .

> [!TIP]
> La visualizzazione locale per un'istanza di DbSet specificata viene creata in modo differito quando si accede per la prima volta e quindi nella cache. La creazione di LocalView è rapida e non usa memoria significativa. Tuttavia, chiama [DetectChanges](xref:core/change-tracking/change-detection), che può essere lento per un numero elevato di entità. Le raccolte create da `ToObservableCollection` e `ToBindingList` vengono create anche in modalità differita e quindi memorizzate nella cache. Entrambi questi metodi creano nuove raccolte, che possono essere lente e utilizzano una grande quantità di memoria quando sono implicate migliaia di entità.
