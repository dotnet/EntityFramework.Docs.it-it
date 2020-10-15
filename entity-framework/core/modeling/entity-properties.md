---
title: Proprietà entità-EF Core
description: Come configurare e mappare le proprietà di un'entità usando Entity Framework Core
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 99b0a9ee1e11714e98ebea8b2e6ac53bd2dc6e55
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062295"
---
# <a name="entity-properties"></a>Proprietà delle entità

Ogni tipo di entità nel modello dispone di un set di proprietà, che EF Core leggerà e scriverà dal database. Se si usa un database relazionale, le proprietà dell'entità vengono mappate alle colonne della tabella.

## <a name="included-and-excluded-properties"></a>Proprietà incluse ed escluse

Per convenzione, tutte le proprietà pubbliche con un getter e un setter verranno incluse nel modello.

Le proprietà specifiche possono essere escluse come segue:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>Nome colonna

Per convenzione, quando si utilizza un database relazionale, viene eseguito il mapping delle proprietà delle entità alle colonne della tabella con lo stesso nome della proprietà.

Se si preferisce configurare le colonne con nomi diversi, è possibile eseguire questa operazione come frammento di codice seguente:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a>Tipi di dati delle colonne

Quando si utilizza un database relazionale, il provider di database seleziona un tipo di dati basato sul tipo .NET della proprietà. Prende in considerazione anche altri metadati, ad esempio la [lunghezza massima](#maximum-length)configurata, se la proprietà fa parte di una chiave primaria e così via.

Ad esempio, SQL Server esegue `DateTime` il mapping delle proprietà alle `datetime2(7)` colonne e `string` delle proprietà alle `nvarchar(max)` colonne (o a `nvarchar(450)` per le proprietà utilizzate come chiave).

È inoltre possibile configurare le colonne in modo da specificare un tipo di dati esatto per una colonna. Il codice seguente, ad esempio, Configura `Url` come una stringa non Unicode con lunghezza massima `200` e `Rating` come decimale con precisione `5` e scala di `2` :

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a>Lunghezza massima

La configurazione di una lunghezza massima fornisce un suggerimento al provider di database sul tipo di dati della colonna appropriato da scegliere per una determinata proprietà. La lunghezza massima si applica solo ai tipi di dati della matrice, ad esempio `string` e `byte[]` .

> [!NOTE]
> Entity Framework non esegue alcuna convalida della lunghezza massima prima di passare i dati al provider. È necessario che il provider o l'archivio dati venga convalidato se appropriato. Se, ad esempio, la destinazione è SQL Server, il superamento della lunghezza massima provocherà un'eccezione poiché il tipo di dati della colonna sottostante non consentirà l'archiviazione di dati in eccesso.

Nell'esempio seguente, la configurazione di una lunghezza massima di 500 causerà la creazione di una colonna di tipo `nvarchar(500)` in SQL Server:

#### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a>Precisione e scala

A partire da EFCore 5,0, è possibile usare l'API Fluent per configurare la precisione e la scalabilità. Indica al provider di database la quantità di spazio di archiviazione necessaria per una determinata colonna. Si applica solo ai tipi di dati in cui il provider consente la precisione e la scalabilità variano, in genere `decimal` e `DateTime` .

Per le `decimal` proprietà, la precisione definisce il numero massimo di cifre necessarie per esprimere qualsiasi valore che la colonna conterrà e la scalabilità definisce il numero massimo di posizioni decimali necessarie. Per `DateTime` le proprietà, la precisione definisce il numero massimo di cifre necessarie per esprimere frazioni di secondi e la scala non viene utilizzata.

> [!NOTE]
> Entity Framework non esegue alcuna convalida della precisione o della scala prima di passare i dati al provider. È necessario che il provider o l'archivio dati venga convalidato in base alle esigenze. Ad esempio, quando la destinazione è SQL Server, una colonna con tipo di dati `datetime` non consente l'impostazione della precisione, mentre una `datetime2` può avere una precisione compresa tra 0 e 7 inclusi.

Nell'esempio seguente, se si configura la `Score` Proprietà in modo che la precisione 14 e la scala 2 provochino la creazione di una colonna di tipo `decimal(14,2)` in SQL Server e la configurazione della `LastUpdated` Proprietà in modo che la precisione 3 provochi una colonna di tipo `datetime2(3)` :

#### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

Attualmente non è possibile usare le annotazioni dei dati per la configurazione.

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> La scala non viene mai definita senza prima definire la precisione, quindi l'API Fluent per la definizione della scala è `HasPrecision(precision, scale)` .

***

## <a name="required-and-optional-properties"></a>Proprietà obbligatorie e facoltative

Una proprietà è considerata facoltativa se è valida per contenerla `null` . Se `null` non è un valore valido da assegnare a una proprietà, viene considerata una proprietà obbligatoria. Quando si esegue il mapping a uno schema di database relazionale, le proprietà obbligatorie vengono create come colonne che non ammettono i valori null e le proprietà facoltative vengono create come colonne nullable.

### <a name="conventions"></a>Convenzioni

Per convenzione, una proprietà il cui tipo .NET può contenere null verrà configurato come facoltativo, mentre le proprietà il cui tipo .NET non può contenere valori null verranno configurate in base alle esigenze. Tutte le proprietà con tipi di valore .NET ( `int` , `decimal` , e così via), ad esempio, `bool` sono configurate come obbligatorie e tutte le proprietà con tipi di valore .NET Nullable ( `int?` , `decimal?` , `bool?` e così via) sono configurate come facoltative.

In C# 8 è stata introdotta una nuova funzionalità denominata [tipi di riferimento Nullable](/dotnet/csharp/tutorials/nullable-reference-types), che consente di aggiungere annotazioni ai tipi di riferimento, indicando se è possibile che contengano null o meno. Questa funzionalità è disabilitata per impostazione predefinita e, se abilitata, modifica il comportamento del EF Core nel modo seguente:

* Se i tipi di riferimento nullable sono disabilitati (impostazione predefinita), tutte le proprietà con i tipi di riferimento .NET vengono configurate come facoltative per convenzione, ad esempio `string` .
* Se sono abilitati i tipi di riferimento Nullable, le proprietà verranno configurate in base al supporto di valori null C# del tipo .NET: `string?` verranno configurate come facoltative, ma `string` verranno configurate in base alle esigenze.

Nell'esempio seguente viene illustrato un tipo di entità con proprietà obbligatorie e facoltative, con la funzionalità di riferimento Nullable disabilitata (impostazione predefinita) e abilitata:

#### <a name="without-nullable-reference-types-default"></a>[Senza tipi di riferimento Nullable (impostazione predefinita)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[Con tipi di riferimento Nullable](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

L'uso di tipi di riferimento Nullable è consigliato perché trasmette il supporto di valori null espresso nel codice C# al modello di EF Core e al database e evita l'uso dell'API Fluent o delle annotazioni dei dati per esprimere lo stesso concetto due volte.

> [!NOTE]
> Prestare attenzione quando si abilitano i tipi di riferimento nullable in un progetto esistente: le proprietà del tipo di riferimento che in precedenza erano configurate come facoltative ora verranno configurate come richieste, a meno che non vengano annotate in modo esplicito come Nullable. Quando si gestisce uno schema di database relazionale, è possibile che vengano generate migrazioni che modificano il supporto di valori null per la colonna di database.

Per ulteriori informazioni sui tipi di riferimento nullable e su come utilizzarli con EF Core, [vedere la pagina della documentazione dedicata per questa funzionalità](xref:core/miscellaneous/nullable-reference-types).

### <a name="explicit-configuration"></a>Configurazione esplicita

Una proprietà che sarebbe facoltativa per convenzione può essere configurata in modo da essere obbligatoria come indicato di seguito:

#### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a>Regole di confronto delle colonne

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

È possibile definire regole di confronto nelle colonne di testo, determinando il modo in cui vengono confrontate e ordinate. Nel frammento di codice seguente, ad esempio, viene configurata una colonna SQL Server per non fare distinzione tra maiuscole e minuscole:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

Se tutte le colonne in un database devono utilizzare una determinata regola di confronto, definire le regole di confronto a livello di database.

Informazioni generali sul supporto EF Core per le regole di confronto sono disponibili nella [pagina della documentazione relativa alle regole di confronto](xref:core/miscellaneous/collations-and-case-sensitivity).
