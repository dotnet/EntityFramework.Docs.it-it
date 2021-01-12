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
# <a name="user-defined-function-mapping"></a>Mapping delle funzioni definite dall'utente

EF Core consente l'utilizzo di funzioni SQL definite dall'utente nelle query. A tale scopo, è necessario eseguire il mapping delle funzioni a un metodo CLR durante la configurazione del modello. Quando si traduce la query LINQ in SQL, viene chiamata la funzione definita dall'utente anziché la funzione CLR a cui è stato eseguito il mapping.

## <a name="mapping-a-method-to-a-sql-function"></a>Mapping di un metodo a una funzione SQL

Per illustrare il funzionamento del mapping delle funzioni definite dall'utente, definire le entità seguenti:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

E la configurazione del modello seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

Il Blog può avere molti post e ogni post può presentare molti commenti.

Successivamente, creare la funzione definita dall'utente `CommentedPostCountForBlog` , che restituisce il numero di post con almeno un commento per un determinato Blog, in base al Blog `Id` :

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

Per usare questa funzione in EF Core, viene definito il metodo CLR seguente, mappato alla funzione definita dall'utente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

Il corpo del metodo CLR non è importante. Il metodo non verrà richiamato sul lato client, a meno che EF Core non possa tradurre gli argomenti. Se gli argomenti possono essere convertiti, EF Core interessa solo la firma del metodo.

> [!NOTE]
> Nell'esempio, il metodo è definito in `DbContext` , ma può anche essere definito come metodo statico all'interno di altre classi.

Questa definizione di funzione può ora essere associata a una funzione definita dall'utente nella configurazione del modello:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

Per impostazione predefinita, EF Core tenta di eseguire il mapping della funzione CLR a una funzione definita dall'utente con lo stesso nome. Se i nomi sono diversi, è possibile usare `HasName` per specificare il nome corretto per la funzione definita dall'utente a cui si vuole eseguire il mapping.

A questo punto, eseguire la query seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

Produrrà questo SQL:

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a>Mapping di un metodo a un oggetto SQL personalizzato

EF Core consente anche funzioni definite dall'utente che vengono convertite in un SQL specifico. L'espressione SQL viene fornita utilizzando il `HasTranslation` metodo durante la configurazione di funzioni definite dall'utente.

Nell'esempio seguente viene creata una funzione che calcola la differenza percentuale tra due numeri interi.

Il metodo CLR è il seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

La definizione della funzione è la seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

Una volta definita, la funzione può essere usata nella query. Invece di chiamare la funzione di database, EF Core convertirà il corpo del metodo direttamente in SQL in base alla struttura ad albero dell'espressione SQL creata da HasTranslation. La query LINQ seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

Produce il codice SQL seguente:

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a>Configurazione del supporto di valori null della funzione definita dall'utente in base ai relativi argomenti

Se la funzione definita dall'utente può restituire solo `null` quando uno o più degli argomenti sono `null` , EFCore fornisce un metodo per specificarlo, ottenendo un SQL più efficiente. Per eseguire questa operazione, è possibile aggiungere una `PropagatesNullability()` chiamata alla configurazione del modello dei parametri della funzione pertinente.

Per illustrare questo problema, definire la funzione utente `ConcatStrings` :

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

e due metodi CLR mappati a esso:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

La configurazione del modello (all'interno del `OnModelCreating` metodo) è la seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

La prima funzione viene configurata in modo standard. La seconda funzione viene configurata per sfruttare l'ottimizzazione della propagazione dei valori null, fornendo ulteriori informazioni sul comportamento della funzione intorno ai parametri null.

Quando si inviano le query seguenti:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

Si ottiene questo SQL:

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

La seconda query non deve rivalutare la funzione stessa per testarne il supporto di valori null.

> [!NOTE]
> Questa ottimizzazione deve essere utilizzata solo se la funzione può restituire solo `null` quando i parametri sono `null` .

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a>Mapping di una funzione Queryable a una funzione con valori di tabella

EF Core supporta anche il mapping a una funzione con valori di tabella utilizzando un metodo CLR definito dall'utente che restituisce un oggetto `IQueryable` di tipi di entità, consentendo EF Core di eseguire il mapping di funzioni con valori con parametri. Il processo è simile al mapping di una funzione scalare definita dall'utente a una funzione SQL: è necessario un TVF nel database, una funzione CLR usata nelle query LINQ e un mapping tra i due.

Si userà ad esempio una funzione con valori di tabella che restituisce tutti i post con almeno un commento che soddisfa una determinata soglia "like":

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

La firma del metodo CLR è la seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> La `FromExpression` chiamata nel corpo della funzione CLR consente di usare la funzione anziché un DbSet normale.

Di seguito è riportato il mapping:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> Finché non viene corretto il [problema 23408](https://github.com/dotnet/efcore/issues/23408) , il mapping a un `IQueryable` di tipi di entità sostituisce il mapping predefinito a una tabella per DbSet. Se necessario, ad esempio quando l'entità non è il mapping senza chiave alla tabella deve essere specificato in modo esplicito tramite il `ToTable` metodo.

> [!NOTE]
> La funzione Queryable deve essere mappata a una funzione con valori di tabella e non può utilizzare `HasTranslation` .

Quando viene eseguito il mapping della funzione, la query seguente:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

Produce

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
