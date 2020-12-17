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
# <a name="efficient-updating"></a><span data-ttu-id="cc2a9-103">Aggiornamento efficiente</span><span class="sxs-lookup"><span data-stu-id="cc2a9-103">Efficient Updating</span></span>

## <a name="batching"></a><span data-ttu-id="cc2a9-104">Creazione di batch</span><span class="sxs-lookup"><span data-stu-id="cc2a9-104">Batching</span></span>

<span data-ttu-id="cc2a9-105">EF Core aiuta a ridurre al minimo round trip raggruppando automaticamente tutti gli aggiornamenti in un singolo round trip.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-105">EF Core helps minimize roundtrips by automatically batching together all updates in a single roundtrip.</span></span> <span data-ttu-id="cc2a9-106">Considerare quanto segue:</span><span class="sxs-lookup"><span data-stu-id="cc2a9-106">Consider the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

<span data-ttu-id="cc2a9-107">Il precedente carica un Blog dal database, ne modifica il nome e quindi aggiunge due nuovi Blog; per applicare questo, due istruzioni SQL INSERT e un'istruzione UPDATE vengono inviate al database.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-107">The above loads a blog from the database, changes its name, and then adds two new blogs; to apply this, two SQL INSERT statements and one UPDATE statement are sent to the database.</span></span> <span data-ttu-id="cc2a9-108">Anziché inviarli uno alla volta, le istanze di Blog sono state aggiunte, EF Core tiene traccia internamente di queste modifiche e le esegue in un singolo round trip quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-108">Rather than sending them one by one, as Blog instances are added, EF Core tracks these changes internally, and executes them in a single roundtrip when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="cc2a9-109">Il numero di istruzioni che EF batch in un singolo round trip dipende dal provider di database utilizzato.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-109">The number of statements that EF batches in a single roundtrip depends on the database provider being used.</span></span> <span data-ttu-id="cc2a9-110">L'analisi delle prestazioni, ad esempio, ha mostrato che la suddivisione in batch è in genere meno efficiente per SQL Server quando sono presenti meno di 4 istruzioni.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-110">For example, performance analysis has shown batching to be generally less efficient for SQL Server when less than 4 statements are involved.</span></span> <span data-ttu-id="cc2a9-111">Analogamente, i vantaggi derivanti dall'invio in batch delle istruzioni 40 per il SQL Server, quindi EF Core eseguiranno per impostazione predefinita solo 42 istruzioni in un unico batch ed eseguiranno istruzioni aggiuntive in round trip separate.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-111">Similarly, the benefits of batching degrade after around 40 statements for SQL Server, so EF Core will by default only execute up to 42 statements in a single batch, and execute additional statements in separate roundtrips.</span></span>

<span data-ttu-id="cc2a9-112">Gli utenti possono anche modificare queste soglie per ottenere prestazioni potenzialmente migliori, ma con particolare attenzione al benchmark prima di modificarle:</span><span class="sxs-lookup"><span data-stu-id="cc2a9-112">Users can also tweak these thresholds to achieve potentially higher performance - but benchmark carefully before modifying these:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a><span data-ttu-id="cc2a9-113">Aggiornamenti in blocco</span><span class="sxs-lookup"><span data-stu-id="cc2a9-113">Bulk updates</span></span>

<span data-ttu-id="cc2a9-114">Supponiamo di voler concedere a tutti i dipendenti un aumento.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-114">Let's assume you want to give all your employees a raise.</span></span> <span data-ttu-id="cc2a9-115">Un'implementazione tipica di questo tipo di EF Core avrà un aspetto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="cc2a9-115">A typical implementation for this in EF Core would look like the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

<span data-ttu-id="cc2a9-116">Anche se si tratta di codice perfettamente valido, analizziamo il risultato da un punto di vista delle prestazioni:</span><span class="sxs-lookup"><span data-stu-id="cc2a9-116">While this is perfectly valid code, let's analyze what it does from a performance perspective:</span></span>

* <span data-ttu-id="cc2a9-117">Viene eseguito un round trip del database per caricare tutti i dipendenti pertinenti; Si noti che questo porta tutti i dati delle righe dei dipendenti al client, anche se è necessario solo lo stipendio.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-117">A database roundtrip is performed, to load all the relevant employees; note that this brings all the Employees' row data to the client, even if only the salary will be needed.</span></span>
* <span data-ttu-id="cc2a9-118">Il rilevamento delle modifiche di EF Core crea snapshot durante il caricamento delle entità, quindi confronta tali snapshot con le istanze per individuare le proprietà modificate.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-118">EF Core's change tracking creates snapshots when loading the entities, and then compares those snapshots to the instances to find out which properties changed.</span></span>
* <span data-ttu-id="cc2a9-119">Viene eseguito un secondo round trip del database per salvare tutte le modifiche.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-119">A second database roundtrip is performed to save all the changes.</span></span> <span data-ttu-id="cc2a9-120">Mentre tutte le modifiche vengono eseguite in un singolo round trip grazie alla suddivisione in batch, EF Core Invia comunque un'istruzione UPDATE per dipendente, che deve essere eseguita dal database.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-120">While all changes are done in a single roundtrip thanks to batching, EF Core still sends an UPDATE statement per employee, which must be executed by the database.</span></span>

<span data-ttu-id="cc2a9-121">I database relazionali supportano anche *gli aggiornamenti bulk*, quindi è possibile riscrivere il codice precedente come la singola istruzione SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="cc2a9-121">Relational databases also support *bulk updates*, so the above could be rewritten as the following single SQL statement:</span></span>

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

<span data-ttu-id="cc2a9-122">In questo modo l'intera operazione viene eseguita in un singolo round trip, senza caricare o inviare dati effettivi al database e senza usare il meccanismo di rilevamento delle modifiche di EF, che impone un sovraccarico aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-122">This performs the entire operation in a single roundtrip, without loading or sending any actual data to the database, and without making use of EF's change tracking machinery, which imposes an additional overhead.</span></span>

<span data-ttu-id="cc2a9-123">Sfortunatamente, EF non fornisce attualmente API per l'esecuzione di aggiornamenti in blocco.</span><span class="sxs-lookup"><span data-stu-id="cc2a9-123">Unfortunately, EF doesn't currently provide APIs for performing bulk updates.</span></span> <span data-ttu-id="cc2a9-124">Fino a quando non vengono introdotte, è possibile usare SQL non elaborato per eseguire l'operazione in cui le prestazioni sono sensibili:</span><span class="sxs-lookup"><span data-stu-id="cc2a9-124">Until these are introduced, you can use raw SQL to perform the operation where performance is sensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
