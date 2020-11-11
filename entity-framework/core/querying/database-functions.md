---
title: Funzioni di database-EF Core
description: Informazioni sulle funzioni di database supportate in EF Core Translation query
author: smitpatel
ms.date: 11/10/2020
uid: core/querying/database-functions
ms.openlocfilehash: 34ced2a602168f6ed84763c046ef581cb87026e8
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503798"
---
# <a name="database-functions"></a>Funzioni di database

Le funzioni di database sono il database equivalente ai [metodi C#](/dotnet/csharp/programming-guide/classes-and-structs/methods). Una funzione di database può essere richiamata con zero o più parametri e calcola il risultato in base ai valori dei parametri. La maggior parte dei database, che usano SQL per eseguire query, dispone del supporto per le funzioni di database. SQL generato da EF Core Translation query consente inoltre di richiamare funzioni di database. I metodi C# non devono tradurre esclusivamente in funzioni di database in EF Core.

- Un metodo C# non può avere una funzione di database equivalente.
  - <xref:System.String.IsNullOrEmpty%2A?displayProperty=nameWithType> il metodo viene convertito in un controllo null e un confronto con una stringa vuota nel database piuttosto che come funzione.
  - <xref:System.String.Equals(System.String,System.StringComparison)?displayProperty=nameWithType> il metodo non ha un database equivalente perché il confronto tra stringhe non può essere rappresentato o imitato facilmente in un database.
- Una funzione di database non può avere un metodo C# equivalente. L' `??` operatore in C#, che non dispone di alcun metodo, viene convertito nella `COALESCE` funzione nel database.

## <a name="types-of-database-functions"></a>Tipi di funzioni di database

EF Core generazione SQL supporta un subset di funzioni che possono essere utilizzate nei database di. Questa limitazione deriva dalla possibilità di rappresentare una query in LINQ per la funzione di database specificata. Inoltre, ogni database ha un supporto variabile per le funzioni di database, pertanto EF Core fornisce un subset comune. Un provider di database è libero di estendere EF Core generazione SQL per supportare più modelli. Di seguito sono riportati i tipi di funzioni di database EF Core supporta e identifica in modo univoco. Questi termini contribuiscono anche a comprendere quali sono le traduzioni integrate con i provider di EF Core.

### <a name="built-in-vs-user-defined-functions"></a>Funzioni predefinite di Visual Studio definite dall'utente

Le funzioni predefinite sono disponibili con il database predefinito, ma le funzioni definite dall'utente vengono definite in modo esplicito dall'utente nel database. Quando EF Core converte le query per l'utilizzo di funzioni di database, utilizza funzioni predefinite per assicurarsi che la funzione sia sempre disponibile nel database. La distinzione delle funzioni predefinite è necessaria in alcuni database per generare correttamente SQL. Per SqlServer, ad esempio, è necessario che ogni funzione definita dall'utente venga richiamata con un nome qualificato dallo schema. Tuttavia, le funzioni predefinite in SqlServer non dispongono di uno schema. PostgreSQL definisce la funzione incorporata nello `public` schema, ma possono essere richiamati con nomi qualificati dallo schema.

### <a name="aggregate-vs-scalar-vs-table-valued-functions"></a>Funzioni di aggregazione e funzioni con valori di tabella

- Le funzioni scalari accettano valori scalari, ad esempio Integer o stringhe come parametri, e restituiscono come risultato un valore scalare. Le funzioni scalari possono essere utilizzate in qualsiasi punto di SQL in cui è possibile passare un valore scalare.
- Le funzioni di aggregazione accettano un flusso di valori scalari come parametri e restituiscono un valore scalare come risultato. Le funzioni di aggregazione vengono applicate all'intero set di risultati della query o a un gruppo di valori generati dall'operatore di applicazione `GROUP BY` .
- Le funzioni con valori di tabella accettano valori scalari come parametri e restituiscono come risultato un flusso di righe. Le funzioni con valori di tabella vengono utilizzate come una clausola di origine della tabella `FROM` .

### <a name="niladic-functions"></a>Funzioni senza parametri

Le funzioni senza parametri sono funzioni di database speciali che non dispongono di parametri e che devono essere richiamate senza parentesi. Sono simili all'accesso a proprietà/campi in un'istanza in C#. Le funzioni senza parametri differiscono dalle funzioni senza parametri, poiché quest'ultima richiede parentesi vuote. Non esiste un nome speciale per le funzioni di database che richiedono sempre le parentesi. Un altro subset di funzioni di database in base al numero di parametri è Variadic funzioni. Quando vengono richiamate, le funzioni Variadic possono assumere un numero variabile di parametri.

## <a name="database-function-mappings-in-ef-core"></a>Mapping di funzioni di database in EF Core

EF Core supporta tre diverse modalità di mapping tra funzioni C# e funzioni di database.

### <a name="built-in-function-mapping"></a>Mapping delle funzioni predefinite

Per impostazione predefinita EF Core provider forniscono i mapping per varie funzioni predefinite rispetto ai tipi primitivi. Ad esempio, viene <xref:System.String.ToLower?displayProperty=nameWithType> convertito `LOWER` in in SqlServer. Questa funzionalità consente agli utenti di scrivere facilmente query in LINQ. Viene in genere fornita una traduzione nel database che restituisce lo stesso risultato fornito dalla funzione C# sul lato client. A volte, per ottenere questo risultato, la traduzione effettiva potrebbe essere più complessa rispetto a una funzione di database. In alcuni scenari, viene anche fornita la traduzione più appropriata anziché la semantica C# corrispondente. La stessa funzionalità viene usata anche per fornire traduzioni comuni per alcuni degli accessi ai Membri C#. Ad esempio, viene <xref:System.String.Length?displayProperty=nameWithType> convertito `LEN` in in SqlServer. Oltre ai provider, i writer di plug-in possono anche aggiungere altre traduzioni. Questa estendibilità è utile quando i plug-in aggiungono il supporto per più tipi come tipi primitivi e vogliono tradurre metodi su di essi.

### <a name="effunctions-mapping"></a>EF. Mapping di funzioni

Poiché non tutte le funzioni di database hanno funzioni C# equivalenti, i provider EF Core hanno metodi C# speciali per richiamare determinate funzioni di database. Questi metodi sono definiti come metodi di estensione su `EF.Functions` per essere usati nelle query LINQ. Questi metodi sono specifici del provider perché sono strettamente legati a determinate funzioni di database. Pertanto, un metodo che funziona per un provider probabilmente non funzionerà per nessun altro provider. Poiché l'intenzione di questi metodi è di richiamare una funzione di database nella query tradotta, il tentativo di valutarli sul client genera un'eccezione.

### <a name="user-defined-function-mapping"></a>Mapping di funzioni definite dall'utente

Oltre ai mapping forniti dai provider di EF Core, gli utenti possono anche definire il mapping personalizzato. Un mapping definito dall'utente estende la conversione delle query in base alle esigenze dell'utente. Questa funzionalità è utile quando nel database sono presenti funzioni definite dall'utente, che l'utente desidera richiamare dalla query LINQ.

## <a name="see-also"></a>Vedi anche

- [SqlServer mapping delle funzioni predefinite](xref:core/providers/sql-server/functions)
- [Mapping delle funzioni predefinite SQLite](xref:core/providers/sqlite/functions)
- [Mapping delle funzioni predefinite di Cosmos](xref:core/providers/cosmos/functions)
