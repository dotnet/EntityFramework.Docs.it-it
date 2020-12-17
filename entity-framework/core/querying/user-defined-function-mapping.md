---
title: Mapping di funzioni definite dall'utente-EF Core
description: Mapping di funzioni definite dall'utente a funzioni di database
author: maumar
ms.date: 11/23/2020
uid: core/user-defined-function-mapping
ms.openlocfilehash: ba60abdc9c81b34b8f4ed8f501cf2f7e52ba9d7d
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657852"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="93dc9-103">Mapping di funzioni definite dall'utente</span><span class="sxs-lookup"><span data-stu-id="93dc9-103">User-defined function mapping</span></span>

<span data-ttu-id="93dc9-104">EF Core consente l'utilizzo di funzioni SQL definite dall'utente nelle query.</span><span class="sxs-lookup"><span data-stu-id="93dc9-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="93dc9-105">A tale scopo, è necessario eseguire il mapping delle funzioni a un metodo CLR durante la configurazione del modello.</span><span class="sxs-lookup"><span data-stu-id="93dc9-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="93dc9-106">Quando si traduce la query LINQ in SQL, viene chiamata la funzione definita dall'utente anziché la funzione CLR a cui è stato eseguito il mapping.</span><span class="sxs-lookup"><span data-stu-id="93dc9-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="93dc9-107">Mapping di un metodo a una funzione SQL</span><span class="sxs-lookup"><span data-stu-id="93dc9-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="93dc9-108">Per illustrare il funzionamento del mapping delle funzioni definite dall'utente, definire le entità seguenti:</span><span class="sxs-lookup"><span data-stu-id="93dc9-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="93dc9-109">E la configurazione del modello seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="93dc9-110">Il Blog può avere molti post e ogni post può presentare molti commenti.</span><span class="sxs-lookup"><span data-stu-id="93dc9-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="93dc9-111">Successivamente, creare la funzione definita dall'utente `CommentedPostCountForBlog` , che restituisce il numero di post con almeno un commento per un determinato Blog, in base al Blog `Id` :</span><span class="sxs-lookup"><span data-stu-id="93dc9-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

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

<span data-ttu-id="93dc9-112">Per usare questa funzione in EF Core, viene definito il metodo CLR seguente, mappato alla funzione definita dall'utente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="93dc9-113">Il corpo del metodo CLR non è importante.</span><span class="sxs-lookup"><span data-stu-id="93dc9-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="93dc9-114">Il metodo non verrà richiamato sul lato client, a meno che EF Core non possa tradurre gli argomenti.</span><span class="sxs-lookup"><span data-stu-id="93dc9-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="93dc9-115">Se gli argomenti possono essere convertiti, EF Core interessa solo la firma del metodo.</span><span class="sxs-lookup"><span data-stu-id="93dc9-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="93dc9-116">Nell'esempio, il metodo è definito in `DbContext` , ma può anche essere definito come metodo statico all'interno di altre classi.</span><span class="sxs-lookup"><span data-stu-id="93dc9-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="93dc9-117">Questa definizione di funzione può ora essere associata a una funzione definita dall'utente nella configurazione del modello:</span><span class="sxs-lookup"><span data-stu-id="93dc9-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="93dc9-118">Per impostazione predefinita, EF Core tenta di eseguire il mapping della funzione CLR a una funzione definita dall'utente con lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="93dc9-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="93dc9-119">Se i nomi sono diversi, è possibile usare `HasName` per specificare il nome corretto per la funzione definita dall'utente a cui si vuole eseguire il mapping.</span><span class="sxs-lookup"><span data-stu-id="93dc9-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="93dc9-120">A questo punto, eseguire la query seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="93dc9-121">Produrrà questo SQL:</span><span class="sxs-lookup"><span data-stu-id="93dc9-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="93dc9-122">Mapping di un metodo a un oggetto SQL personalizzato</span><span class="sxs-lookup"><span data-stu-id="93dc9-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="93dc9-123">EF Core consente anche funzioni definite dall'utente che vengono convertite in un SQL specifico.</span><span class="sxs-lookup"><span data-stu-id="93dc9-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="93dc9-124">L'espressione SQL viene fornita utilizzando il `HasTranslation` metodo durante la configurazione di funzioni definite dall'utente.</span><span class="sxs-lookup"><span data-stu-id="93dc9-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="93dc9-125">Nell'esempio seguente viene creata una funzione che calcola la differenza percentuale tra due numeri interi.</span><span class="sxs-lookup"><span data-stu-id="93dc9-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="93dc9-126">Il metodo CLR è il seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="93dc9-127">La definizione della funzione è la seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="93dc9-128">Una volta definita, la funzione può essere usata nella query.</span><span class="sxs-lookup"><span data-stu-id="93dc9-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="93dc9-129">Invece di chiamare la funzione di database, EF Core convertirà il corpo del metodo direttamente in SQL in base alla struttura ad albero dell'espressione SQL creata da HasTranslation.</span><span class="sxs-lookup"><span data-stu-id="93dc9-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="93dc9-130">La query LINQ seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="93dc9-131">Produce il codice SQL seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="93dc9-132">Mapping di una funzione Queryable a una funzione con valori di tabella</span><span class="sxs-lookup"><span data-stu-id="93dc9-132">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="93dc9-133">EF Core supporta anche il mapping a una funzione con valori di tabella utilizzando un metodo CLR definito dall'utente che restituisce un oggetto `IQueryable` di tipi di entità, consentendo EF Core di eseguire il mapping di funzioni con valori con parametri.</span><span class="sxs-lookup"><span data-stu-id="93dc9-133">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="93dc9-134">Il processo è simile al mapping di una funzione scalare definita dall'utente a una funzione SQL: è necessario un TVF nel database, una funzione CLR usata nelle query LINQ e un mapping tra i due.</span><span class="sxs-lookup"><span data-stu-id="93dc9-134">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="93dc9-135">Si userà ad esempio una funzione con valori di tabella che restituisce tutti i post con almeno un commento che soddisfa una determinata soglia "like":</span><span class="sxs-lookup"><span data-stu-id="93dc9-135">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

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

<span data-ttu-id="93dc9-136">La firma del metodo CLR è la seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-136">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="93dc9-137">La `FromExpression` chiamata nel corpo della funzione CLR consente di usare la funzione anziché un DbSet normale.</span><span class="sxs-lookup"><span data-stu-id="93dc9-137">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="93dc9-138">Di seguito è riportato il mapping:</span><span class="sxs-lookup"><span data-stu-id="93dc9-138">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="93dc9-139">Finché non viene corretto il [problema 23408](https://github.com/dotnet/efcore/issues/23408) , il mapping a un `IQueryable` di tipi di entità sostituisce il mapping predefinito a una tabella per DbSet.</span><span class="sxs-lookup"><span data-stu-id="93dc9-139">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="93dc9-140">Se necessario, ad esempio quando l'entità non è il mapping senza chiave alla tabella deve essere specificato in modo esplicito tramite il `ToTable` metodo.</span><span class="sxs-lookup"><span data-stu-id="93dc9-140">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="93dc9-141">La funzione Queryable deve essere mappata a una funzione con valori di tabella e non può utilizzare `HasTranslation` .</span><span class="sxs-lookup"><span data-stu-id="93dc9-141">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="93dc9-142">Quando viene eseguito il mapping della funzione, la query seguente:</span><span class="sxs-lookup"><span data-stu-id="93dc9-142">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="93dc9-143">Produce</span><span class="sxs-lookup"><span data-stu-id="93dc9-143">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
