---
title: Novità di EF Core 2.1 - EF Core
description: Modifiche e miglioramenti in Entity Framework Core 2,1
author: divega
ms.date: 02/20/2018
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: b3d44fe344155df1d814e189b533010673754089
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072317"
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="c63cc-103">Nuove funzionalità di EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="c63cc-103">New features in EF Core 2.1</span></span>

<span data-ttu-id="c63cc-104">Oltre a numerose correzioni di bug e piccoli miglioramenti funzionali e delle prestazioni, EF Core 2.1 include alcune nuove funzionalità interessanti:</span><span class="sxs-lookup"><span data-stu-id="c63cc-104">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="c63cc-105">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="c63cc-105">Lazy loading</span></span>

<span data-ttu-id="c63cc-106">EF Core contiene ora i blocchi predefiniti necessari per consentire a tutti di creare classi di entità che possono caricare le proprietà di navigazione su richiesta.</span><span class="sxs-lookup"><span data-stu-id="c63cc-106">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="c63cc-107">È stato anche creato un nuovo pacchetto, Microsoft.EntityFrameworkCore.Proxies, che sfrutta questi blocchi predefiniti per produrre classi di proxy con caricamento lazy in base a classi di entità con modifiche minime, ad esempio classi con le proprietà di navigazione virtuale.</span><span class="sxs-lookup"><span data-stu-id="c63cc-107">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (for example, classes with virtual navigation properties).</span></span>

<span data-ttu-id="c63cc-108">Leggere la [sezione sul caricamento lazy](xref:core/querying/related-data#lazy-loading) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-108">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="c63cc-109">Parametri nei costruttori di entità</span><span class="sxs-lookup"><span data-stu-id="c63cc-109">Parameters in entity constructors</span></span>

<span data-ttu-id="c63cc-110">In quanto uno dei blocchi predefiniti richiesti per il caricamento lazy, è stata abilitata la creazione di entità che accettano parametri nei propri costruttori.</span><span class="sxs-lookup"><span data-stu-id="c63cc-110">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="c63cc-111">È possibile usare i parametri per inserire i valori delle proprietà, i delegati del caricamento lazy e i servizi.</span><span class="sxs-lookup"><span data-stu-id="c63cc-111">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="c63cc-112">Leggere la [sezione sul costruttore di entità con parametri](xref:core/modeling/constructors) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-112">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="c63cc-113">Conversioni di valori</span><span class="sxs-lookup"><span data-stu-id="c63cc-113">Value conversions</span></span>

<span data-ttu-id="c63cc-114">Fino ad ora, EF Core poteva solo eseguire il mapping di proprietà con tipi supportati in modo nativo dal provider di database sottostante.</span><span class="sxs-lookup"><span data-stu-id="c63cc-114">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="c63cc-115">I valori venivano copiati tra le colonne e le proprietà senza alcuna trasformazione.</span><span class="sxs-lookup"><span data-stu-id="c63cc-115">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="c63cc-116">A partire da EF Core 2.1, si possono applicare conversioni dei valori per trasformare i valori ottenuti dalle colonne prima che vengano applicate alle proprietà e viceversa.</span><span class="sxs-lookup"><span data-stu-id="c63cc-116">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="c63cc-117">Sono disponibili un certo numero di conversioni che possono essere applicate per convenzione in base alle esigenze, nonché un'API di configurazione esplicita che consente la registrazione di conversioni personalizzate tra colonne e le proprietà.</span><span class="sxs-lookup"><span data-stu-id="c63cc-117">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="c63cc-118">Alcune applicazioni di questa funzionalità sono le seguenti:</span><span class="sxs-lookup"><span data-stu-id="c63cc-118">Some of the application of this feature are:</span></span>

- <span data-ttu-id="c63cc-119">Archiviazione delle enumerazioni come stringhe</span><span class="sxs-lookup"><span data-stu-id="c63cc-119">Storing enums as strings</span></span>
- <span data-ttu-id="c63cc-120">Mapping di integer senza segno con SQL Server</span><span class="sxs-lookup"><span data-stu-id="c63cc-120">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="c63cc-121">Crittografia e decrittografia automatiche dei valori delle proprietà</span><span class="sxs-lookup"><span data-stu-id="c63cc-121">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="c63cc-122">Leggere la [sezione sulle conversioni dei valori](xref:core/modeling/value-conversions) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-122">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="c63cc-123">Conversione di GroupBy LINQ</span><span class="sxs-lookup"><span data-stu-id="c63cc-123">LINQ GroupBy translation</span></span>

<span data-ttu-id="c63cc-124">Prima della versione 2.1, in EF Core l'operatore LINQ GroupBy veniva sempre valutato in memoria.</span><span class="sxs-lookup"><span data-stu-id="c63cc-124">Before version 2.1, in EF Core the GroupBy LINQ operator would always be evaluated in memory.</span></span> <span data-ttu-id="c63cc-125">È ora supportata la conversione nella clausola SQL GROUP BY nella maggior parte dei casi.</span><span class="sxs-lookup"><span data-stu-id="c63cc-125">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="c63cc-126">Questo esempio mostra una query con GroupBy usata per calcolare varie funzioni di aggregazione:</span><span class="sxs-lookup"><span data-stu-id="c63cc-126">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

``` csharp
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

<span data-ttu-id="c63cc-127">La conversione SQL corrispondente è simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="c63cc-127">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="c63cc-128">Seeding dei dati</span><span class="sxs-lookup"><span data-stu-id="c63cc-128">Data Seeding</span></span>

<span data-ttu-id="c63cc-129">Con la nuova versione sarà possibile fornire i dati iniziali per popolare un database.</span><span class="sxs-lookup"><span data-stu-id="c63cc-129">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="c63cc-130">Diversamente da EF6, il seeding dei dati è associato a un tipo di entità come parte della configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="c63cc-130">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="c63cc-131">Le migrazioni di EF Core possono calcolare automaticamente quali operazioni di inserimento, aggiornamento o eliminazione devono essere applicate durante l'aggiornamento del database a una nuova versione del modello.</span><span class="sxs-lookup"><span data-stu-id="c63cc-131">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="c63cc-132">Ad esempio, è possibile usare questo codice per configurare i dati iniziali per un'operazione Post in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="c63cc-132">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="c63cc-133">Leggere la [sezione sul seeding dei dati](xref:core/modeling/data-seeding) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-133">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="c63cc-134">Tipi di query</span><span class="sxs-lookup"><span data-stu-id="c63cc-134">Query types</span></span>

<span data-ttu-id="c63cc-135">Un modello EF Core può ora includere tipi di query.</span><span class="sxs-lookup"><span data-stu-id="c63cc-135">An EF Core model can now include query types.</span></span> <span data-ttu-id="c63cc-136">Diversamente dai tipi di entità, per i tipi di query non vengono definite chiavi e questi tipi non possono essere inseriti, eliminati o aggiornati (ovvero sono di sola lettura), ma possono essere restituiti direttamente dalle query.</span><span class="sxs-lookup"><span data-stu-id="c63cc-136">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (that is, they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="c63cc-137">Alcuni degli scenari di utilizzo per i tipi di query sono:</span><span class="sxs-lookup"><span data-stu-id="c63cc-137">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="c63cc-138">Mapping a viste senza chiavi primarie</span><span class="sxs-lookup"><span data-stu-id="c63cc-138">Mapping to views without primary keys</span></span>
- <span data-ttu-id="c63cc-139">Mapping a tabelle senza chiavi primarie</span><span class="sxs-lookup"><span data-stu-id="c63cc-139">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="c63cc-140">Mapping a query definite nel modello</span><span class="sxs-lookup"><span data-stu-id="c63cc-140">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="c63cc-141">Utilizzo come tipo restituito per le query `FromSql()`</span><span class="sxs-lookup"><span data-stu-id="c63cc-141">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="c63cc-142">Leggere la [sezione sui tipi di query](xref:core/modeling/keyless-entity-types) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-142">Read the [section on query types](xref:core/modeling/keyless-entity-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="c63cc-143">Include per i tipi derivati</span><span class="sxs-lookup"><span data-stu-id="c63cc-143">Include for derived types</span></span>

<span data-ttu-id="c63cc-144">Sarà ora possibile specificare le proprietà di navigazione definite solo su tipi derivati durante la scrittura di espressioni per il metodo `Include`.</span><span class="sxs-lookup"><span data-stu-id="c63cc-144">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="c63cc-145">Per la versione fortemente tipizzata di `Include`, è supportato l'uso di un cast esplicito o dell'operatore `as`.</span><span class="sxs-lookup"><span data-stu-id="c63cc-145">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="c63cc-146">Sono ora supportati anche i riferimenti ai nomi di proprietà di navigazione definite su tipi derivati nella versione stringa di `Include`:</span><span class="sxs-lookup"><span data-stu-id="c63cc-146">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="c63cc-147">Leggere la [sezione su Include con tipi derivati](xref:core/querying/related-data#include-on-derived-types) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-147">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="c63cc-148">Supporto di System.Transactions</span><span class="sxs-lookup"><span data-stu-id="c63cc-148">System.Transactions support</span></span>

<span data-ttu-id="c63cc-149">È stata aggiunta la possibilità di utilizzare le funzionalità di System.Transactions, ad esempio TransactionScope.</span><span class="sxs-lookup"><span data-stu-id="c63cc-149">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="c63cc-150">Questa funzionalità sarà disponibile sia per .NET Framework che per .NET Core quando si usano provider di database che la supportano.</span><span class="sxs-lookup"><span data-stu-id="c63cc-150">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="c63cc-151">Leggere la [sezione su System.Transactions](xref:core/saving/transactions#using-systemtransactions) per altre informazioni su questo argomento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-151">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="c63cc-152">Migliore ordinamento delle colonne nella migrazione iniziale</span><span class="sxs-lookup"><span data-stu-id="c63cc-152">Better column ordering in initial migration</span></span>

<span data-ttu-id="c63cc-153">In base ai suggerimenti dei clienti, sono state aggiornate le migrazioni per generare inizialmente le colonne per le tabelle nello stesso ordine con cui sono dichiarate le proprietà nelle classi.</span><span class="sxs-lookup"><span data-stu-id="c63cc-153">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="c63cc-154">Si noti che EF Core non può modificare l'ordine quando vengono aggiunti nuovi membri dopo la creazione iniziale delle tabelle.</span><span class="sxs-lookup"><span data-stu-id="c63cc-154">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="c63cc-155">Ottimizzazione di sottoquery correlate</span><span class="sxs-lookup"><span data-stu-id="c63cc-155">Optimization of correlated subqueries</span></span>

<span data-ttu-id="c63cc-156">È stata migliorata la conversione di query per evitare l'esecuzione di "N + 1" query SQL in molti scenari comuni in cui l'utilizzo di una proprietà di navigazione nella proiezione comporta il join dei dati dalla query radice con i dati da una sottoquery correlata.</span><span class="sxs-lookup"><span data-stu-id="c63cc-156">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="c63cc-157">L'ottimizzazione richiede la memorizzazione nel buffer dei risultati della sottoquery ed è necessario modificare la query per acconsentire esplicitamente al nuovo comportamento.</span><span class="sxs-lookup"><span data-stu-id="c63cc-157">The optimization requires buffering the results from the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="c63cc-158">Ad esempio, la query seguente viene in genere convertita in un'unica query per Customers, oltre a N (dove "N" è il numero di clienti restituito) query separate per Orders:</span><span class="sxs-lookup"><span data-stu-id="c63cc-158">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="c63cc-159">Includendo `ToList()` nella posizione corretta, si indica che la memorizzazione nel buffer è appropriata per Orders e viene quindi abilitata l'ottimizzazione:</span><span class="sxs-lookup"><span data-stu-id="c63cc-159">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="c63cc-160">Si noti che questa query verrà convertita in solo due query SQL: una per Customers e la successiva per Orders.</span><span class="sxs-lookup"><span data-stu-id="c63cc-160">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="owned-attribute"></a><span data-ttu-id="c63cc-161">Attributo [Owned]</span><span class="sxs-lookup"><span data-stu-id="c63cc-161">[Owned] attribute</span></span>

<span data-ttu-id="c63cc-162">È ora possibile configurare [tipi di entità di proprietà](xref:core/modeling/owned-entities) aggiungendo semplicemente l'annotazione `[Owned]` al tipo e assicurandosi quindi che l'entità proprietario venga aggiunta al modello:</span><span class="sxs-lookup"><span data-stu-id="c63cc-162">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

``` csharp
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

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a><span data-ttu-id="c63cc-163">Strumento da riga di comando dotnet-ef incluso in .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="c63cc-163">Command-line tool dotnet-ef included in .NET Core SDK</span></span>

<span data-ttu-id="c63cc-164">I comandi _dotnet-ef_ sono ora inclusi in .NET Core SDK, pertanto non sarà più necessario usare DotNetCliToolReference nel progetto per essere in grado di usare le migrazioni o eseguire lo scaffolding di un elemento DbContext da un database esistente.</span><span class="sxs-lookup"><span data-stu-id="c63cc-164">The _dotnet-ef_ commands are now part of the .NET Core SDK, therefore it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

<span data-ttu-id="c63cc-165">Vedere la sezione sull'[installazione degli strumenti](xref:core/miscellaneous/cli/dotnet#installing-the-tools) per maggiori dettagli su come abilitare gli strumenti da riga di comando per versioni diverse di .NET Core SDK ed EF Core.</span><span class="sxs-lookup"><span data-stu-id="c63cc-165">See the section on [installing the tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools) for more details on how to enable command line tools for different versions of the .NET Core SDK and EF Core.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="c63cc-166">Pacchetto Microsoft.EntityFrameworkCore.Abstractions</span><span class="sxs-lookup"><span data-stu-id="c63cc-166">Microsoft.EntityFrameworkCore.Abstractions package</span></span>

<span data-ttu-id="c63cc-167">Il nuovo pacchetto contiene gli attributi e le interfacce che è possibile usare nei progetti per attivare le funzionalità di EF Core senza creare una dipendenza da EF Core nel suo complesso.</span><span class="sxs-lookup"><span data-stu-id="c63cc-167">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="c63cc-168">Ad esempio, l'attributo [Owned] e l'interfaccia ILazyLoader si trovano qui.</span><span class="sxs-lookup"><span data-stu-id="c63cc-168">For example, the [Owned] attribute and the ILazyLoader interface are located here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="c63cc-169">Eventi di modifica dello stato</span><span class="sxs-lookup"><span data-stu-id="c63cc-169">State change events</span></span>

<span data-ttu-id="c63cc-170">È possibile usare i nuovi eventi `Tracked` e `StateChanged` su `ChangeTracker` per scrivere logica che reagisce alle entità immettendo il DbContext o modificando il relativo stato.</span><span class="sxs-lookup"><span data-stu-id="c63cc-170">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="c63cc-171">Analizzatore di parametri SQL non elaborati</span><span class="sxs-lookup"><span data-stu-id="c63cc-171">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="c63cc-172">EF Core include un nuovo analizzatore del codice che rileva gli utilizzi potenzialmente non sicuri delle API per operazioni SQL non elaborate, ad esempio `FromSql` o `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="c63cc-172">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="c63cc-173">Ad esempio, per la query seguente verrà visualizzato un avviso perché _minAge_ non è con parametri:</span><span class="sxs-lookup"><span data-stu-id="c63cc-173">For example, for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="c63cc-174">Compatibilità dei provider di database</span><span class="sxs-lookup"><span data-stu-id="c63cc-174">Database provider compatibility</span></span>

<span data-ttu-id="c63cc-175">È consigliabile usare EF Core 2.1 con provider che siano stati aggiornati o almeno testati per essere usati con EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="c63cc-175">It is recommended that you use EF Core 2.1 with providers that have been updated or at least tested to work with EF Core 2.1.</span></span>

> [!TIP]
> <span data-ttu-id="c63cc-176">Se si rilevano incompatibilità impreviste o qualsiasi problema per le nuove funzionalità o se si desidera inviare commenti o suggerimenti, usare lo [strumento per la registrazione dei problemi](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span><span class="sxs-lookup"><span data-stu-id="c63cc-176">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
