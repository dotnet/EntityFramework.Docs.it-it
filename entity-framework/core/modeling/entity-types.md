---
title: Tipi di entità-EF Core
description: Come configurare ed eseguire il mapping di tipi di entità usando Entity Framework Core
author: roji
ms.date: 12/03/2019
uid: core/modeling/entity-types
ms.openlocfilehash: fead7f9e37efb7f674f429acbfd16c2ca78480d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071511"
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
