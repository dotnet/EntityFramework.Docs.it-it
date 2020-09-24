---
title: Caricamento eager dei dati correlati-EF Core
description: Caricamento eager di dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 5ac15a85b28f21588639f34cbaa9ef76f366f7b5
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210467"
---
# <a name="eager-loading-of-related-data"></a>Caricamento eager di dati correlati

## <a name="eager-loading"></a>Caricamento eager

È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query. Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto. Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.

È possibile includere dati correlati da più relazioni in una singola query.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>Inclusione di più livelli

È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`. L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

È possibile combinare tutte le chiamate per includere dati correlati da più livelli e più radici nella stessa query.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

È possibile che si vogliano includere più entità correlate per una delle entità incluse. Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`. Per includere entrambi, è necessario specificare ogni percorso di inclusione a partire dalla radice. Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`. Non significa che si otterranno join ridondanti; nella maggior parte dei casi, EF combinerà i join durante la generazione di SQL.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>Query single e Split

### <a name="single-queries"></a>Query singole

Nei database relazionali, per impostazione predefinita, tutte le entità correlate vengono caricate introducendo i JOIN:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Se in un Blog tipico sono presenti più post correlati, le righe per questi post duplicano le informazioni del Blog, causando il cosiddetto problema "esplosione cartesiana". Man mano che vengono caricate più relazioni uno-a-molti, la quantità di dati duplicati può aumentare e influire negativamente sulle prestazioni dell'applicazione. Per impostazione predefinita, EF Core genera un avviso se rileva le query che caricano la raccolta, incluse le quali possono causare problemi di prestazioni.

### <a name="split-queries"></a>Suddividere query

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

EF consente di specificare che una query LINQ specificata deve essere *suddivisa* in più query SQL. Anziché JOIN, le query suddivise eseguono una query SQL aggiuntiva per ogni spostamento uno-a-molti incluso:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

Verrà generato il codice SQL seguente:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> Le entità correlate uno-a-uno vengono sempre caricate tramite JOIN nella stessa query, in quanto non ha alcun effetto sulle prestazioni.

### <a name="enabling-split-queries-globally"></a>Abilitazione di query Split a livello globale

È anche possibile configurare le query Split come predefinite per il contesto dell'applicazione:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Quando le query suddivise sono configurate come predefinite, è comunque possibile configurare query specifiche da eseguire come query singole:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSingleQuery&highlight=5)]

Se la modalità di suddivisione delle query non è specificata in modo esplicito, né globalmente né nella query, né EF Core rileva che una singola query carica più inclusioni di raccolta, viene generato un avviso per attirare l'attenzione sui potenziali problemi di prestazioni. Se si imposta la modalità di query su SingleQuery, l'avviso non verrà generato.

### <a name="characteristics-of-split-queries"></a>Caratteristiche delle query suddivise

Mentre Split query evita i problemi di prestazioni associati a JOIN e l'esplosione cartesiana, presenta anche alcuni svantaggi:

* Anche se la maggior parte dei database garantisce la coerenza dei dati per le singole query, non esistono garanzie di questo tipo per più query. Se il database viene aggiornato simultaneamente durante l'esecuzione delle query, i dati risultanti potrebbero non essere coerenti. È possibile mitigarlo eseguendo il wrapping delle query in una transazione serializzabile o snapshot, anche se in questo modo è possibile creare problemi di prestazioni propri. Per ulteriori informazioni, vedere la documentazione del database.
* Ogni query implica attualmente un round trip di rete aggiuntivo al database. Il round trip della rete può compromettere le prestazioni, soprattutto se la latenza del database è elevata, ad esempio servizi cloud.
* Sebbene alcuni database consentano di usare contemporaneamente i risultati di più query (SQL Server con MARS, SQLite), la maggior parte consente di rendere attiva una sola query in un determinato punto. Tutti i risultati delle query precedenti devono quindi essere memorizzati nel buffer nella memoria dell'applicazione prima di eseguire query successive, il che comporta un aumento dei requisiti di memoria.

Sfortunatamente, non esiste una strategia per il caricamento di entità correlate che soddisfino tutti gli scenari. Valutare con attenzione i vantaggi e gli svantaggi delle query singole e divise e selezionare quello che soddisfa le proprie esigenze.

## <a name="filtered-include"></a>Inclusione filtrato

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

Quando si applica l'inclusione per caricare dati correlati, è possibile applicare determinate operazioni enumerabili sulla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.

Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .

Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro. Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

Al contrario, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](xref:core/querying/tracking). Tutte le entità rilevanti su cui sono state eseguite query in precedenza e che sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro. Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.

Esempio:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a>Inclusione per i tipi derivati

È possibile includere dati correlati dalla navigazione definita solo in un tipo derivato usando `Include` e `ThenInclude` .

Dato il modello seguente:

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

Il contenuto della navigazione `School` di tutte le entità People che sono Student può essere caricato in modalità eager usando vari modelli:

* Cast

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* Operatore `as`

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* Overload di `Include` che accetta parametri di tipo `string`

  ```csharp
  context.People.Include("School").ToList()
  ```
  
