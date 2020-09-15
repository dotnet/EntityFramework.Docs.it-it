---
title: Transazioni - EF Core
description: Gestione delle transazioni per l'atomicità quando si salvano i dati con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/transactions
ms.openlocfilehash: d824db6a4d6e1fc0fc385f007ccfc2c6cbbcae79
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070835"
---
# <a name="using-transactions"></a>Utilizzo di transazioni

Le transazioni consentono di elaborare varie operazioni di database in modo atomico. Se viene eseguito il commit della transazione, tutte le operazioni vengono applicate correttamente nel database. Se viene eseguito il rollback della transazione, nessuna delle operazioni viene applicata nel database.

> [!TIP]  
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) di questo articolo in GitHub.

## <a name="default-transaction-behavior"></a>Comportamento delle transazioni predefinito

Per impostazione predefinita, se il provider di database supporta le transazioni, tutte le modifiche in una singola chiamata a `SaveChanges()` vengono applicate in una transazione. Se le modifiche hanno esito negativo, viene eseguito il rollback della transazione e nessuna delle modifiche viene applicata al database. Ciò significa che è garantito che l'operazione `SaveChanges()` venga completata correttamente oppure che lasci il database non modificato se si verifica un errore.

Per la maggior parte delle applicazioni, questo comportamento predefinito è sufficiente. È consigliabile controllare le transazioni manualmente solo se i requisiti dell'applicazione lo rendono necessario.

## <a name="controlling-transactions"></a>Controllo delle transazioni

È possibile usare l'API `DbContext.Database` per avviare le transazioni, eseguirne il commit ed eseguirne il rollback. L'esempio seguente mostra due operazioni `SaveChanges()` e una query LINQ in esecuzione in una singola transazione.

Non tutti i provider di database supportano le transazioni. Alcuni provider possono generare eccezioni o non eseguire alcuna operazione quando vengono chiamate API per le transazioni.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a>Transazione tra contesti diversi (solo database relazionali)

È anche possibile condividere una transazione tra più istanze di contesto. Questa funzionalità è disponibile solo quando si usa un provider di database relazionale, perché richiede l'uso di `DbTransaction` e `DbConnection`, specifici per i database relazionali.

Per condividere una transazione, è necessario che i contesti condividano sia `DbConnection` che `DbTransaction`.

### <a name="allow-connection-to-be-externally-provided"></a>Consentire connessioni dall'esterno

La condivisione di `DbConnection` richiede la possibilità di passare una connessione in un contesto durante la costruzione.

Il modo più semplice per consentire `DbConnection` dall'esterno consiste nell'interrompere l'uso del metodo `DbContext.OnConfiguring` per configurare il contesto e nel creare esternamente `DbContextOptions` e passare tale opzioni al costruttore del contesto.

> [!TIP]  
> `DbContextOptionsBuilder` è l'API usata in `DbContext.OnConfiguring` per configurare il contesto. In questo caso viene usata esternamente per creare `DbContextOptions`.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

In alternativa è possibile continuare a usare `DbContext.OnConfiguring`, accettando però una `DbConnection` che viene salvata e quindi usata in `DbContext.OnConfiguring`.

``` csharp
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

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Uso di DbTransaction esterne (solo database relazionali)

Se si usano più tecnologie di accesso ai dati per accedere a un database relazionale, può essere utile condividere una transazione tra le operazioni eseguite da queste diverse tecnologie.

L'esempio seguente mostra come eseguire un'operazione ADO.NET SqlClient e un'operazione di Entity Framework Core nella stessa transazione.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>Utilizzo di System.Transactions

> [!NOTE]  
> Questa funzionalità è stata introdotta in EF Core 2.1.

È possibile usare le transazioni di ambiente, se è necessario coordinarle in un ambito più ampio.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

È anche supportato l'inserimento in una transazione esplicita.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a>Limitazioni di System.Transactions  

1. EF Core si basa sui provider di database per implementare il supporto per System.Transactions. Anche se il supporto è piuttosto comune tra i provider ADO.NET per .NET Framework, l'API è stata aggiunta solo di recente a .NET Core e il supporto di conseguenza non è ancora molto diffuso. Se un provider non implementa il supporto per System.Transactions, è possibile che le chiamate a queste API vengano ignorate completamente. SqlClient per .NET Core offre questo supporto dalla versione 2.1 in poi. SqlClient per .NET Core 2.0 genererà un'eccezione se si tenta di usare la funzionalità.

   > [!IMPORTANT]  
   > È consigliabile verificare che il comportamento dell'API con il provider sia corretto prima di basarsi su di essa per la gestione delle transazioni. In caso contrario, è consigliabile contattare il gestore del provider del database.

2. A partire dalla versione 2.1, l'implementazione di System.Transactions in .NET Core non include il supporto per le transazioni distribuite, quindi non è possibile usare `TransactionScope` o `CommittableTransaction` per coordinare le transazioni tra più gestori di risorse.
