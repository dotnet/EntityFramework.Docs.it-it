---
title: Aggiornamento efficiente-EF Core
description: Guida alle prestazioni per un aggiornamento efficiente con Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: 92766d2339fb04ed5ebc3123429171cc9be424b1
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657864"
---
# <a name="efficient-updating"></a>Aggiornamento efficiente

## <a name="batching"></a>Creazione di batch

EF Core aiuta a ridurre al minimo round trip raggruppando automaticamente tutti gli aggiornamenti in un singolo round trip. Considerare quanto segue:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

Il precedente carica un Blog dal database, ne modifica il nome e quindi aggiunge due nuovi Blog; per applicare questo, due istruzioni SQL INSERT e un'istruzione UPDATE vengono inviate al database. Anziché inviarli uno alla volta, le istanze di Blog sono state aggiunte, EF Core tiene traccia internamente di queste modifiche e le esegue in un singolo round trip quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.

Il numero di istruzioni che EF batch in un singolo round trip dipende dal provider di database utilizzato. L'analisi delle prestazioni, ad esempio, ha mostrato che la suddivisione in batch è in genere meno efficiente per SQL Server quando sono presenti meno di 4 istruzioni. Analogamente, i vantaggi derivanti dall'invio in batch delle istruzioni 40 per il SQL Server, quindi EF Core eseguiranno per impostazione predefinita solo 42 istruzioni in un unico batch ed eseguiranno istruzioni aggiuntive in round trip separate.

Gli utenti possono anche modificare queste soglie per ottenere prestazioni potenzialmente migliori, ma con particolare attenzione al benchmark prima di modificarle:

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a>Aggiornamenti in blocco

Supponiamo di voler concedere a tutti i dipendenti un aumento. Un'implementazione tipica di questo tipo di EF Core avrà un aspetto simile al seguente:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

Anche se si tratta di codice perfettamente valido, analizziamo il risultato da un punto di vista delle prestazioni:

* Viene eseguito un round trip del database per caricare tutti i dipendenti pertinenti; Si noti che questo porta tutti i dati delle righe dei dipendenti al client, anche se è necessario solo lo stipendio.
* Il rilevamento delle modifiche di EF Core crea snapshot durante il caricamento delle entità, quindi confronta tali snapshot con le istanze per individuare le proprietà modificate.
* Viene eseguito un secondo round trip del database per salvare tutte le modifiche. Mentre tutte le modifiche vengono eseguite in un singolo round trip grazie alla suddivisione in batch, EF Core Invia comunque un'istruzione UPDATE per dipendente, che deve essere eseguita dal database.

I database relazionali supportano anche *gli aggiornamenti bulk*, quindi è possibile riscrivere il codice precedente come la singola istruzione SQL seguente:

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

In questo modo l'intera operazione viene eseguita in un singolo round trip, senza caricare o inviare dati effettivi al database e senza usare il meccanismo di rilevamento delle modifiche di EF, che impone un sovraccarico aggiuntivo.

Sfortunatamente, EF non fornisce attualmente API per l'esecuzione di aggiornamenti in blocco. Fino a quando non vengono introdotte, è possibile usare SQL non elaborato per eseguire l'operazione in cui le prestazioni sono sensibili:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
