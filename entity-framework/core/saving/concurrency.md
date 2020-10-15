---
title: Gestione dei conflitti di concorrenza - EF Core
description: Gestione dei conflitti quando gli stessi dati vengono aggiornati contemporaneamente a Entity Framework Core
author: ajcvickers
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: b596a99db431331bb12a28fc6ddc06f1c941b67c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063023"
---
# <a name="handling-concurrency-conflicts"></a>Gestione dei conflitti di concorrenza

> [!NOTE]
> Questa pagina illustra il funzionamento della concorrenza in EF Core e come gestire i conflitti di concorrenza nell'applicazione. Vedere [Token di concorrenza](xref:core/modeling/concurrency) per informazioni dettagliate su come configurare i token di concorrenza nel modello.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) di questo articolo in GitHub.

Con _concorrenza del database_ si fa riferimento alle situazioni in cui più processi o utenti accedono agli stessi dati in un database o li modificano nello stesso momento. Con _controllo della concorrenza_ si fa riferimento ai meccanismi specifici usati per garantire la coerenza dei dati in presenza di modifiche simultanee.

EF Core implementa il _controllo della concorrenza ottimistica_, ovvero consentirà a più processi o utenti di apportare modifiche in modo indipendente senza l'overhead di sincronizzazione o blocco. In una situazione ideale, queste modifiche non interferiranno l'una con l'altra e quindi potranno essere completate. Nel caso peggiore, due o più processi tenteranno di apportare modifiche in conflitto e solo uno di essi avrà esito positivo.

## <a name="how-concurrency-control-works-in-ef-core"></a>Funzionamento del controllo della concorrenza in EF Core

Le proprietà configurate come token di concorrenza vengono usate per implementare il controllo della concorrenza ottimistica: ogni volta che un'operazione di aggiornamento o eliminazione viene eseguita durante `SaveChanges`, il valore del token di concorrenza nel database viene confrontato con il valore originale letto da EF Core.

- Se i valori corrispondono, l'operazione può essere completata.
- Se i valori non corrispondono, EF Core presuppone che un altro utente abbia eseguito un'operazione in conflitto e interrompe la transazione corrente.

La situazione in cui un altro utente ha eseguito un'operazione in conflitto con l'operazione corrente è nota come _conflitto di concorrenza_.

I provider di database sono responsabili dell'implementazione del confronto dei valori dei token di concorrenza.

Nei database relazionali EF Core include un controllo per il valore del token di concorrenza nella clausola `WHERE` di qualsiasi istruzione `UPDATE` o `DELETE`. Dopo avere eseguito le istruzioni, EF Core legge il numero di righe interessate.

Se nessuna riga è interessata, viene rilevato un conflitto di concorrenza ed EF Core genera `DbUpdateConcurrencyException`.

Ad esempio, si può decidere di configurare `LastName` per `Person` come token di concorrenza. Qualsiasi operazione di aggiornamento su Person includerà il controllo della concorrenza nella clausola `WHERE`:

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>Risoluzione dei conflitti di concorrenza

Continuando con l'esempio precedente, se un utente tenta di salvare alcune modifiche apportate a `Person`, ma un altro utente ha già cambiato `LastName` verrà generata un'eccezione.

A questo punto, l'applicazione potrebbe semplicemente informare l'utente che l'aggiornamento non è riuscito a causa di modifiche in conflitto e passare oltre. Potrebbe però essere opportuno richiedere all'utente di verificare che il record rappresenti ancora la stessa persona effettiva e ripetere l'operazione.

Questo processo è un esempio di _risoluzione di un conflitto di concorrenza_.

La risoluzione di un conflitto di concorrenza comporta l'unione delle modifiche in sospeso dal `DbContext` corrente con i valori nel database. I valori che vengono uniti varieranno in base all'applicazione e potrebbero dipendere dall'input dell'utente.

**Sono disponibili tre set di valori per risolvere un conflitto di concorrenza:**

- I **valori correnti** sono i valori che l'applicazione stava tentando di scrivere nel database.
- I **valori originali** sono i valori che sono stati originariamente recuperati dal database, prima di apportare eventuali modifiche.
- I **valori del database** sono i valori attualmente archiviati nel database.

L'approccio generale per gestire i conflitti di concorrenza è il seguente:

1. Intercettare `DbUpdateConcurrencyException` durante `SaveChanges`.
2. Usare `DbUpdateConcurrencyException.Entries` per preparare un nuovo set di modifiche per le entità interessate.
3. Aggiornare i valori originali del token di concorrenza in modo da riflettere i valori correnti nel database.
4. Ripetere il processo fino a quando non si verificano conflitti.

Nell'esempio seguente, `Person.FirstName` e `Person.LastName` vengono configurati come token di concorrenza. È presente un commento `// TODO:` nella posizione in cui viene inclusa la logica specifica dell'applicazione per scegliere il valore da salvare.

[!code-csharp[Main](../../../samples/core/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=33-34)]
