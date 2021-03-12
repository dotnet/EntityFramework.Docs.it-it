---
title: Tipi di entità-EF Core
description: Come configurare ed eseguire il mapping di tipi di entità usando Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 6c8029b64210dd9bc69fd53d9a3b4aa06bf519e0
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023991"
---
# <a name="entity-types"></a>Tipi di entità

L'inclusione di un DbSet di un tipo nel contesto indica che è incluso nel modello di EF Core; in genere si fa riferimento a un tipo come un' *entità*. EF Core possibile leggere e scrivere istanze di entità da e verso il database e, se si utilizza un database relazionale, EF Core possibile creare tabelle per le entità tramite migrazioni.

## <a name="including-types-in-the-model"></a>Inclusione di tipi nel modello

Per convenzione, i tipi esposti nelle proprietà DbSet nel contesto sono inclusi nel modello come entità. Sono inclusi anche i tipi di entità specificati nel `OnModelCreating` metodo, così come tutti i tipi individuati in modo ricorsivo per esplorare le proprietà di navigazione di altri tipi di entità individuati.

Nell'esempio di codice riportato di seguito vengono inclusi tutti i tipi:

* `Blog` è incluso perché è esposto in una proprietà DbSet nel contesto.
* `Post` è incluso perché viene individuato tramite la `Blog.Posts` proprietà di navigazione.
* `AuditEntry` Poiché è specificato in `OnModelCreating` .

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>Esclusione di tipi dal modello

Se non si desidera che un tipo venga incluso nel modello, è possibile escluderlo:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a>Esclusione dalle migrazioni

> [!NOTE]
> La possibilità di escludere tabelle dalle migrazioni è stata introdotta in EF Core 5,0.

A volte è utile avere lo stesso tipo di entità mappato in più `DbContext` tipi. Ciò vale soprattutto quando si usano i [contesti delimitati](https://www.martinfowler.com/bliki/BoundedContext.html), per i quali è comune avere un `DbContext` tipo diverso per ogni contesto delimitato.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

Con queste migrazioni di configurazione la tabella non viene creata `AspNetUsers` , ma `IdentityUser` è ancora inclusa nel modello e può essere utilizzata normalmente.

Se è necessario iniziare a gestire la tabella usando di nuovo le migrazioni, è necessario creare una nuova migrazione in cui `AspNetUsers` non è escluso. La prossima migrazione conterrà ora le modifiche apportate alla tabella.

## <a name="table-name"></a>Nome tabella

Per convenzione, ogni tipo di entità verrà configurato per eseguire il mapping a una tabella di database con lo stesso nome della proprietà DbSet che espone l'entità. Se non esiste alcun DbSet per l'entità specificata, viene usato il nome della classe.

È possibile configurare manualmente il nome della tabella:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a>Schema della tabella

Quando si utilizza un database relazionale, le tabelle vengono create per convenzione nello schema predefinito del database. Ad esempio, Microsoft SQL Server utilizzerà lo `dbo` schema (SQLite non supporta gli schemi).

È possibile configurare le tabelle da creare in uno schema specifico, come indicato di seguito:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

Anziché specificare lo schema per ogni tabella, è anche possibile definire lo schema predefinito a livello di modello con l'API Fluent:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

Si noti che l'impostazione dello schema predefinito influirà anche su altri oggetti di database, ad esempio le sequenze.

## <a name="view-mapping"></a>Visualizza mapping

È possibile eseguire il mapping dei tipi di entità alle viste di database usando l'API Fluent.

> [!Note]
> EF presuppone che la vista a cui si fa riferimento sia già presente nel database, non la creerà automaticamente in una migrazione.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 Il mapping a una vista consente di rimuovere il mapping predefinito della tabella, ma a partire da EF 5,0 il tipo di entità può anche essere mappato in modo esplicito a una tabella. In questo caso, il mapping delle query verrà usato per le query e il mapping delle tabelle verrà usato per gli aggiornamenti.

> [!TIP]
> Per testare i tipi di entità di cui è stato eseguito il mapping alle viste usando il provider in memoria, eseguirne il mapping a una query tramite `ToInMemoryQuery` . Per altri dettagli, vedere un [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Miscellaneous/Testing/ItemsWebApi/) usando questa tecnica.

## <a name="table-valued-function-mapping"></a>Mapping di funzioni con valori di tabella

È possibile eseguire il mapping di un tipo di entità a una funzione con valori di tabella (TVF) anziché a una tabella nel database. Per illustrare questo problema, è possibile definire un'altra entità che rappresenta il Blog con più post. Nell'esempio, l'entità è senza [chiave](xref:core/modeling/keyless-entity-types), ma non deve essere.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

Successivamente, creare la funzione con valori di tabella seguente nel database, che restituisce solo i Blog con più post, oltre al numero di post associati a ognuno di questi Blog:

```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

A questo punto, `BlogWithMultiplePost` è possibile eseguire il mapping dell'entità a questa funzione nel modo seguente:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> Per eseguire il mapping di un'entità a una funzione con valori di tabella, la funzione deve essere senza parametri.

Convenzionalmente, le proprietà dell'entità verranno mappate alle colonne corrispondenti restituite da TVF. Se le colonne restituite da TVF hanno un nome diverso rispetto alla proprietà dell'entità, è possibile configurarle usando il `HasColumnName` metodo, come nel caso del mapping a una tabella normale.

Quando il tipo di entità viene mappato a una funzione con valori di tabella, la query:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

Produce il codice SQL seguente:

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a>Commenti tabella

È possibile impostare un commento di testo arbitrario che viene impostato nella tabella di database, consentendo di documentare lo schema nel database:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

> [!NOTE]
> L'impostazione di commenti tramite annotazioni dati è stata introdotta in EF Core 5,0.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

***
