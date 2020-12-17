---
title: Argomenti relativi alle prestazioni avanzate
description: Argomenti relativi alle prestazioni avanzate per Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657873"
---
# <a name="advanced-performance-topics"></a>Argomenti relativi alle prestazioni avanzate

## <a name="dbcontext-pooling"></a>Pooling DbContext

`AddDbContextPool` consente di raggruppare le `DbContext` istanze. Il pool di contesto può aumentare la velocità effettiva in scenari a scalabilità elevata, ad esempio i server Web riutilizzando le istanze di contesto, anziché creare nuove istanze per ogni richiesta.

Il modello tipico in un'app ASP.NET Core con EF Core prevede la registrazione di un <xref:Microsoft.EntityFrameworkCore.DbContext> tipo personalizzato nel contenitore di [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) e l'ottenimento di istanze del tipo tramite i parametri del costruttore nei controller o Razor Pages. Con l'inserimento del costruttore viene creata una nuova istanza del contesto per ogni richiesta.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> Abilita un pool di istanze di contesto riutilizzabili. Per utilizzare il pool di contesto, utilizzare il `AddDbContextPool` metodo anziché `AddDbContext` durante la registrazione del servizio:

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Quando `AddDbContextPool` si usa, nel momento in cui viene richiesta un'istanza del contesto, EF verifica innanzitutto se è disponibile un'istanza nel pool. Dopo che l'elaborazione della richiesta è stata finalizzata, lo stato dell'istanza viene reimpostato e quest'ultima viene restituita al pool.

Questa situazione è concettualmente simile al modo in cui il pool di connessioni opera nei provider ADO.NET e offre il vantaggio di risparmiare parte del costo di inizializzazione dell'istanza del contesto.

Il `poolSize` parametro di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> imposta il numero massimo di istanze conservate dal pool. Una volta `poolSize` superato il limite, le nuove istanze del contesto non vengono memorizzate nella cache e EF esegue il fallback al comportamento non in pool della creazione di istanze su richiesta.

### <a name="limitations"></a>Limitazioni

Le app devono essere profilate e testate per mostrare che l'inizializzazione del contesto è un costo significativo.

`AddDbContextPool` presenta alcune limitazioni sulle operazioni che possono essere eseguite nel `OnConfiguring` metodo del contesto.

> [!WARNING]
> Evitare di usare il pool di contesto nelle app che mantengono lo stato. Ad esempio, i campi privati nel contesto che non devono essere condivisi tra le richieste. EF Core reimposta solo lo stato di cui è consapevole prima di aggiungere un'istanza del contesto al pool.

Il pool di contesto funziona riutilizzando la stessa istanza del contesto tra le richieste. Ciò significa che viene registrato in modo efficace come [singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in termini di istanza stessa, in modo che sia in grado di renderlo persistente.

Il pool di contesto è destinato agli scenari in cui la configurazione del contesto, che include servizi risolti, viene fissata tra le richieste. Per i casi in cui i servizi con [ambito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) sono obbligatori o è necessario modificare la configurazione, non usare il pool. Il miglioramento delle prestazioni del pool è in genere trascurabile tranne che negli scenari altamente ottimizzati.

## <a name="query-caching-and-parameterization"></a>Caching e parametrizzazione delle query

Quando EF riceve un albero di query LINQ per l'esecuzione, deve prima di tutto "compilare" tale albero in una query SQL. Poiché si tratta di un processo intenso, EF memorizza nella cache le query in base alla *forma* della struttura ad albero della query: le query con la stessa struttura riutilizzano gli output di compilazione memorizzati nella cache internamente e possono ignorare la compilazione Le diverse query possono comunque fare riferimento a *valori* diversi, ma purché questi valori siano parametrizzati correttamente, la struttura è la stessa e la memorizzazione nella cache funzionerà correttamente.

Si considerino le due query seguenti:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

Poiché gli alberi delle espressioni contengono costanti diverse, l'albero delle espressioni è diverso e ognuna di queste query verrà compilata separatamente da EF Core. Ogni query, inoltre, genera un comando SQL leggermente diverso:

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

Poiché SQL differisce, il server di database probabilmente dovrà anche produrre un piano di query per entrambe le query, anziché riutilizzare lo stesso piano.

Una piccola modifica alle query può variare notevolmente:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

Poiché il nome del Blog è ora *parametrizzato*, entrambe le query hanno la stessa forma struttura ad albero e EF deve essere compilato solo una volta. Anche l'oggetto SQL prodotto è parametrizzato, consentendo al database di riutilizzare lo stesso piano di query:

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

Si noti che non è necessario parametrizzare ogni query: è perfettamente necessario avere alcune query con costanti e, in realtà, i database e EF possono talvolta eseguire determinate operazioni di ottimizzazione per le costanti che non sono possibili quando la query è parametrizzata. Vedere la sezione sulle [query costruite in modo dinamico](#dynamically-constructed-queries) per un esempio in cui la parametrizzazione corretta è fondamentale.

> [!NOTE]
> I [contatori degli eventi](xref:core/logging-events-diagnostics/event-counters) di EF Core segnalano la percentuale di riscontri nella cache di query. In un'applicazione normale questo contatore raggiunge il 100% subito dopo l'avvio del programma, una volta che la maggior parte delle query è stata eseguita almeno una volta. Se questo contatore rimane stabile al di sotto del 100%, significa che l'applicazione potrebbe eseguire una cosa che sconfigge la cache delle query. è consigliabile esaminarla.

> [!NOTE]
> Il modo in cui il database gestisce i piani di query della cache è dipendente dal database. Ad esempio, SQL Server gestisce in modo implicito una cache dei piani di query LRU, mentre PostgreSQL non (ma le istruzioni preparate possono produrre un effetto finale molto simile). Per ulteriori informazioni, consultare la documentazione del database.

## <a name="dynamically-constructed-queries"></a>Query costruite in modo dinamico

In alcune situazioni, è necessario costruire dinamicamente query LINQ anziché specificarle in codice sorgente. Questo può accadere, ad esempio, in un sito Web che riceve informazioni di query arbitrarie da un client, con operatori di query aperti (ordinamento, filtro, paging...). In linea di massima, se eseguita correttamente, le query costruite in modo dinamico possono essere altrettanto efficienti di quelle regolari (anche se non è possibile usare l'ottimizzazione della query compilata con le query dinamiche). In pratica, tuttavia, spesso sono l'origine dei problemi di prestazioni, poiché è facile creare accidentalmente alberi delle espressioni con forme diverse ogni volta.

Nell'esempio seguente vengono utilizzate due tecniche per costruire dinamicamente una query; viene aggiunto un `Where` operatore alla query solo se il parametro specificato non è null. Si noti che questo non è un caso d'uso valido per la costruzione dinamica di una query, ma per semplicità:

### <a name="with-constant"></a>[Con costante](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[Con parametro](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

Il benchmarking di queste due tecniche offre i risultati seguenti:

|        Metodo |       Media |    Errore |    StdDev |   Generazione 0 |  Generazione 1 | Generazione 2 | Allocato |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  WithConstant | 1.096,7 US | 12,54 US |  11,12 US | 13,6719 | 1,9531 |     - |  83,91 KB |
| WithParameter |   570,8 US | 42,43 US | 124,43 US |  5,8594 |      - |     - |  37,16 KB |

Anche se la differenza di sottomillisecondi sembra piccola, tenere presente che la versione costante inquina continuamente la cache e causa la ricompilazione di altre query, rallentando anche le altre query.

> [!NOTE]
> Evitare di creare query con l'API dell'albero delle espressioni a meno che non sia effettivamente necessario. Oltre alla complessità dell'API, è molto facile provocare inavvertitamente problemi significativi in termini di prestazioni quando vengono utilizzati.
