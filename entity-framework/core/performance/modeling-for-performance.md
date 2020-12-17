---
title: Modellazione per le prestazioni-EF Core
description: Modellazione efficiente quando si usa Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/modeling-for-performance
ms.openlocfilehash: fc16ec67c3865aa7b7a95519463ca7493a2709b0
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657858"
---
# <a name="modeling-for-performance"></a>Modellazione per le prestazioni

In molti casi, la modalità di modellazione può avere un impatto significativo sulle prestazioni dell'applicazione. mentre un modello correttamente normalizzato e "corretto" è in genere un buon punto di partenza, in applicazioni reali alcuni compromessi pragmatici possono andare a lungo per ottenere prestazioni ottimali. Poiché è piuttosto difficile modificare il modello dopo che un'applicazione è in esecuzione nell'ambiente di produzione, è opportuno tenere in considerazione le prestazioni durante la creazione del modello iniziale.

## <a name="denormalization-and-caching"></a>Denormalizzazione e memorizzazione nella cache

La *denormalizzazione* è la pratica di aggiunta di dati ridondanti allo schema, in genere per eliminare i join quando si eseguono query. Ad esempio, per un modello con Blog e post, in cui ogni post ha una classificazione, potrebbe essere necessario visualizzare spesso la valutazione media del Blog. L'approccio semplice a questo consente di raggruppare i post in base al Blog e calcolare la media come parte della query; Tuttavia è necessario un join costoso tra le due tabelle. La denormalizzazione aggiungerebbe la media calcolata di tutti i post a una nuova colonna nel Blog, in modo che sia immediatamente accessibile, senza Unione o calcolo.

Il precedente può essere visualizzato come una forma di *caching* : le informazioni di aggregazione dei post vengono memorizzate nella cache nel Blog; Analogamente a quanto avviene per la memorizzazione nella cache, il problema è il modo in cui il valore memorizzato nella cache è aggiornato con i dati memorizzati nella cache. In molti casi, è accettabile che i dati memorizzati nella cache siano in ritardo per un po'; Nell'esempio precedente, ad esempio, è generalmente ragionevole che la valutazione media del Blog non sia completamente aggiornata in un dato momento. In tal caso, è possibile ricalcolarla ogni ora e quindi; in caso contrario, è necessario configurare un sistema più elaborato per mantenere aggiornati i valori memorizzati nella cache.

Di seguito sono riportate alcune tecniche per la denormalizzazione e la memorizzazione nella cache in EF Core e vengono indicate le sezioni pertinenti nella documentazione.

### <a name="stored-computed-columns"></a>Colonne calcolate archiviate

Se i dati da memorizzare nella cache sono un prodotto di altre colonne nella stessa tabella, una [colonna calcolata archiviata](xref:core/modeling/generated-properties#computed-columns) può essere una soluzione perfetta. Ad esempio, un oggetto `Customer` può `FirstName` avere `LastName` colonne e, ma potrebbe essere necessario eseguire una ricerca in base al *nome completo* del cliente. Una colonna calcolata archiviata viene gestita automaticamente dal database, che lo Ricalcola ogni volta che viene modificata la riga, ed è anche possibile definire un indice su di esso per velocizzare le query.

### <a name="update-cache-columns-when-inputs-change"></a>Aggiornare le colonne della cache quando gli input cambiano

Se la colonna memorizzata nella cache deve fare riferimento a input dall'esterno della riga della tabella, non è possibile utilizzare le colonne calcolate. Tuttavia, è comunque possibile ricalcolare la colonna ogni volta che viene modificato l'input; ad esempio, è possibile ricalcolare la valutazione media del blog ogni volta che un post viene modificato, aggiunto o rimosso. Assicurarsi di identificare le condizioni esatte quando è necessario il ricalcolo; in caso contrario, il valore memorizzato nella cache non sarà sincronizzato.

Un modo per eseguire questa operazione consiste nell'eseguire l'aggiornamento autonomamente tramite l'API di EF Core normale. `SaveChanges`[Gli eventi o gli](xref:core/logging-events-diagnostics/events) [intercettori](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) possono essere usati per verificare automaticamente se sono in corso l'aggiornamento di un post e per eseguire il ricalcolo in questo modo. Si noti che questa operazione comporta in genere un round trip aggiuntivo del database, in quanto è necessario inviare comandi aggiuntivi.

Per altre applicazioni sensibili alle prestazioni, è possibile definire trigger di database per eseguire automaticamente il ricalcolo nel database. Questo consente di salvare il database aggiuntivo round trip, si verifica automaticamente all'interno della stessa transazione dell'aggiornamento principale e può essere più semplice da configurare. EF non fornisce alcuna API specifica per la creazione o la gestione dei trigger, ma è perfetto per [creare una migrazione vuota e aggiungere la definizione del trigger tramite SQL non elaborato](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

### <a name="materialized-views"></a>Viste materializzate

Le viste materializzate sono simili alle visualizzazioni regolari, ad eccezione del fatto che i dati vengono archiviati su disco ("materializzato"), anziché calcolati ogni volta che viene eseguita una query sulla vista. Questo strumento è utile quando non si desidera semplicemente aggiungere una singola colonna della cache a un database esistente, ma si desidera memorizzare nella cache l'intero set di risultati dei risultati di una query complessa e costosa, come se si trattasse di una normale tabella. Questi risultati possono quindi essere sottoposti a query in modo molto conveniente senza alcun calcolo o join. Diversamente dalle colonne calcolate, le viste materializzate non vengono aggiornate automaticamente quando cambiano le tabelle sottostanti. è necessario aggiornarle manualmente. Se i dati memorizzati nella cache possono ritardare, l'aggiornamento della vista può essere eseguito tramite un timer. un'altra opzione consiste nell'impostare i trigger del database per esaminare una vista materializzata quando si verificano determinati eventi del database.

EF non fornisce attualmente alcuna API specifica per la creazione o la gestione delle visualizzazioni, materializzata o in altro modo; Tuttavia, è possibile [creare una migrazione vuota e aggiungere la definizione della vista tramite SQL non elaborato](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

## <a name="inheritance-mapping"></a>Mapping ereditarietà

Prima di continuare con questa sezione, è consigliabile leggere [la pagina dedicata sull'ereditarietà](xref:core/modeling/inheritance) .

EF Core supporta attualmente due tecniche per il mapping di un modello di ereditarietà a un database relazionale:

* **Tabella per gerarchia** (TPH), in cui viene eseguito il mapping di un'intera gerarchia di classi .NET a una singola tabella di database
* **Tabella per tipo** (TPT), in cui ogni tipo nella gerarchia .NET è mappato a una tabella diversa del database.

La scelta della tecnica di mapping dell'ereditarietà può avere un impatto significativo sulle prestazioni dell'applicazione. è consigliabile misurare attentamente prima di eseguire il commit a una scelta.

Talvolta le persone scelgono TPT perché sembra essere la tecnica "più pulita"; una tabella separata per ogni tipo .NET rende lo schema del database simile alla gerarchia dei tipi .NET. Poiché, inoltre, TPH deve rappresentare l'intera gerarchia in una singola tabella, le righe hanno *tutte* le colonne indipendentemente dal tipo effettivamente contenuto nella riga e le colonne non correlate sono sempre vuote e inutilizzate. Oltre a sembrare una tecnica di mapping "non pulita", molti ritengono che tali colonne vuote occupino una notevole quantità di spazio nel database e possano danneggiare anche le prestazioni.

Tuttavia, la misurazione Mostra che TPT è nella maggior parte dei casi la tecnica di mapping inferiore dal punto di vista delle prestazioni. Quando tutti i dati in TPH provengono da una singola tabella, le query TPT devono essere unite in join a più tabelle e i join rappresentano una delle principali fonti di problemi di prestazioni nei database relazionali. Inoltre, in genere i database tendono a gestire correttamente le colonne vuote e le funzionalità come [SQL Server colonne di tipo sparse](/sql/relational-databases/tables/use-sparse-columns) possono ridurre ulteriormente questo overhead.

Per un esempio concreto, [vedere questo benchmark](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/Inheritance.cs) che configura un modello semplice con una gerarchia di 7 tipi. 5000 righe vengono sottoposte a seeding per ogni tipo, per un totale di 35000 righe. il benchmark caricherà semplicemente tutte le righe dal database:

| Metodo |     Media |   Errore |  StdDev |     Generazione 0 |     Generazione 1 |     Generazione 2 | Allocato |
|------- |---------:|--------:|--------:|----------:|----------:|----------:|----------:|
|    TPH | 132,3 ms | 2,29 ms | 2,03 MS | 8000,0000 | 3000,0000 | 1250,0000 |  44,49 MB |
|    TPT | 201,3 ms | 3,32 ms | 3,10 ms | 9000,0000 | 4000,0000 |         - |  61,84 MB |

Come si può notare, TPH è molto più efficiente di TPT per questo scenario. Si noti che i risultati effettivi dipendono sempre dalla query specifica eseguita e dal numero di tabelle nella gerarchia, in modo che le altre query possano mostrare un gap di prestazioni diverso. si consiglia di usare questo codice di benchmark come modello per il test di altre query.
