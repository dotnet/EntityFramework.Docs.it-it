---
title: Caricamento eager dei dati correlati-EF Core
description: Caricamento eager di dati correlati con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: bd9c9045c1c2707d69ee4070bea59ad8066789f3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430105"
---
# <a name="eager-loading-of-related-data"></a>Caricamento eager di dati correlati

## <a name="eager-loading"></a>Caricamento eager

È possibile usare il metodo `Include` per specificare i dati correlati da includere nei risultati della query. Nell'esempio seguente la proprietà `Posts` dei blog restituiti nei risultati verrà popolata con i post correlati.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core correggerà automaticamente le proprietà di navigazione per qualsiasi altra entità caricata in precedenza nell'istanza di contesto. Anche se i dati per una proprietà di navigazione non vengono inclusi in modo esplicito, la proprietà può comunque essere popolata se alcune o tutte le entità correlate sono state caricate in precedenza.

È possibile includere dati correlati da più relazioni in una singola query.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> Il caricamento eager di un'esplorazione della raccolta in una singola query può causare problemi di prestazioni. Per altre informazioni, vedere [confronto tra query singole e Split](xref:core/querying/single-split-queries).

## <a name="including-multiple-levels"></a>Inclusione di più livelli

È possibile eseguire il drill-down delle relazioni per includere più livelli di dati correlati tramite il metodo `ThenInclude`. L'esempio seguente carica tutti i blog, i post correlati e l'autore di ogni post.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

È possibile concatenare più chiamate a `ThenInclude` per continuare a includere ulteriori livelli di dati correlati.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

È possibile combinare tutte le chiamate per includere dati correlati da più livelli e più radici nella stessa query.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

È possibile che si vogliano includere più entità correlate per una delle entità incluse. Quando ad esempio si eseguono query per `Blogs`, è necessario includere `Posts` e poi si può anche decidere di includere `Author` e `Tags` per `Posts`. Per includere entrambi, è necessario specificare ogni percorso di inclusione a partire dalla radice. Ad esempio, `Blog -> Posts -> Author` e `Blog -> Posts -> Tags`. Non significa che si otterranno join ridondanti; nella maggior parte dei casi, EF combinerà i join durante la generazione di SQL.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="filtered-include"></a>Inclusione filtrato

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

Quando si applica l'inclusione per caricare dati correlati, è possibile aggiungere determinate operazioni enumerabili alla navigazione della raccolta inclusa, che consente di filtrare e ordinare i risultati.

Le operazioni supportate sono:,,, `Where` `OrderBy` `OrderByDescending` `ThenBy` , `ThenByDescending` , `Skip` e `Take` .

Tali operazioni devono essere applicate alla navigazione della raccolta nell'espressione lambda passata al metodo include, come illustrato nell'esempio seguente:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

Ogni navigazione inclusa consente solo un set univoco di operazioni di filtro. Nei casi in cui vengono applicate più operazioni di inclusione per una determinata navigazione della raccolta ( `blog.Posts` negli esempi seguenti), le operazioni di filtro possono essere specificate solo in uno di essi:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

Al contrario, è possibile applicare operazioni identiche per ogni navigazione inclusa più volte:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> Nel caso di query di rilevamento, i risultati dell'inclusione filtrata possono essere imprevisti a causa della [correzione della navigazione](xref:core/querying/tracking). Tutte le entità rilevanti su cui sono state eseguite query in precedenza e che sono state archiviate in change tracker saranno presenti nei risultati della query di inclusione filtrata, anche se non soddisfano i requisiti del filtro. Si consiglia `NoTracking` di utilizzare le query o di ricreare DbContext quando si utilizza l'inclusione filtrata in tali situazioni.

Esempio:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> In caso di rilevamento di query, viene considerata caricata la navigazione su cui è stato applicato l'inclusione filtrato. Ciò significa che EF Core non tenterà di ricaricare i valori usando il caricamento [esplicito](xref:core/querying/related-data/explicit) o il [caricamento lazy](xref:core/querying/related-data/lazy), anche se alcuni elementi potrebbero ancora essere mancanti.

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
