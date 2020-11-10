---
title: Novità di EF Core 2.1 - EF Core
description: Modifiche e miglioramenti in Entity Framework Core 2,1
author: ajcvickers
ms.date: 02/20/2018
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: f235c310cbd336da65cd05d25e1338d87534eb2a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429338"
---
# <a name="new-features-in-ef-core-21"></a>Nuove funzionalità di EF Core 2.1

Oltre a numerose correzioni di bug e piccoli miglioramenti funzionali e delle prestazioni, EF Core 2.1 include alcune nuove funzionalità interessanti:

## <a name="lazy-loading"></a>Caricamento lazy

EF Core contiene ora i blocchi predefiniti necessari per consentire a tutti di creare classi di entità che possono caricare le proprietà di navigazione su richiesta. È stato anche creato un nuovo pacchetto, Microsoft.EntityFrameworkCore.Proxies, che sfrutta questi blocchi predefiniti per produrre classi di proxy con caricamento lazy in base a classi di entità con modifiche minime, ad esempio classi con le proprietà di navigazione virtuale.

Leggere la [sezione sul caricamento lazy](xref:core/querying/related-data#lazy-loading) per altre informazioni su questo argomento.

## <a name="parameters-in-entity-constructors"></a>Parametri nei costruttori di entità

In quanto uno dei blocchi predefiniti richiesti per il caricamento lazy, è stata abilitata la creazione di entità che accettano parametri nei propri costruttori. È possibile usare i parametri per inserire i valori delle proprietà, i delegati del caricamento lazy e i servizi.

Leggere la [sezione sul costruttore di entità con parametri](xref:core/modeling/constructors) per altre informazioni su questo argomento.

## <a name="value-conversions"></a>Conversioni di valori

Fino ad ora, EF Core poteva solo eseguire il mapping di proprietà con tipi supportati in modo nativo dal provider di database sottostante. I valori venivano copiati tra le colonne e le proprietà senza alcuna trasformazione. A partire da EF Core 2.1, si possono applicare conversioni dei valori per trasformare i valori ottenuti dalle colonne prima che vengano applicate alle proprietà e viceversa. Sono disponibili un certo numero di conversioni che possono essere applicate per convenzione in base alle esigenze, nonché un'API di configurazione esplicita che consente la registrazione di conversioni personalizzate tra colonne e le proprietà. Alcune applicazioni di questa funzionalità sono le seguenti:

- Archiviazione delle enumerazioni come stringhe
- Mapping di integer senza segno con SQL Server
- Crittografia e decrittografia automatiche dei valori delle proprietà

Leggere la [sezione sulle conversioni dei valori](xref:core/modeling/value-conversions) per altre informazioni su questo argomento.

## <a name="linq-groupby-translation"></a>Conversione di GroupBy LINQ

Prima della versione 2.1, in EF Core l'operatore LINQ GroupBy veniva sempre valutato in memoria. È ora supportata la conversione nella clausola SQL GROUP BY nella maggior parte dei casi.

Questo esempio mostra una query con GroupBy usata per calcolare varie funzioni di aggregazione:

```csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

La conversione SQL corrispondente è simile alla seguente:

```sql
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>Seeding dei dati

Con la nuova versione sarà possibile fornire i dati iniziali per popolare un database. Diversamente da EF6, il seeding dei dati è associato a un tipo di entità come parte della configurazione del modello. Le migrazioni di EF Core possono calcolare automaticamente quali operazioni di inserimento, aggiornamento o eliminazione devono essere applicate durante l'aggiornamento del database a una nuova versione del modello.

Ad esempio, è possibile usare questo codice per configurare i dati iniziali per un'operazione Post in `OnModelCreating`:

```csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

Leggere la [sezione sul seeding dei dati](xref:core/modeling/data-seeding) per altre informazioni su questo argomento.

## <a name="query-types"></a>Tipi di query

Un modello EF Core può ora includere tipi di query. Diversamente dai tipi di entità, per i tipi di query non vengono definite chiavi e questi tipi non possono essere inseriti, eliminati o aggiornati (ovvero sono di sola lettura), ma possono essere restituiti direttamente dalle query. Alcuni degli scenari di utilizzo per i tipi di query sono:

- Mapping a viste senza chiavi primarie
- Mapping a tabelle senza chiavi primarie
- Mapping a query definite nel modello
- Utilizzo come tipo restituito per le query `FromSql()`

Leggere la [sezione sui tipi di query](xref:core/modeling/keyless-entity-types) per altre informazioni su questo argomento.

## <a name="include-for-derived-types"></a>Include per i tipi derivati

Sarà ora possibile specificare le proprietà di navigazione definite solo su tipi derivati durante la scrittura di espressioni per il metodo `Include`. Per la versione fortemente tipizzata di `Include`, è supportato l'uso di un cast esplicito o dell'operatore `as`. Sono ora supportati anche i riferimenti ai nomi di proprietà di navigazione definite su tipi derivati nella versione stringa di `Include`:

```csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

Leggere la [sezione su Include con tipi derivati](xref:core/querying/related-data#include-on-derived-types) per altre informazioni su questo argomento.

## <a name="systemtransactions-support"></a>Supporto di System.Transactions

È stata aggiunta la possibilità di utilizzare le funzionalità di System.Transactions, ad esempio TransactionScope. Questa funzionalità sarà disponibile sia per .NET Framework che per .NET Core quando si usano provider di database che la supportano.

Leggere la [sezione su System.Transactions](xref:core/saving/transactions#using-systemtransactions) per altre informazioni su questo argomento.

## <a name="better-column-ordering-in-initial-migration"></a>Migliore ordinamento delle colonne nella migrazione iniziale

In base ai suggerimenti dei clienti, sono state aggiornate le migrazioni per generare inizialmente le colonne per le tabelle nello stesso ordine con cui sono dichiarate le proprietà nelle classi. Si noti che EF Core non può modificare l'ordine quando vengono aggiunti nuovi membri dopo la creazione iniziale delle tabelle.

## <a name="optimization-of-correlated-subqueries"></a>Ottimizzazione di sottoquery correlate

È stata migliorata la conversione di query per evitare l'esecuzione di "N + 1" query SQL in molti scenari comuni in cui l'utilizzo di una proprietà di navigazione nella proiezione comporta il join dei dati dalla query radice con i dati da una sottoquery correlata. L'ottimizzazione richiede la memorizzazione nel buffer dei risultati della sottoquery ed è necessario modificare la query per acconsentire esplicitamente al nuovo comportamento.

Ad esempio, la query seguente viene in genere convertita in un'unica query per Customers, oltre a N (dove "N" è il numero di clienti restituito) query separate per Orders:

```csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

Includendo `ToList()` nella posizione corretta, si indica che la memorizzazione nel buffer è appropriata per Orders e viene quindi abilitata l'ottimizzazione:

```csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

Si noti che questa query verrà convertita in solo due query SQL: una per Customers e la successiva per Orders.

## <a name="owned-attribute"></a>Attributo [Owned]

È ora possibile configurare [tipi di entità di proprietà](xref:core/modeling/owned-entities) aggiungendo semplicemente l'annotazione `[Owned]` al tipo e assicurandosi quindi che l'entità proprietario venga aggiunta al modello:

```csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a>Strumento da riga di comando dotnet-ef incluso in .NET Core SDK

I comandi _dotnet-ef_ sono ora inclusi in .NET Core SDK, pertanto non sarà più necessario usare DotNetCliToolReference nel progetto per essere in grado di usare le migrazioni o eseguire lo scaffolding di un elemento DbContext da un database esistente.

Vedere la sezione sull'[installazione degli strumenti](xref:core/cli/dotnet#installing-the-tools) per maggiori dettagli su come abilitare gli strumenti da riga di comando per versioni diverse di .NET Core SDK ed EF Core.

## <a name="microsoftentityframeworkcoreabstractions-package"></a>Pacchetto Microsoft.EntityFrameworkCore.Abstractions

Il nuovo pacchetto contiene gli attributi e le interfacce che è possibile usare nei progetti per attivare le funzionalità di EF Core senza creare una dipendenza da EF Core nel suo complesso. Ad esempio, l'attributo [Owned] e l'interfaccia ILazyLoader si trovano qui.

## <a name="state-change-events"></a>Eventi di modifica dello stato

È possibile usare i nuovi eventi `Tracked` e `StateChanged` su `ChangeTracker` per scrivere logica che reagisce alle entità immettendo il DbContext o modificando il relativo stato.

## <a name="raw-sql-parameter-analyzer"></a>Analizzatore di parametri SQL non elaborati

EF Core include un nuovo analizzatore del codice che rileva gli utilizzi potenzialmente non sicuri delle API per operazioni SQL non elaborate, ad esempio `FromSql` o `ExecuteSqlCommand`. Ad esempio, per la query seguente verrà visualizzato un avviso perché _minAge_ non è con parametri:

```csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a>Compatibilità dei provider di database

È consigliabile usare EF Core 2.1 con provider che siano stati aggiornati o almeno testati per essere usati con EF Core 2.1.

> [!TIP]
> Se si rilevano incompatibilità impreviste o qualsiasi problema per le nuove funzionalità o se si desidera inviare commenti o suggerimenti, usare lo [strumento per la registrazione dei problemi](https://github.com/dotnet/efcore/issues/new).
