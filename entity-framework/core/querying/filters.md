---
title: Filtri di query globali - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664130"
---
# <a name="global-query-filters"></a>Filtri di query globali

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 2.0.

I filtri di query globali sono predicati di query LINQ (un'espressione booleana in genere passata all'operatore di query *Where* di LINQ) applicati ai tipi di entità nel modello di metadati (solitamente in *OnModelCreating*). Questi filtri vengono automaticamente applicati a qualsiasi query LINQ che interessa questi tipi di entità, inclusi quelli a cui si fa riferimento in modo indiretto, ad esempio tramite l'uso di riferimenti alle proprietà Include o di navigazione diretta. Alcune applicazioni comuni di questa funzionalità sono:

* **Eliminazione temporanea**, un tipo di entità definisce una proprietà *IsDeleted*.
* **Multi-tenant** : un tipo di entità definisce una proprietà *TenantId* .

## <a name="example"></a>Esempio

L'esempio seguente mostra come usare i filtri di query globali per implementare i comportamenti di query per eliminazione temporanea e multi-tenancy in un modello di blog semplice.

> [!TIP]
> È possibile visualizzare un [esempio di multi-tenant](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) ed [esempi usando le navigazioni](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) su GitHub. 

Prima di tutto, definire le entità:

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

Si noti la dichiarazione di un campo _tenantId_ per l'entità _Blog_. Questo verrà usato per associare ogni istanza di Blog a un tenant specifico. Viene anche definita una proprietà _IsDeleted_ per il tipo di entità _Post_. Questa proprietà viene usata per tenere traccia dell'eliminazione temporanea di un'istanza di _Post_. L'istanza è contrassegnata come eliminata senza rimuovere fisicamente i dati sottostanti.

A questo punto, configurare i filtri di query in _OnModelCreating_ usando l'API `HasQueryFilter`.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

Le espressioni del predicato passate alle chiamate di _HasQueryFilter_ verranno ora applicate automaticamente a tutte le query LINQ per tali tipi.

> [!TIP]
> Si noti l'uso del campo a livello di istanza di DbContext `_tenantId`, usato per impostare il tenant corrente. I filtri a livello di modello useranno il valore dell'istanza del contesto corretta, ovvero l'istanza che esegue la query.

> [!NOTE]
> Attualmente non è possibile definire più filtri query sulla stessa entità. verrà applicato solo l'ultimo. Tuttavia, è possibile definire un singolo filtro con più condizioni usando l'operatore _and_ logico ([ `&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).

## <a name="use-of-navigations"></a>Uso delle navigazioni

È possibile utilizzare le navigazioni durante la definizione di filtri di query globali. Vengono applicati in modo ricorsivo: quando vengono convertite le navigazioni utilizzate nei filtri di query, vengono applicati anche i filtri di query definiti sulle entità a cui viene fatto riferimento, aggiungendo potenzialmente ulteriori spostamenti.

> [!NOTE]
> Attualmente EF Core non rileva i cicli nelle definizioni di filtro delle query globali, quindi è necessario prestare attenzione durante la definizione. Se specificato in modo non corretto, questo potrebbe causare cicli infiniti durante la conversione delle query.

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a>Accesso all'entità con filtro query tramite la navigazione reqiured

> [!CAUTION]
> L'uso della navigazione necessaria per accedere a un'entità con un filtro di query globale definito può causare risultati imprevisti. 

Per la navigazione obbligatoria si prevede che l'entità correlata sia sempre presente. Se l'entità correlata obbligatoria viene esclusa dal filtro della query, l'entità padre potrebbe finire in uno stato imprevisto. Questo può comportare la restituzione di un numero inferiore di elementi del previsto. 

Per illustrare il problema, è possibile usare le `Blog` `Post` entità e specificate in precedenza e il metodo _OnModelCreating_ seguente:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

È possibile effettuare il seeding del modello con i dati seguenti:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

Il problema può essere rilevato durante l'esecuzione di due query:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

Con questa configurazione, la prima query restituisce tutti i 6 oggetti `Post` , tuttavia la seconda query restituisce solo 3. Questo problema si verifica perché il metodo _include_ nella seconda query carica le `Blog` entità correlate. Poiché la navigazione tra `Blog` e `Post` è obbligatoria, EF core utilizza `INNER JOIN` quando si costruisce la query:

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

L'uso di `INNER JOIN` Filtra tutti i i `Post` cui oggetti correlati `Blog` sono stati rimossi da un filtro di query globale. 

Può essere risolto usando la navigazione facoltativa anziché obbligatoria. In questo modo la prima query rimane invariata, ma la seconda query genera ora `LEFT JOIN` e restituisce 6 risultati.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Un approccio alternativo consiste nello specificare filtri coerenti in entrambe `Blog` le `Post` entità e.
In questo modo, i filtri corrispondenti vengono applicati sia a `Blog` che a `Post` . `Post`gli oggetti che potrebbero finire nello stato imprevisto vengono rimossi ed entrambe le query restituiscono 3 risultati. 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>Disabilitazione dei filtri

È possibile disabilitare i filtri per singole query LINQ usando l'operatore `IgnoreQueryFilters()`.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Limitazioni

I filtri di query globali presentano le limitazioni seguenti:

* I filtri possono essere definiti solo per il tipo di entità radice di una gerarchia di ereditarietà.
