---
title: Valori generati-EF Core
description: Come configurare la generazione di valori per le proprietà quando si usa Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: a10ed3522c255f18d31a75e237a5507fdba468a1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619375"
---
# <a name="generated-values"></a>Valori generati

## <a name="value-generation-patterns"></a>Modelli di generazione di valori

Sono disponibili tre modelli di generazione dei valori che possono essere usati per le proprietà:

* Nessuna generazione di valori
* Valore generato in aggiunta
* Valore generato durante l'aggiunta o l'aggiornamento

### <a name="no-value-generation"></a>Nessuna generazione di valori

Nessuna generazione di valori indica che verrà sempre fornito un valore valido da salvare nel database. Questo valore valido deve essere assegnato a nuove entità prima che vengano aggiunte al contesto.

### <a name="value-generated-on-add"></a>Valore generato in aggiunta

Il valore generato durante l'aggiunta indica che viene generato un valore per le nuove entità.

A seconda del provider di database in uso, è possibile che i valori vengano generati lato client da EF o nel database. Se il valore viene generato dal database, EF può assegnare un valore temporaneo quando si aggiunge l'entità al contesto. Questo valore temporaneo verrà quindi sostituito dal valore generato dal database durante `SaveChanges()` .

Se si aggiunge un'entità al contesto a cui è assegnato un valore assegnato alla proprietà, EF tenterà di inserire tale valore anziché generarne uno nuovo. A una proprietà viene assegnato un valore se non viene assegnato il valore predefinito CLR (per, per `null` , `string` `0` `int` `Guid.Empty` per `Guid` e così via). Per ulteriori informazioni, vedere [valori espliciti per le proprietà generate](xref:core/saving/explicit-values-generated-properties).

> [!WARNING]
> Il modo in cui il valore viene generato per le entità aggiunte dipenderà dal provider di database in uso. I provider di database possono impostare automaticamente la generazione del valore per alcuni tipi di proprietà, mentre altri potrebbero richiedere di configurare manualmente il modo in cui viene generato il valore.
>
> Quando si utilizza SQL Server, ad esempio, i valori vengono generati automaticamente per `GUID` le proprietà (usando l'algoritmo GUID sequenziale SQL Server). Tuttavia, se si specifica che una `DateTime` proprietà viene generata in aggiunta, è necessario configurare una modalità per la generazione dei valori. A tale scopo, è possibile configurare un valore predefinito di `GETDATE()` , vedere [valori predefiniti](#default-values).

### <a name="value-generated-on-add-or-update"></a>Valore generato durante l'aggiunta o l'aggiornamento

Il valore generato durante l'aggiunta o l'aggiornamento indica che viene generato un nuovo valore ogni volta che il record viene salvato (inserimento o aggiornamento).

Analogamente `value generated on add` , se si specifica un valore per la proprietà in un'istanza appena aggiunta di un'entità, tale valore verrà inserito anziché un valore generato. È anche possibile impostare un valore esplicito durante l'aggiornamento. Per ulteriori informazioni, vedere [valori espliciti per le proprietà generate](xref:core/saving/explicit-values-generated-properties).

> [!WARNING]
> Il modo in cui il valore viene generato per le entità aggiunte e aggiornate dipenderà dal provider di database in uso. I provider di database possono impostare automaticamente la generazione del valore per alcuni tipi di proprietà, mentre altri richiederanno di configurare manualmente il modo in cui viene generato il valore.
>
> Quando si utilizza SQL Server, ad esempio, `byte[]` le proprietà impostate come generate in Aggiungi o aggiorna e contrassegnate come token di concorrenza verranno impostate con il `rowversion` tipo di dati, in modo che i valori vengano generati nel database. Tuttavia, se si specifica che una `DateTime` proprietà viene generata durante l'aggiunta o l'aggiornamento, è necessario configurare una modalità per la generazione dei valori. A tale scopo, è possibile configurare un valore predefinito di `GETDATE()` (vedere [valori predefiniti](#default-values)) per generare valori per le nuove righe. È quindi possibile usare un trigger di database per generare valori durante gli aggiornamenti, ad esempio il trigger di esempio seguente.
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>Valore generato in aggiunta

Per convenzione, le chiavi primarie non composite di tipo short, int, Long o GUID sono impostate per avere valori generati per le entità inserite, se un valore non è fornito dall'applicazione. Il provider di database gestisce in genere la configurazione necessaria. una chiave primaria numerica in SQL Server, ad esempio, viene configurata automaticamente come colonna IDENTITY.

È possibile configurare qualsiasi proprietà in modo che il relativo valore venga generato per le entità inserite come indicato di seguito:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> In questo modo EF sa che i valori vengono generati per le entità aggiunte, non garantisce che EF configurerà il meccanismo effettivo per generare valori. Per altri dettagli, vedere [valore generato nella sezione Aggiungi](#value-generated-on-add) .

### <a name="default-values"></a>Valori predefiniti

Nei database relazionali, una colonna può essere configurata con un valore predefinito. Se viene inserita una riga senza un valore per tale colonna, verrà utilizzato il valore predefinito.

È possibile configurare un valore predefinito in una proprietà:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

È anche possibile specificare un frammento SQL usato per calcolare il valore predefinito:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Se si specifica un valore predefinito, la proprietà viene configurata in modo implicito come valore generato durante l'aggiunta.

## <a name="value-generated-on-add-or-update"></a>Valore generato durante l'aggiunta o l'aggiornamento

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> In questo modo, EF sa che i valori vengono generati per le entità aggiunte o aggiornate, ma non garantisce che EF configurerà il meccanismo effettivo per generare valori. Per ulteriori informazioni, vedere la sezione [valore generato nella sezione Aggiungi o aggiorna](#value-generated-on-add-or-update) .

### <a name="computed-columns"></a>Colonne calcolate

In alcuni database relazionali, una colonna può essere configurata in modo che il relativo valore venga calcolato nel database, in genere con un'espressione che fa riferimento ad altre colonne:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=ComputedColumn&highlight=5)]

> [!NOTE]
> In alcuni casi, il valore della colonna viene calcolato ogni volta che viene recuperato (talvolta denominato colonne *virtuali* ) e in altri viene calcolato a ogni aggiornamento della riga e archiviato (talvolta denominato colonne *archiviate* o *rese permanente* ). Questa operazione varia in base ai provider di database.

## <a name="no-value-generation"></a>Nessuna generazione di valori

La disabilitazione della generazione di valori in una proprietà è in genere necessaria se una convenzione la configura per la generazione di valori. Se, ad esempio, si dispone di una chiave primaria di tipo int, questa verrà impostata in modo implicito come valore generato durante l'aggiunta; Questa operazione può essere disabilitata tramite quanto segue:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
