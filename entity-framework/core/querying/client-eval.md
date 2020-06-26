---
title: Valutazione client e server-EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 429fec2d2da6eb8910ac20531455ec8183096ec6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370422"
---
# <a name="client-vs-server-evaluation"></a>Valutazione client e server

Come regola generale, Entity Framework Core tenta di valutare il più possibile una query sul server. EF Core converte parti della query in parametri, che possono essere valutati sul lato client. Il resto della query (insieme ai parametri generati) viene assegnato al provider di database per determinare la query di database equivalente da valutare nel server. EF Core supporta la valutazione parziale del client nella proiezione di primo livello (essenzialmente, l'ultima chiamata a `Select()` ). Se la proiezione di primo livello nella query non può essere convertita nel server, EF Core recupererà tutti i dati richiesti dal server e valuterà le parti rimanenti della query nel client. Se EF Core rileva un'espressione, in una posizione diversa dalla proiezione di primo livello, che non può essere convertita nel server, viene generata un'eccezione in fase di esecuzione. Vedere [il funzionamento delle query](xref:core/querying/how-query-works) per comprendere in che modo EF Core determina le operazioni che non possono essere convertite nel server.

> [!NOTE]
> Prima della versione 3,0, Entity Framework Core la valutazione client supportata in qualsiasi punto della query. Per ulteriori informazioni, vedere la [sezione versioni precedenti](#previous-versions).

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) di questo articolo in GitHub.

## <a name="client-evaluation-in-the-top-level-projection"></a>Valutazione client nella proiezione di primo livello

Nell'esempio seguente viene usato un metodo helper per standardizzare gli URL per i Blog restituiti da un database SQL Server. Poiché il provider di SQL Server non ha informazioni dettagliate sul modo in cui viene implementato questo metodo, non è possibile convertirlo in SQL. Tutti gli altri aspetti della query vengono valutati nel database, ma il passaggio dell'oggetto restituito `URL` tramite questo metodo viene eseguito nel client.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>Valutazione client non supportata

Sebbene la valutazione del client sia utile, può comportare una riduzione delle prestazioni a volte. Si consideri la query seguente, in cui il metodo helper viene ora usato in un filtro WHERE. Poiché il filtro non può essere applicato nel database, è necessario eseguire il pull di tutti i dati in memoria per applicare il filtro nel client. In base al filtro e alla quantità di dati sul server, la valutazione client può comportare una riduzione delle prestazioni. Quindi Entity Framework Core blocca la valutazione del client e genera un'eccezione in fase di esecuzione.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>Valutazione esplicita del client

Potrebbe essere necessario forzare la valutazione del client in modo esplicito in alcuni casi come segue

- La quantità di dati è ridotta, quindi la valutazione del client non comporta un notevole calo delle prestazioni.
- L'operatore LINQ utilizzato non ha alcuna conversione sul lato server.

In questi casi, è possibile acconsentire esplicitamente alla valutazione del client chiamando metodi come `AsEnumerable` o `ToList` ( `AsAsyncEnumerable` o `ToListAsync` per Async). `AsEnumerable`Se si usa, i risultati vengono trasmessi in streaming, ma l'uso `ToList` di causerebbe la memorizzazione nel buffer creando un elenco, che richiede anche memoria aggiuntiva. Tuttavia, se l'enumerazione viene eseguita più volte, l'archiviazione dei risultati in un elenco contribuisce maggiormente perché esiste solo una query al database. A seconda dell'utilizzo specifico, è consigliabile valutare quale metodo è più utile per il caso.

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>Potenziale perdita di memoria nella valutazione client

Poiché la conversione e la compilazione di query sono costose, EF Core memorizza nella cache il piano di query compilato. Il delegato memorizzato nella cache può usare il codice client durante la valutazione client della proiezione di primo livello. EF Core genera parametri per le parti valutate dal client dell'albero di e riutilizza il piano di query sostituendo i valori dei parametri. Tuttavia, alcune costanti nell'albero delle espressioni non possono essere convertite in parametri. Se il delegato memorizzato nella cache contiene tali costanti, tali oggetti non possono essere sottoposti a Garbage Collection perché sono ancora a cui viene fatto riferimento. Se un oggetto di questo tipo contiene un DbContext o altri servizi, può causare un aumento dell'utilizzo della memoria dell'app nel tempo. Questo comportamento è in genere un segno di una perdita di memoria. EF Core genera un'eccezione ogni volta che viene rilevata una costante di un tipo che non può essere mappato utilizzando il provider di database corrente. Di seguito sono riportate le cause più comuni e le relative soluzioni:

- **Uso di un metodo di istanza**: quando si usano i metodi di istanza in una proiezione client, l'albero delle espressioni contiene una costante dell'istanza. Se il metodo non utilizza dati provenienti dall'istanza, provare a rendere statico il metodo. Se sono necessari dati dell'istanza nel corpo del metodo, passare i dati specifici come argomento al metodo.
- **Passaggio di argomenti costanti al metodo**: questo caso si verifica generalmente usando `this` in un argomento del metodo client. Provare a suddividere l'argomento in in più argomenti scalari, che possono essere mappati dal provider di database.
- **Altre costanti**: se una costante si trova in un altro caso, è possibile valutare se la costante è necessaria nell'elaborazione. Se è necessario disporre della costante o se non è possibile usare una soluzione nei casi precedenti, creare una variabile locale per archiviare il valore e usare la variabile locale nella query. EF Core convertirà la variabile locale in un parametro.

## <a name="previous-versions"></a>Versioni precedenti

La sezione seguente si applica alle versioni EF Core precedenti 3,0.

Le versioni precedenti di EF Core supportano la valutazione client in qualsiasi parte della query, non solo la proiezione di primo livello. Per questo motivo, le query simili a quelle pubblicate sotto la sezione di [valutazione client non supportata](#unsupported-client-evaluation) hanno funzionato correttamente. Poiché questo comportamento potrebbe causare problemi di prestazioni inosservati, EF Core registrato un avviso di valutazione del client. Per ulteriori informazioni sulla visualizzazione dell'output di registrazione, vedere [registrazione](xref:core/miscellaneous/logging).

Facoltativamente EF Core consentito modificare il comportamento predefinito in modo da generare un'eccezione o non eseguire alcuna operazione durante la valutazione del client (ad eccezione di nella proiezione). Il comportamento di generazione delle eccezioni lo renderebbe simile al comportamento in 3,0. Per modificare il comportamento, è necessario configurare gli avvisi durante la configurazione delle opzioni per il contesto, in genere in `DbContext.OnConfiguring` o in `Startup.cs` se si usa ASP.NET Core.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
