---
title: Indici-EF Core
description: Configurazione di indici in un modello di Entity Framework Core
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "98128537"
---
# <a name="indexes"></a>Indici

Gli indici sono un concetto comune in molti archivi dati. Mentre la loro implementazione nell'archivio dati può variare, vengono usati per rendere più efficienti le ricerche basate su una colonna o un set di colonne. Per ulteriori informazioni sull'utilizzo di un indice efficace, vedere la [sezione indici](xref:core/performance/efficient-querying#use-indexes-properly) nella documentazione relativa alle prestazioni.

È possibile specificare un indice su una colonna come indicato di seguito:

## <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> La configurazione degli indici tramite le annotazioni dei dati è stata introdotta in EF Core 5,0.

## <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> Per convenzione, viene creato un indice in ogni proprietà o set di proprietà utilizzate come chiave esterna.
>
> EF Core supporta solo un indice per set di proprietà distinti. Se si configura un indice in un set di proprietà in cui è già definito un indice, per convenzione o per una configurazione precedente, si modificherà la definizione di tale indice. Questa opzione è utile se si desidera configurare ulteriormente un indice creato per convenzione.

## <a name="composite-index"></a>Indice composto

Un indice può inoltre estendersi su più di una colonna:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

***

Gli indici su più colonne, noti anche come *indici compositi*, velocizzano le query che filtrano le colonne dell'indice, ma anche le query che filtrano solo le *prime* colonne analizzate dall'indice. Per ulteriori informazioni, vedere la documentazione relativa alle [prestazioni](xref:core/performance/efficient-querying#use-indexes-properly) .

## <a name="index-uniqueness"></a>Univocità indice

Per impostazione predefinita, gli indici non sono univoci: più righe possono avere gli stessi valori per il set di colonne dell'indice. È possibile rendere univoco un indice nel modo seguente:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

Se si tenta di inserire più di un'entità con gli stessi valori per il set di colonne dell'indice, verrà generata un'eccezione.

## <a name="index-name"></a>Nome dell'indice

Per convenzione, gli indici creati in un database relazionale vengono denominati `IX_<type name>_<property name>` . Per gli indici composti, `<property name>` diventa un elenco di nomi di proprietà separato da un carattere di sottolineatura.

È possibile impostare il nome dell'indice creato nel database:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a>Filtro indice

Alcuni database relazionali consentono di specificare un indice filtrato o parziale. Ciò consente di indicizzare solo un subset di valori di una colonna, riducendo le dimensioni dell'indice e migliorando le prestazioni e l'utilizzo dello spazio su disco. Per ulteriori informazioni su SQL Server indici filtrati, [vedere la documentazione](/sql/relational-databases/indexes/create-filtered-indexes)di.

È possibile utilizzare l'API Fluent per specificare un filtro per un indice, fornito come espressione SQL:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

Quando si usa il provider di SQL Server EF aggiunge un `'IS NOT NULL'` filtro per tutte le colonne nullable che fanno parte di un indice univoco. Per eseguire l'override di questa convenzione, è possibile specificare un `null` valore.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a>included_columns

Alcuni database relazionali consentono di configurare un set di colonne che vengono incluse nell'indice, ma non fanno parte della relativa chiave. Questo può migliorare significativamente le prestazioni di esecuzione delle query quando tutte le colonne della query sono incluse nell'indice come colonne chiave o colonne, in quanto non è necessario accedere alla tabella stessa. Per ulteriori informazioni su SQL Server colonne incluse, [vedere la documentazione](/sql/relational-databases/indexes/create-indexes-with-included-columns)di.

Nell'esempio seguente la `Url` colonna fa parte della chiave di indice, pertanto qualsiasi filtro di query su tale colonna può usare l'indice. Inoltre, le query che accedono solo alle `Title` `PublishedOn` colonne e non dovranno accedere alla tabella e vengono eseguite in modo più efficiente:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
