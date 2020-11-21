---
title: Transazioni - EF Core
description: Gestione delle transazioni per l'atomicità quando si salvano i dati con Entity Framework Core
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: b5e1fa2a0bcc466f22f03fee7ecaef9dcea1efaf
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003549"
---
# <a name="using-transactions"></a>Utilizzo di transazioni

Le transazioni consentono di elaborare varie operazioni di database in modo atomico. Se viene eseguito il commit della transazione, tutte le operazioni vengono applicate correttamente nel database. Se viene eseguito il rollback della transazione, nessuna delle operazioni viene applicata nel database.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) di questo articolo in GitHub.

## <a name="default-transaction-behavior"></a>Comportamento delle transazioni predefinito

Per impostazione predefinita, se il provider di database supporta le transazioni, tutte le modifiche in una singola chiamata a `SaveChanges` vengono applicate in una transazione. Se le modifiche hanno esito negativo, viene eseguito il rollback della transazione e nessuna delle modifiche viene applicata al database. Ciò significa che è garantito che l'operazione `SaveChanges` venga completata correttamente oppure che lasci il database non modificato se si verifica un errore.

Per la maggior parte delle applicazioni, questo comportamento predefinito è sufficiente. È consigliabile controllare le transazioni manualmente solo se i requisiti dell'applicazione lo rendono necessario.

## <a name="controlling-transactions"></a>Controllo delle transazioni

È possibile usare l'API `DbContext.Database` per avviare le transazioni, eseguirne il commit ed eseguirne il rollback. Nell'esempio seguente vengono illustrate due `SaveChanges` operazioni e una query LINQ eseguita in un'unica transazione:

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

Mentre tutti i provider di database relazionali supportano le transazioni, altri tipi di provider possono generare o no-op quando vengono chiamate le API di transazione.

## <a name="savepoints"></a>Salvataggio

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

Quando `SaveChanges` viene richiamato ed è già in corso una transazione nel contesto, EF crea automaticamente un *salvataggio* prima di salvare i dati. Salvataggio sono punti all'interno di una transazione di database di cui è possibile eseguire il rollback in un secondo momento, se si verifica un errore o per qualsiasi altro motivo. Se `SaveChanges` si verifica un errore, viene automaticamente eseguito il rollback della transazione a salvataggio, lasciando la transazione nello stesso stato di se non è mai stata avviata. Ciò consente di risolvere eventuali problemi e di ritentare il salvataggio, in particolare quando si verificano problemi di [concorrenza ottimistica](xref:core/saving/concurrency) .

> [!WARNING]
> Salvataggio sono incompatibili con i set di risultati attivi multipli di SQL Server e non vengono usati. Se si verifica un errore durante `SaveChanges` , è possibile che la transazione venga lasciata in uno stato sconosciuto.

È anche possibile gestire manualmente salvataggio, così come avviene con le transazioni. Nell'esempio seguente viene creato un salvataggio all'interno di una transazione e viene eseguito il rollback in caso di errore:

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a>Transazione tra più contesti

È anche possibile condividere una transazione tra più istanze di contesto. Questa funzionalità è disponibile solo quando si usa un provider di database relazionale, perché richiede l'uso di `DbTransaction` e `DbConnection`, specifici per i database relazionali.

Per condividere una transazione, è necessario che i contesti condividano sia `DbConnection` che `DbTransaction`.

### <a name="allow-connection-to-be-externally-provided"></a>Consentire connessioni dall'esterno

La condivisione di `DbConnection` richiede la possibilità di passare una connessione in un contesto durante la costruzione.

Il modo più semplice per consentire `DbConnection` dall'esterno consiste nell'interrompere l'uso del metodo `DbContext.OnConfiguring` per configurare il contesto e nel creare esternamente `DbContextOptions` e passare tale opzioni al costruttore del contesto.

> [!TIP]
> `DbContextOptionsBuilder` è l'API usata in `DbContext.OnConfiguring` per configurare il contesto. In questo caso viene usata esternamente per creare `DbContextOptions`.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

In alternativa è possibile continuare a usare `DbContext.OnConfiguring`, accettando però una `DbConnection` che viene salvata e quindi usata in `DbContext.OnConfiguring`.

```csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a>Condividere connessione e transazione

È ora possibile creare più istanze di contesto che condividono la stessa connessione. Usare quindi l'API `DbContext.Database.UseTransaction(DbTransaction)` per includere entrambi i contesti nella stessa transazione.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Uso di DbTransaction esterne (solo database relazionali)

Se si usano più tecnologie di accesso ai dati per accedere a un database relazionale, può essere utile condividere una transazione tra le operazioni eseguite da queste diverse tecnologie.

L'esempio seguente mostra come eseguire un'operazione ADO.NET SqlClient e un'operazione di Entity Framework Core nella stessa transazione.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a>Utilizzo di System.Transactions

È possibile usare le transazioni di ambiente, se è necessario coordinarle in un ambito più ampio.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

È anche supportato l'inserimento in una transazione esplicita.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a>Limitazioni di System.Transactions

1. EF Core si basa sui provider di database per implementare il supporto per System.Transactions. Se un provider non implementa il supporto per System.Transactions, è possibile che le chiamate a queste API vengano ignorate completamente. SqlClient lo supporta.

   > [!IMPORTANT]
   > È consigliabile verificare che il comportamento dell'API con il provider sia corretto prima di basarsi su di essa per la gestione delle transazioni. In caso contrario, è consigliabile contattare il gestore del provider del database.

2. A partire da .NET Core 2,1, l'implementazione System. Transactions non include il supporto per le transazioni distribuite, pertanto non è possibile usare `TransactionScope` o `CommittableTransaction` per coordinare le transazioni tra più gestori di risorse.
