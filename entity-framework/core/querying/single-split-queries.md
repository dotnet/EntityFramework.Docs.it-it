---
title: Query single vs. Split-EF Core
description: Conversione di query in query singole e divise in SQL con Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 8615e7ba5247a90a1f980e9f7b1b23c81170971f
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128992"
---
# <a name="split-queries"></a>Suddividere le query

## <a name="single-queries"></a>Query singole

Nei database relazionali tutte le entità correlate vengono caricate introducendo JOIN in una singola query.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Se in un Blog tipico sono presenti più post correlati, le righe per questi post duplicano le informazioni del Blog. Questa duplicazione porta al cosiddetto problema "esplosione cartesiana". Man mano che vengono caricate più relazioni uno-a-molti, la quantità di dati duplicati può aumentare e influire negativamente sulle prestazioni dell'applicazione.

## <a name="split-queries"></a>Suddividere le query

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0. Funziona solo quando si usa `Include` . [Questo problema](https://github.com/dotnet/efcore/issues/21234) tiene traccia del supporto per la query Split durante il caricamento dei dati correlati nella proiezione senza `Include` .

EF consente di specificare che una query LINQ specificata deve essere *suddivisa* in più query SQL. Anziché JOIN, le query suddivise generano una query SQL aggiuntiva per ogni navigazione della raccolta inclusa:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

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

## <a name="enabling-split-queries-globally"></a>Abilitazione di query Split a livello globale

È anche possibile configurare le query Split come predefinite per il contesto dell'applicazione:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Quando le query suddivise sono configurate come predefinite, è comunque possibile configurare query specifiche da eseguire come query singole:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

Per impostazione predefinita, EF Core usa la modalità query singola in assenza di qualsiasi configurazione. Poiché può causare problemi di prestazioni, EF Core genera un avviso ogni volta che vengono soddisfatte le condizioni seguenti:

- EF Core rileva che la query carica più raccolte.
- L'utente non ha configurato la modalità di suddivisione delle query a livello globale.
- L'utente non ha usato `AsSingleQuery` / `AsSplitQuery` l'operatore nella query.

Per disabilitare l'avviso, configurare la modalità di suddivisione delle query a livello globale o a livello di query su un valore appropriato.

## <a name="characteristics-of-split-queries"></a>Caratteristiche delle query suddivise

Mentre Split query evita i problemi di prestazioni associati a JOIN e l'esplosione cartesiana, presenta anche alcuni svantaggi:

- Anche se la maggior parte dei database garantisce la coerenza dei dati per le singole query, non esistono garanzie di questo tipo per più query. Se il database viene aggiornato simultaneamente durante l'esecuzione delle query, i dati risultanti potrebbero non essere coerenti. È possibile mitigarlo eseguendo il wrapping delle query in una transazione serializzabile o snapshot, anche se in questo modo è possibile creare problemi di prestazioni propri. Per ulteriori informazioni, vedere la documentazione del database.
- Ogni query implica attualmente un round trip di rete aggiuntivo al database. Il round trip della rete può compromettere le prestazioni, soprattutto se la latenza del database è elevata, ad esempio servizi cloud.
- Sebbene alcuni database consentano di usare contemporaneamente i risultati di più query (SQL Server con MARS, SQLite), la maggior parte consente di rendere attiva una sola query in un determinato punto. Tutti i risultati delle query precedenti devono quindi essere memorizzati nel buffer nella memoria dell'applicazione prima di eseguire query successive, il che comporta un aumento dei requisiti di memoria.

Sfortunatamente, non esiste una strategia per il caricamento di entità correlate che soddisfino tutti gli scenari. Valutare con attenzione i vantaggi e gli svantaggi delle query singole e divise per selezionare quello che soddisfa le proprie esigenze.
