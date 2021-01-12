---
title: Query SQL non elaborate - EF Core
description: Utilizzo di SQL non elaborato per le query in Entity Framework Core
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 56724f9fddc9126fd4dfe0f348a0c525b43a3478
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128381"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="08403-103">Query SQL non elaborate</span><span class="sxs-lookup"><span data-stu-id="08403-103">Raw SQL Queries</span></span>

<span data-ttu-id="08403-104">Entity Framework Core consente di ricorrere a query SQL non elaborate quando si lavora con un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="08403-104">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="08403-105">Le query SQL non elaborate sono utili se la query desiderata non può essere espressa mediante LINQ.</span><span class="sxs-lookup"><span data-stu-id="08403-105">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="08403-106">Le query SQL non elaborate vengono usate anche se l'uso di una query LINQ produce una query SQL inefficiente.</span><span class="sxs-lookup"><span data-stu-id="08403-106">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="08403-107">Le query SQL non elaborate possono restituire tipi di entità regolari o [tipi di entità autochiave](xref:core/modeling/keyless-entity-types) che fanno parte del modello.</span><span class="sxs-lookup"><span data-stu-id="08403-107">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]
> <span data-ttu-id="08403-108">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="08403-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="08403-109">Query SQL non elaborate di base</span><span class="sxs-lookup"><span data-stu-id="08403-109">Basic raw SQL queries</span></span>

<span data-ttu-id="08403-110">È possibile utilizzare il `FromSqlRaw` metodo di estensione per iniziare una query LINQ basata su una query SQL non elaborata.</span><span class="sxs-lookup"><span data-stu-id="08403-110">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="08403-111">`FromSqlRaw` può essere usato solo nelle radici di query, direttamente in `DbSet<>` .</span><span class="sxs-lookup"><span data-stu-id="08403-111">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRaw)]

<span data-ttu-id="08403-112">Per eseguire una stored procedure, è possibile usare query SQL non elaborate.</span><span class="sxs-lookup"><span data-stu-id="08403-112">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="08403-113">Passaggio di parametri</span><span class="sxs-lookup"><span data-stu-id="08403-113">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="08403-114">**Usa sempre la parametrizzazione per query SQL non elaborate**</span><span class="sxs-lookup"><span data-stu-id="08403-114">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="08403-115">Quando si introducono valori forniti dall'utente in una query SQL non elaborata, è necessario prestare attenzione per evitare attacchi intrusivi nel codice SQL.</span><span class="sxs-lookup"><span data-stu-id="08403-115">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="08403-116">Oltre a convalidare che tali valori non contengono caratteri non validi, utilizzare sempre la parametrizzazione che invia i valori separati dal testo SQL.</span><span class="sxs-lookup"><span data-stu-id="08403-116">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="08403-117">In particolare, non passare mai una stringa concatenata o interpolata ( `$""` ) con valori specificati dall'utente non convalidati in `FromSqlRaw` o `ExecuteSqlRaw` .</span><span class="sxs-lookup"><span data-stu-id="08403-117">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="08403-118">I `FromSqlInterpolated` `ExecuteSqlInterpolated` metodi e consentono di utilizzare la sintassi di interpolazione delle stringhe in modo da proteggere gli attacchi intrusivi nel codice SQL.</span><span class="sxs-lookup"><span data-stu-id="08403-118">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="08403-119">Nell'esempio seguente viene passato un singolo parametro a un stored procedure includendo un segnaposto di parametro nella stringa di query SQL e fornendo un argomento aggiuntivo.</span><span class="sxs-lookup"><span data-stu-id="08403-119">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="08403-120">Sebbene questa sintassi sia simile alla `String.Format` sintassi, il valore fornito viene racchiuso in un oggetto `DbParameter` e il nome del parametro generato viene inserito nel punto in cui `{0}` è stato specificato il segnaposto.</span><span class="sxs-lookup"><span data-stu-id="08403-120">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="08403-121">`FromSqlInterpolated` è simile a `FromSqlRaw` , ma consente di usare la sintassi di interpolazione delle stringhe.</span><span class="sxs-lookup"><span data-stu-id="08403-121">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="08403-122">Analogamente `FromSqlRaw` `FromSqlInterpolated` a, può essere utilizzato solo nelle radici di query.</span><span class="sxs-lookup"><span data-stu-id="08403-122">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="08403-123">Come nell'esempio precedente, il valore viene convertito in un oggetto `DbParameter` e non è vulnerabile a SQL injection.</span><span class="sxs-lookup"><span data-stu-id="08403-123">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="08403-124">Prima della versione 3,0 `FromSqlRaw` ed `FromSqlInterpolated` erano due overload denominati `FromSql` .</span><span class="sxs-lookup"><span data-stu-id="08403-124">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="08403-125">Per ulteriori informazioni, vedere la [sezione versioni precedenti](#previous-versions).</span><span class="sxs-lookup"><span data-stu-id="08403-125">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="08403-126">È anche possibile costruire un DbParameter e fornirlo come valore del parametro.</span><span class="sxs-lookup"><span data-stu-id="08403-126">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="08403-127">Poiché viene usato un segnaposto di parametro SQL normale, anziché un segnaposto di stringa, `FromSqlRaw` può essere usato in modo sicuro:</span><span class="sxs-lookup"><span data-stu-id="08403-127">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="08403-128">`FromSqlRaw` consente di utilizzare parametri denominati nella stringa di query SQL, utile quando un stored procedure dispone di parametri facoltativi:</span><span class="sxs-lookup"><span data-stu-id="08403-128">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> <span data-ttu-id="08403-129">**Ordinamento parametri** Entity Framework Core passa i parametri in base all'ordine della `SqlParameter[]` matrice.</span><span class="sxs-lookup"><span data-stu-id="08403-129">**Parameter Ordering** Entity Framework Core passes parameters based on the order of the `SqlParameter[]` array.</span></span> <span data-ttu-id="08403-130">Quando si passano più oggetti `SqlParameter` , l'ordinamento nella stringa SQL deve corrispondere all'ordine dei parametri nella definizione del stored procedure.</span><span class="sxs-lookup"><span data-stu-id="08403-130">When passing multiple `SqlParameter`s, the ordering in the SQL string must match the order of the parameters in the stored procedure's definition.</span></span> <span data-ttu-id="08403-131">In caso contrario, è possibile che vengano generate eccezioni di conversione del tipo e/o un comportamento imprevisto quando viene eseguita la procedura.</span><span class="sxs-lookup"><span data-stu-id="08403-131">Failure to do this may result in type conversion exceptions and/or unexpected behavior when the procedure is executed.</span></span>

## <a name="composing-with-linq"></a><span data-ttu-id="08403-132">Composizione con LINQ</span><span class="sxs-lookup"><span data-stu-id="08403-132">Composing with LINQ</span></span>

<span data-ttu-id="08403-133">È possibile comporre la query SQL RAW iniziale usando gli operatori LINQ.</span><span class="sxs-lookup"><span data-stu-id="08403-133">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="08403-134">EF Core lo considererà come sottoquery e compose su di esso nel database.</span><span class="sxs-lookup"><span data-stu-id="08403-134">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="08403-135">Nell'esempio seguente viene utilizzata una query SQL non elaborata che seleziona da una funzione Table-Valued (TVF).</span><span class="sxs-lookup"><span data-stu-id="08403-135">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="08403-136">E quindi lo compone usando LINQ per eseguire operazioni di filtro e ordinamento.</span><span class="sxs-lookup"><span data-stu-id="08403-136">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="08403-137">La query precedente genera SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="08403-137">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="08403-138">Inclusione di dati correlati</span><span class="sxs-lookup"><span data-stu-id="08403-138">Including related data</span></span>

<span data-ttu-id="08403-139">Il metodo `Include` può essere usato per includere dati correlati, come in qualsiasi altra query LINQ:</span><span class="sxs-lookup"><span data-stu-id="08403-139">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="08403-140">Per la composizione con LINQ è necessario che la query SQL non elaborata sia componibile perché EF Core considererà il SQL fornito come una sottoquery.</span><span class="sxs-lookup"><span data-stu-id="08403-140">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="08403-141">Le query SQL componibili iniziano con la parola chiave `SELECT`.</span><span class="sxs-lookup"><span data-stu-id="08403-141">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="08403-142">Inoltre, SQL passato non deve contenere caratteri o opzioni non valide per una sottoquery, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="08403-142">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="08403-143">Un punto e virgola finale</span><span class="sxs-lookup"><span data-stu-id="08403-143">A trailing semicolon</span></span>
- <span data-ttu-id="08403-144">In SQL Server, un hint a livello di query finale, ad esempio `OPTION (HASH JOIN)`</span><span class="sxs-lookup"><span data-stu-id="08403-144">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="08403-145">In SQL Server, `ORDER BY` clausola non utilizzata con `OFFSET 0` o `TOP 100 PERCENT` nella `SELECT` clausola</span><span class="sxs-lookup"><span data-stu-id="08403-145">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="08403-146">SQL Server non consente la composizione su chiamate stored procedure, quindi qualsiasi tentativo di applicare operatori di query aggiuntivi a tale chiamata genererà SQL non valido.</span><span class="sxs-lookup"><span data-stu-id="08403-146">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="08403-147">Usare `AsEnumerable` `AsAsyncEnumerable` il metodo o subito dopo i `FromSqlRaw` `FromSqlInterpolated` metodi o per assicurarsi che EF Core non tenti di comporre su una stored procedure.</span><span class="sxs-lookup"><span data-stu-id="08403-147">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="08403-148">Rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="08403-148">Change Tracking</span></span>

<span data-ttu-id="08403-149">Le query che utilizzano `FromSqlRaw` i `FromSqlInterpolated` metodi o seguono esattamente le stesse regole di rilevamento delle modifiche di qualsiasi altra query LINQ in EF core.</span><span class="sxs-lookup"><span data-stu-id="08403-149">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="08403-150">Se ad esempio la query proietta tipi di entità, i risultati vengono rilevati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="08403-150">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="08403-151">Nell'esempio seguente viene utilizzata una query SQL non elaborata che seleziona da una funzione Table-Valued (TVF), quindi Disabilita il rilevamento delle modifiche con la chiamata a `AsNoTracking` :</span><span class="sxs-lookup"><span data-stu-id="08403-151">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="08403-152">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="08403-152">Limitations</span></span>

<span data-ttu-id="08403-153">Esistono alcune limitazioni da tenere presenti quando si usano query SQL non elaborate:</span><span class="sxs-lookup"><span data-stu-id="08403-153">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="08403-154">La query SQL deve restituire i dati per tutte le proprietà del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="08403-154">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="08403-155">I nomi delle colonne nel set di risultati devono corrispondere ai nomi di colonna a cui sono mappate le proprietà.</span><span class="sxs-lookup"><span data-stu-id="08403-155">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="08403-156">Si noti che questo comportamento è diverso da EF6.</span><span class="sxs-lookup"><span data-stu-id="08403-156">Note this behavior is different from EF6.</span></span> <span data-ttu-id="08403-157">EF6 ignorata la proprietà nel mapping delle colonne per le query SQL non elaborate e i nomi delle colonne del set di risultati devono corrispondere ai nomi delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="08403-157">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="08403-158">La query SQL non può contenere dati correlati.</span><span class="sxs-lookup"><span data-stu-id="08403-158">The SQL query can't contain related data.</span></span> <span data-ttu-id="08403-159">Tuttavia, in molti casi è possibile estendere la query usando l'operatore `Include` per restituire i dati correlati (vedere [Inclusione di dati correlati](#including-related-data)).</span><span class="sxs-lookup"><span data-stu-id="08403-159">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="08403-160">Versioni precedenti</span><span class="sxs-lookup"><span data-stu-id="08403-160">Previous versions</span></span>

<span data-ttu-id="08403-161">EF Core versione 2,2 e versioni precedenti hanno due overload del metodo denominato `FromSql` , che si comportavano nello stesso modo dei più recenti `FromSqlRaw` e `FromSqlInterpolated` .</span><span class="sxs-lookup"><span data-stu-id="08403-161">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="08403-162">È stato facile chiamare accidentalmente il metodo stringa non elaborata quando lo scopo era quello di chiamare il metodo della stringa interpolata e viceversa.</span><span class="sxs-lookup"><span data-stu-id="08403-162">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="08403-163">La chiamata accidentale di un overload errato potrebbe causare la mancata esecuzione di query con parametri.</span><span class="sxs-lookup"><span data-stu-id="08403-163">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
