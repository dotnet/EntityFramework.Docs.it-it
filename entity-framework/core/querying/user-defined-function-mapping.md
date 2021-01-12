---
title: Mapping di funzioni definite dall'utente-EF Core
description: Mapping di funzioni definite dall'utente a funzioni di database
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3e49ed9c49b38b98430128ffdc7ceef0b844b9df
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129122"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="0b1ed-103">Mapping delle funzioni definite dall'utente</span><span class="sxs-lookup"><span data-stu-id="0b1ed-103">User-defined function mapping</span></span>

<span data-ttu-id="0b1ed-104">EF Core consente l'utilizzo di funzioni SQL definite dall'utente nelle query.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="0b1ed-105">A tale scopo, è necessario eseguire il mapping delle funzioni a un metodo CLR durante la configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="0b1ed-106">Quando si traduce la query LINQ in SQL, viene chiamata la funzione definita dall'utente anziché la funzione CLR a cui è stato eseguito il mapping.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="0b1ed-107">Mapping di un metodo a una funzione SQL</span><span class="sxs-lookup"><span data-stu-id="0b1ed-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="0b1ed-108">Per illustrare il funzionamento del mapping delle funzioni definite dall'utente, definire le entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="0b1ed-109">E la configurazione del modello seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="0b1ed-110">Il Blog può avere molti post e ogni post può presentare molti commenti.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="0b1ed-111">Successivamente, creare la funzione definita dall'utente `CommentedPostCountForBlog` , che restituisce il numero di post con almeno un commento per un determinato Blog, in base al Blog `Id` :</span><span class="sxs-lookup"><span data-stu-id="0b1ed-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

<span data-ttu-id="0b1ed-112">Per usare questa funzione in EF Core, viene definito il metodo CLR seguente, mappato alla funzione definita dall'utente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="0b1ed-113">Il corpo del metodo CLR non è importante.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="0b1ed-114">Il metodo non verrà richiamato sul lato client, a meno che EF Core non possa tradurre gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="0b1ed-115">Se gli argomenti possono essere convertiti, EF Core interessa solo la firma del metodo.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1ed-116">Nell'esempio, il metodo è definito in `DbContext` , ma può anche essere definito come metodo statico all'interno di altre classi.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="0b1ed-117">Questa definizione di funzione può ora essere associata a una funzione definita dall'utente nella configurazione del modello:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="0b1ed-118">Per impostazione predefinita, EF Core tenta di eseguire il mapping della funzione CLR a una funzione definita dall'utente con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="0b1ed-119">Se i nomi sono diversi, è possibile usare `HasName` per specificare il nome corretto per la funzione definita dall'utente a cui si vuole eseguire il mapping.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="0b1ed-120">A questo punto, eseguire la query seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="0b1ed-121">Produrrà questo SQL:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="0b1ed-122">Mapping di un metodo a un oggetto SQL personalizzato</span><span class="sxs-lookup"><span data-stu-id="0b1ed-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="0b1ed-123">EF Core consente anche funzioni definite dall'utente che vengono convertite in un SQL specifico.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="0b1ed-124">L'espressione SQL viene fornita utilizzando il `HasTranslation` metodo durante la configurazione di funzioni definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="0b1ed-125">Nell'esempio seguente viene creata una funzione che calcola la differenza percentuale tra due numeri interi.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="0b1ed-126">Il metodo CLR è il seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="0b1ed-127">La definizione della funzione è la seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="0b1ed-128">Una volta definita, la funzione può essere usata nella query.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="0b1ed-129">Invece di chiamare la funzione di database, EF Core convertirà il corpo del metodo direttamente in SQL in base alla struttura ad albero dell'espressione SQL creata da HasTranslation.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="0b1ed-130">La query LINQ seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="0b1ed-131">Produce il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a><span data-ttu-id="0b1ed-132">Configurazione del supporto di valori null della funzione definita dall'utente in base ai relativi argomenti</span><span class="sxs-lookup"><span data-stu-id="0b1ed-132">Configuring nullability of user-defined function based on its arguments</span></span>

<span data-ttu-id="0b1ed-133">Se la funzione definita dall'utente può restituire solo `null` quando uno o più degli argomenti sono `null` , EFCore fornisce un metodo per specificarlo, ottenendo un SQL più efficiente.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-133">If the user-defined function can only return `null` when one or more of its arguments are `null`, EFCore provides way to specify that, resulting in more performant SQL.</span></span> <span data-ttu-id="0b1ed-134">Per eseguire questa operazione, è possibile aggiungere una `PropagatesNullability()` chiamata alla configurazione del modello dei parametri della funzione pertinente.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-134">It can be done by adding a `PropagatesNullability()` call to the relevant function parameters model configuration.</span></span>

<span data-ttu-id="0b1ed-135">Per illustrare questo problema, definire la funzione utente `ConcatStrings` :</span><span class="sxs-lookup"><span data-stu-id="0b1ed-135">To illustrate this, define user function `ConcatStrings`:</span></span>

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

<span data-ttu-id="0b1ed-136">e due metodi CLR mappati a esso:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-136">and two CLR methods that map to it:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

<span data-ttu-id="0b1ed-137">La configurazione del modello (all'interno del `OnModelCreating` metodo) è la seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-137">The model configuration (inside `OnModelCreating` method) is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

<span data-ttu-id="0b1ed-138">La prima funzione viene configurata in modo standard.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-138">The first function is configured in the standard way.</span></span> <span data-ttu-id="0b1ed-139">La seconda funzione viene configurata per sfruttare l'ottimizzazione della propagazione dei valori null, fornendo ulteriori informazioni sul comportamento della funzione intorno ai parametri null.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-139">The second function is configured to take advantage of the nullability propagation optimization, providing more information on how the function behaves around null parameters.</span></span>

<span data-ttu-id="0b1ed-140">Quando si inviano le query seguenti:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-140">When issuing the following queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

<span data-ttu-id="0b1ed-141">Si ottiene questo SQL:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-141">We get this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

<span data-ttu-id="0b1ed-142">La seconda query non deve rivalutare la funzione stessa per testarne il supporto di valori null.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-142">The second query doesn't need to re-evaluate the function itself to test its nullability.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1ed-143">Questa ottimizzazione deve essere utilizzata solo se la funzione può restituire solo `null` quando i parametri sono `null` .</span><span class="sxs-lookup"><span data-stu-id="0b1ed-143">This optimization should only be used if the function can only return `null` when it's parameters are `null`.</span></span>

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="0b1ed-144">Mapping di una funzione Queryable a una funzione con valori di tabella</span><span class="sxs-lookup"><span data-stu-id="0b1ed-144">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="0b1ed-145">EF Core supporta anche il mapping a una funzione con valori di tabella utilizzando un metodo CLR definito dall'utente che restituisce un oggetto `IQueryable` di tipi di entità, consentendo EF Core di eseguire il mapping di funzioni con valori con parametri.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-145">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="0b1ed-146">Il processo è simile al mapping di una funzione scalare definita dall'utente a una funzione SQL: è necessario un TVF nel database, una funzione CLR usata nelle query LINQ e un mapping tra i due.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-146">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="0b1ed-147">Si userà ad esempio una funzione con valori di tabella che restituisce tutti i post con almeno un commento che soddisfa una determinata soglia "like":</span><span class="sxs-lookup"><span data-stu-id="0b1ed-147">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

<span data-ttu-id="0b1ed-148">La firma del metodo CLR è la seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-148">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="0b1ed-149">La `FromExpression` chiamata nel corpo della funzione CLR consente di usare la funzione anziché un DbSet normale.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-149">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="0b1ed-150">Di seguito è riportato il mapping:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-150">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="0b1ed-151">Finché non viene corretto il [problema 23408](https://github.com/dotnet/efcore/issues/23408) , il mapping a un `IQueryable` di tipi di entità sostituisce il mapping predefinito a una tabella per DbSet.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-151">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="0b1ed-152">Se necessario, ad esempio quando l'entità non è il mapping senza chiave alla tabella deve essere specificato in modo esplicito tramite il `ToTable` metodo.</span><span class="sxs-lookup"><span data-stu-id="0b1ed-152">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="0b1ed-153">La funzione Queryable deve essere mappata a una funzione con valori di tabella e non può utilizzare `HasTranslation` .</span><span class="sxs-lookup"><span data-stu-id="0b1ed-153">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="0b1ed-154">Quando viene eseguito il mapping della funzione, la query seguente:</span><span class="sxs-lookup"><span data-stu-id="0b1ed-154">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="0b1ed-155">Produce</span><span class="sxs-lookup"><span data-stu-id="0b1ed-155">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
