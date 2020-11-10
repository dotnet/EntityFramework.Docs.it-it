---
title: Filtri di query globali - EF Core
description: Uso di filtri di query globali per filtrare i risultati con Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 6436f9f8e2e09d44ef9528fd2022720d40095fe0
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430131"
---
# <a name="global-query-filters"></a>Filtri di query globali

I filtri di query globali sono predicati di query LINQ applicati ai tipi di entità nel modello di metadati (in genere in `OnModelCreating` ). Un predicato di query è un'espressione booleana che in genere viene passata all' `Where` operatore di query LINQ.  EF Core applica automaticamente tali filtri alle query LINQ che coinvolgono tali tipi di entità.  EF Core li applica anche ai tipi di entità, a cui viene fatto riferimento indirettamente tramite l'utilizzo della proprietà di navigazione o di inclusione. Alcune applicazioni comuni di questa funzionalità sono:

* **Soft delete** : un tipo di entità definisce una `IsDeleted` Proprietà.
* **Multi-tenant** : un tipo di entità definisce una `TenantId` Proprietà.

## <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come utilizzare i filtri di query globali per implementare comportamenti di query di multi-tenant e di eliminazione temporanea in un semplice modello di Blog.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) di questo articolo in GitHub.

Prima di tutto, definire le entità:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

Si noti la dichiarazione di un `_tenantId` campo nell' `Blog` entità. Questo campo verrà usato per associare ogni istanza di Blog a un tenant specifico. Definito anche come `IsDeleted` proprietà nel tipo di `Post` entità. Questa proprietà viene usata per tenere traccia del fatto che un'istanza post è stata "eliminata temporaneamente". L'istanza è contrassegnata come eliminata senza rimuovere fisicamente i dati sottostanti.

Configurare quindi i filtri query in `OnModelCreating` usando l' `HasQueryFilter` API.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

Le espressioni del predicato passate alle `HasQueryFilter` chiamate verranno ora applicate automaticamente a qualsiasi query LINQ per tali tipi.

> [!TIP]
> Si noti l'uso del campo a livello di istanza di DbContext `_tenantId`, usato per impostare il tenant corrente. I filtri a livello di modello useranno il valore dell'istanza del contesto corretta, ovvero l'istanza che esegue la query.

> [!NOTE]
> Attualmente non è possibile definire più filtri query sulla stessa entità. verrà applicato solo l'ultimo. Tuttavia, è possibile definire un singolo filtro con più condizioni usando l' `AND` operatore logico ([ `&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).

## <a name="use-of-navigations"></a>Uso delle navigazioni

È inoltre possibile utilizzare le navigazioni nella definizione dei filtri di query globali. Se si utilizzano le navigazioni in filtro query, i filtri query verranno applicati in modo ricorsivo. Quando EF Core espande le navigazioni utilizzate nei filtri di query, verranno applicati anche i filtri di query definiti sulle entità a cui si fa riferimento.

Per illustrare questa configurazione, configurare i filtri query in `OnModelCreating` nel modo seguente: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]

Eseguire quindi una query per tutte le `Blog` entità: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]

Questa query produce il codice SQL seguente che applica i filtri di query definiti per le `Blog` `Post` entità e:

```sql
SELECT [b].[BlogId], [b].[Name], [b].[Url]
FROM [Blogs] AS [b]
WHERE (
    SELECT COUNT(*)
    FROM [Posts] AS [p]
    WHERE ([p].[Title] LIKE N'%fish%') AND ([b].[BlogId] = [p].[BlogId])) > 0
```

> [!NOTE]
> Attualmente EF Core non rileva i cicli nelle definizioni di filtro delle query globali, quindi è necessario prestare attenzione durante la definizione. Se specificato in modo non corretto, i cicli potrebbero causare cicli infiniti durante la conversione delle query.

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>Accesso all'entità con filtro query tramite la navigazione obbligatoria

> [!CAUTION]
> L'uso della navigazione necessaria per accedere a un'entità con un filtro di query globale definito può causare risultati imprevisti.

Per la navigazione obbligatoria si prevede che l'entità correlata sia sempre presente. Se l'entità correlata necessaria viene esclusa dal filtro di query, l'entità padre non sarà nel risultato. Quindi, è possibile ottenere meno elementi del previsto.

Per illustrare il problema, è possibile usare le `Blog` `Post` entità e specificate in precedenza e il `OnModelCreating` metodo seguente:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

È possibile effettuare il seeding del modello con i dati seguenti:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

Il problema può essere rilevato durante l'esecuzione di due query:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

Con l'installazione precedente, la prima query restituisce tutti i 6 `Post` , tuttavia la seconda query restituisce solo 3. Questa mancata corrispondenza si verifica perché `Include` il metodo nella seconda query carica le `Blog` entità correlate. Poiché la navigazione tra `Blog` e `Post` è obbligatoria, EF core utilizza `INNER JOIN` quando si costruisce la query:

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

L'uso di `INNER JOIN` Filtra tutti i i `Post` cui oggetti correlati `Blog` sono stati rimossi da un filtro di query globale.

Può essere risolto usando la navigazione facoltativa anziché obbligatoria.
In questo modo la prima query rimane invariata, ma la seconda query genera ora `LEFT JOIN` e restituisce 6 risultati.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

Un approccio alternativo consiste nello specificare filtri coerenti in entrambe `Blog` le `Post` entità e.
In questo modo, i filtri corrispondenti vengono applicati sia a `Blog` che a `Post` . `Post`gli oggetti che potrebbero finire nello stato imprevisto vengono rimossi ed entrambe le query restituiscono 3 risultati.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a>Disabilitazione dei filtri

È possibile disabilitare i filtri per singole query LINQ usando l'operatore <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Limitazioni

I filtri di query globali presentano le limitazioni seguenti:

* I filtri possono essere definiti solo per il tipo di entità radice di una gerarchia di ereditarietà.
