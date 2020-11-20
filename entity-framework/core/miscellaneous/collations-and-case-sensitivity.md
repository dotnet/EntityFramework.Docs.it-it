---
title: Regole di confronto e distinzione maiuscole/minuscole-EF Core
description: Configurazione delle regole di confronto e distinzione tra maiuscole e minuscole nel database e nelle query con Entity Framework Core
author: roji
ms.date: 04/27/2020
uid: core/miscellaneous/collations-and-case-sensitivity
ms.openlocfilehash: eca68af6e658f76e1480b1e1083212f160fa765c
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003458"
---
# <a name="collations-and-case-sensitivity"></a>Regole di confronto e distinzione tra maiuscole e minuscole

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

L'elaborazione del testo nei database può essere complessa e richiede un maggior intervento da parte dell'utente. Per una cosa, i database variano notevolmente in base alla modalità di gestione del testo. ad esempio, mentre alcuni database fanno distinzione tra maiuscole e minuscole per impostazione predefinita (ad esempio SQLite, PostgreSQL), altri non fanno distinzione tra maiuscole e minuscole (SQL Server, MySQL). Inoltre, a causa dell'utilizzo di indici, la distinzione tra maiuscole e minuscole e gli aspetti simili possono avere un notevole effetto sulle prestazioni delle query: Sebbene sia possibile tentare di utilizzare per forzare un confronto senza distinzione tra maiuscole e minuscole in un database con distinzione tra maiuscole e minuscole `string.Lower` , questa operazione può impedire all'applicazione di utilizzare gli indici. In questa pagina viene illustrato in dettaglio come configurare la distinzione tra maiuscole e minuscole, o più in generale, le regole di confronto e come eseguire questa operazione in modo efficiente senza compromettere le prestazioni delle query.

## <a name="introduction-to-collations"></a>Introduzione alle regole di confronto

Un concetto fondamentale nell'elaborazione del testo sono le regole di *confronto*, ovvero un set di regole che determinano il modo in cui i valori di testo vengono ordinati e confrontati per verificare l'uguaglianza. Se, ad esempio, le regole di confronto senza distinzione tra maiuscole e minuscole ignorano le differenze tra lettere maiuscole e minuscole ai fini del confronto di uguaglianza, non vengono applicate regole di confronto con distinzione tra maiuscole e minuscole. Tuttavia, poiché la distinzione tra maiuscole e minuscole è dipendente dalle impostazioni cultura (ad esempio `i` e `I` rappresenta una lettera diversa in turco), esistono più regole di confronto senza distinzione tra maiuscole e minuscole, ognuna con un proprio set di regole. L'ambito delle regole di confronto si estende anche oltre la distinzione tra maiuscole e minuscole e altri aspetti dei dati di tipo carattere. in tedesco, ad esempio, è a volte, ma non sempre, auspicabile da trattare `ä` e `ae` come identico. Infine, le regole di confronto definiscono anche il modo in cui vengono *ordinati* i valori di testo: mentre `ä` il tedesco si trova dopo `a` , lo svedese lo inserisce alla fine dell'alfabeto.

Tutte le operazioni di testo in un database utilizzano regole di confronto, in modo esplicito o implicito, per determinare il modo in cui l'operazione Confronta e ordina le stringhe. L'elenco effettivo delle regole di confronto disponibili e i relativi schemi di denominazione sono specifici del database. consultare [la sezione seguente](#database-specific-information) per collegamenti a pagine di documentazione rilevanti di diversi database. Fortunatamente, il database in genere consente di definire regole di confronto predefinite a livello di database o di colonna e di specificare in modo esplicito le regole di confronto da utilizzare per operazioni specifiche in una query.

## <a name="database-collation"></a>Regole di confronto del database

Nella maggior parte dei sistemi di database, le regole di confronto predefinite vengono definite a livello di database. a meno che non venga sottoposto a override, le regole di confronto si applicano in modo implicito a tutte le operazioni di testo che si verificano Le regole di confronto del database vengono in genere impostate al momento della creazione del database (tramite l' `CREATE DATABASE` istruzione DDL). se non è specificato, il valore predefinito è un valore a livello di server determinato al momento dell'installazione. Ad esempio, le regole di confronto predefinite a livello di server in SQL Server sono `SQL_Latin1_General_CP1_CI_AS` , ovvero regole di confronto con distinzione tra maiuscole e minuscole e con distinzione tra caratteri accentati. Sebbene i sistemi di database consentano in genere di modificare le regole di confronto di un database esistente, questo può causare complicazioni. è consigliabile selezionare le regole di confronto prima della creazione del database.

Quando si utilizzano migrazioni di EF Core per gestire lo schema del database, nel metodo del modello viene `OnModelCreating` configurato un database SQL Server per l'utilizzo di regole di confronto con distinzione tra maiuscole e minuscole:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>Regole di confronto a livello di colonna

È inoltre possibile definire le regole di confronto per le colonne di testo, eseguendo l'override dell'impostazione predefinita del database. Questo può essere utile se alcune colonne non devono fare distinzione tra maiuscole e minuscole, mentre il resto del database deve fare distinzione tra maiuscole e minuscole.

Quando si utilizzano migrazioni di EF Core per gestire lo schema del database, il codice seguente configura la colonna per la proprietà in modo che non venga fatta distinzione tra maiuscole e minuscole `Name` in un database che altrimenti è configurato per la distinzione tra maiuscole e minuscole:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>Regole di confronto esplicite in una query

In alcuni casi, è necessario eseguire query sulla stessa colonna utilizzando regole di confronto diverse in base a query diverse. Ad esempio, una query potrebbe dover eseguire un confronto con distinzione tra maiuscole e minuscole in una colonna, mentre un altro potrebbe dover eseguire un confronto senza distinzione tra maiuscole e minuscole nella stessa colonna. Questa operazione può essere eseguita specificando in modo esplicito le regole di confronto all'interno della query stessa:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

Viene generata una `COLLATE` clausola nella query SQL che applica regole di confronto con distinzione tra maiuscole e minuscole, indipendentemente dalle regole di confronto definite a livello di colonna o di database:

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>Regole di confronto esplicite e indici

Gli indici sono uno dei fattori più importanti nelle prestazioni del database. una query che viene eseguita in modo efficiente con un indice può frantumarsi senza tale indice. Gli indici ereditano in modo implicito le regole di confronto della colonna; Ciò significa che tutte le query sulla colonna sono automaticamente idonee per l'utilizzo di indici definiti nella colonna, purché la query non specifichi regole di confronto diverse. La specifica di regole di confronto esplicite in una query in genere impedisce a tale query di utilizzare un indice definito in tale colonna, poiché le regole di confronto non corrispondono più. è quindi consigliabile prestare attenzione quando si usa questa funzionalità. È sempre preferibile definire le regole di confronto a livello di colonna (o database), consentendo a tutte le query di utilizzare in modo implicito tali regole di confronto e trarre vantaggio da qualsiasi indice.

Si noti che alcuni database consentono di definire le regole di confronto durante la creazione di un indice, ad esempio PostgreSQL, SQLite. In questo modo è possibile definire più indici nella stessa colonna, velocizzando le operazioni con regole di confronto diverse, ad esempio i confronti con distinzione tra maiuscole e minuscole e senza distinzione tra maiuscole e minuscole. Per ulteriori informazioni, consultare la documentazione del provider di database.

> [!WARNING]
> Controllare sempre i piani di query delle query e verificare che gli indici corretti vengano usati nelle query critiche per le prestazioni in esecuzione su grandi quantità di dati. L'override della distinzione tra maiuscole e minuscole in una query tramite `EF.Functions.Collate` (o chiamando `string.ToLower` ) può avere un impatto significativo sulle prestazioni dell'applicazione.

## <a name="translation-of-built-in-net-string-operations"></a>Conversione di operazioni di stringa .NET predefinite

Per impostazione predefinita, in .NET l'uguaglianza di stringhe fa distinzione tra maiuscole e minuscole: `s1 == s2` esegue un confronto ordinale che richiede che le stringhe siano identiche. Poiché le regole di confronto predefinite dei database variano e, poiché è auspicabile un'uguaglianza semplice per l'utilizzo degli indici, EF Core non tenta di convertire l'uguaglianza semplice in un'operazione con distinzione tra maiuscole e minuscole nel database: l'uguaglianza di C# viene convertita direttamente nell'uguaglianza SQL, che può o meno fare distinzione tra maiuscole e minuscole, a seconda del database specifico in uso e

Inoltre, .NET fornisce overload di [`string.Equals`](/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) accettazione di un' [`StringComparison`](/dotnet/api/system.stringcomparison) enumerazione, che consente di specificare la distinzione tra maiuscole e minuscole e le impostazioni cultura per il confronto. Per impostazione predefinita, EF Core si evita di tradurre questi overload in SQL e il tentativo di usarli comporta un'eccezione. Per una cosa, EF Core non conosce quali regole di confronto con distinzione tra maiuscole e minuscole o senza distinzione tra maiuscole e minuscole. Ancora più importante, l'applicazione di regole di confronto nella maggior parte dei casi impedisce l'utilizzo di indici, influire significativamente sulle prestazioni per un costrutto .NET molto semplice e di uso comune. Per forzare una query a usare il confronto tra maiuscole e minuscole o senza distinzione tra maiuscole e minuscole, specificare le regole di confronto in modo esplicito tramite, `EF.Functions.Collate` come [descritto](#explicit-collations-and-indexes)

## <a name="additional-resources"></a>Risorse aggiuntive

### <a name="database-specific-information"></a>Informazioni specifiche del database

* [SQL Server documentazione sulle regole di confronto](/sql/relational-databases/collations/collation-and-unicode-support).
* [Documentazione di Microsoft. Data. sqlite sulle regole di confronto](/dotnet/standard/data/sqlite/collation).
* [Documentazione di PostgreSQL sulle regole di confronto](https://www.postgresql.org/docs/current/collation.html).
* [Documentazione di MySQL sulle regole di confronto](https://dev.mysql.com/doc/refman/en/charset-general.html).

### <a name="other-resources"></a>Altre risorse

* [EF Core sessione della community standup](https://www.youtube.com/watch?v=OgMhLVa_VfA&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=1), introducendo regole di confronto ed esplorando gli aspetti delle prestazioni e dell'indicizzazione.
