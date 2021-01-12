---
title: Programmazione asincrona-EF Core
description: Esecuzione di query e salvataggio asincrono dei dati con Entity Framework Core
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: 52b3ac20a50babbed6937ebe3365ac1947dcaef1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128576"
---
# <a name="asynchronous-programming"></a>Programmazione asincrona

Le operazioni asincrone evitano il blocco di un thread mentre la query viene eseguita nel database. Le operazioni asincrone sono importanti per mantenere un'interfaccia utente reattiva nelle applicazioni rich client e possono anche aumentare la velocità effettiva nelle applicazioni Web in cui liberano il thread per soddisfare altre richieste nelle applicazioni Web.

In base a .NET standard, EF Core fornisce controparti asincrone a tutti i metodi sincroni che eseguono operazioni di I/O. Questi hanno gli stessi effetti dei metodi di sincronizzazione e possono essere usati con le `async` `await` parole chiave C# e. Ad esempio, invece di usare DbContext. SaveChanges, che bloccherà un thread durante l'esecuzione dell'I/O del database, è possibile usare DbContext. SaveChangesAsync:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

Per ulteriori informazioni, vedere [la documentazione generale per la programmazione asincrona in C#](/dotnet/csharp/async).

> [!WARNING]
> EF Core non supporta l'esecuzione di più operazioni parallele nella stessa istanza del contesto. È sempre necessario attendere il completamento di un'operazione prima di iniziare l'operazione successiva. Questa operazione viene in genere eseguita usando la `await` parola chiave in ogni operazione asincrona.

> [!WARNING]
> Sfortunatamente, l'implementazione asincrona di [Microsoft. Data. SqlClient](https://github.com/dotnet/SqlClient) presenta alcuni problemi noti (ad esempio [#593](https://github.com/dotnet/SqlClient/issues/593), [#601](https://github.com/dotnet/SqlClient/issues/601)e altri).

> [!NOTE]
> EF Core passa i token di annullamento al provider di database sottostante in uso (ad esempio, Microsoft. Data. SqlClient). Questi token possono o meno essere rispettati. consultare la documentazione del provider di database.

## <a name="async-linq-operators"></a>Operatori LINQ asincroni

Per supportare l'esecuzione asincrona di query LINQ, EF Core fornisce un set di metodi di estensione asincroni che eseguono la query e restituiscono risultati. Queste controparti per gli operatori LINQ standard e sincroni includono ToListAsync, SingleAsync, AsAsyncEnumerable e così via:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

Si noti che non sono presenti versioni asincrone di alcuni operatori LINQ, ad esempio WHERE o OrderBy, perché questi compilano solo l'albero delle espressioni LINQ e non causano l'esecuzione della query nel database. Solo gli operatori che provocano l'esecuzione di query hanno controparti asincrone.

> [!IMPORTANT]
> I metodi di estensione asincroni di EF Core sono definiti nello spazio dei nomi `Microsoft.EntityFrameworkCore`. Questo spazio dei nomi deve essere importato affinché i metodi siano disponibili.

## <a name="client-side-async-linq-operators"></a>Operatori LINQ asincroni sul lato client

Gli operatori LINQ asincroni descritti in precedenza possono essere usati solo in query EF. non è possibile usarli con la query LINQ to Objects sul lato client. Per eseguire operazioni LINQ asincrone sul lato client al di fuori di EF, usare il [pacchetto System. Interactive. Async.](https://www.nuget.org/packages/System.Interactive.Async) Questo pacchetto può essere particolarmente utile per l'esecuzione di operazioni sul client che non possono essere convertite per la valutazione nel server.

Sfortunatamente, il riferimento a System. Interactive. Async causa errori di compilazione ambigui per gli operatori LINQ applicati a DbSet di EF; Ciò rende difficile l'uso di EF e System. Interactive. Async nello stesso progetto. Per risolvere questo problema, aggiungere AsQueryable alla DbSet:

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
