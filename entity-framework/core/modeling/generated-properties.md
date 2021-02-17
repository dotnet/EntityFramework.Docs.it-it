---
title: Valori generati-EF Core
description: Come configurare la generazione di valori per le proprietà quando si usa Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: a9e43f3b755bf028bc76581135988e831a42d0d1
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543341"
---
# <a name="generated-values"></a>Valori generati

È possibile generare valori per le colonne del database in diversi modi: le colonne chiave primaria sono spesso numeri interi a incremento automatico, altre colonne hanno valori predefiniti o calcolati e così via. In questa pagina vengono illustrati i vari modelli per la generazione di valori di configurazione con EF Core.

## <a name="default-values"></a>Valori predefiniti

Nei database relazionali, una colonna può essere configurata con un valore predefinito. Se viene inserita una riga senza un valore per tale colonna, verrà utilizzato il valore predefinito.

È possibile configurare un valore predefinito in una proprietà:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

È anche possibile specificare un frammento SQL usato per calcolare il valore predefinito:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a>Colonne calcolate

Nella maggior parte dei database relazionali, una colonna può essere configurata in modo che il relativo valore venga calcolato nel database, in genere con un'espressione che fa riferimento ad altre colonne:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

Il precedente crea una colonna calcolata *virtuale* , il cui valore viene calcolato ogni volta che viene recuperato dal database. È inoltre possibile specificare che una colonna calcolata deve essere *archiviata* (talvolta denominata *permanente*), ovvero viene calcolata a ogni aggiornamento della riga e viene archiviata su disco insieme a colonne normali:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> Il supporto per la creazione di colonne calcolate archiviate è stato aggiunto in EF Core 5,0.

## <a name="primary-keys"></a>Chiavi primarie

Per convenzione, le chiavi primarie non composite di tipo short, int, Long o GUID sono impostate in modo da generare valori per le entità inserite se un valore non viene fornito dall'applicazione. Il provider di database gestisce in genere la configurazione necessaria. una chiave primaria numerica in SQL Server, ad esempio, viene configurata automaticamente come colonna IDENTITY.

Per ulteriori informazioni, [vedere la documentazione sulle chiavi](xref:core/modeling/keys).

## <a name="explicitly-configuring-value-generation"></a>Configurazione esplicita della generazione di valori

Abbiamo visto sopra che EF Core imposta automaticamente la generazione di valori per le chiavi primarie, ma è consigliabile eseguire la stessa operazione per le proprietà non chiave. È possibile configurare qualsiasi proprietà in modo che il relativo valore venga generato per le entità inserite come indicato di seguito:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

Analogamente, una proprietà può essere configurata in modo da generare il relativo valore in Aggiungi o aggiorna:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=6)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

***

> [!WARNING]
> Diversamente da quanto avviene con i valori predefiniti o le colonne calcolate, non viene specificata la *modalità* di generazione dei valori. dipende dal provider di database utilizzato. I provider di database possono impostare automaticamente la generazione di valori per alcuni tipi di proprietà, ma altri potrebbero richiedere di configurare manualmente la modalità di generazione del valore.
>
> Ad esempio, in SQL Server, quando una proprietà GUID viene configurata come valore generato su Aggiungi, il provider esegue automaticamente il lato client di generazione del valore, usando un algoritmo per generare valori GUID sequenziali ottimali. Tuttavia, `ValueGeneratedOnAdd()` l'impostazione di su una proprietà DateTime non avrà alcun effetto ([vedere la sezione seguente per la generazione di valori DateTime](#datetime-value-generation)).
>
> Analogamente, le proprietà byte [] configurate come generate in Aggiungi o aggiorna e contrassegnate come token di concorrenza vengono impostate con il tipo di dati rowversion, in modo che i valori vengano generati automaticamente nel database. Tuttavia, la specifica di `ValueGeneratedOnAddOrUpdate()` non avrà alcun effetto.
>
> [!NOTE]
> A seconda del provider di database in uso, è possibile che i valori vengano generati lato client da EF o nel database. Se il valore viene generato dal database, EF può assegnare un valore temporaneo quando si aggiunge l'entità al contesto; Questo valore temporaneo verrà quindi sostituito dal valore generato dal database durante `SaveChanges()` . Per ulteriori informazioni, [vedere la documentazione relativa ai valori temporanei](xref:core/change-tracking/explicit-tracking#temporary-values).

## <a name="datetime-value-generation"></a>Generazione di valori di data/ora

Una richiesta comune consiste nel disporre di una colonna di database contenente la data e l'ora in cui la colonna è stata inserita per la prima volta (valore generato al momento dell'aggiunta) o per l'ultimo aggiornamento (valore generato durante l'aggiunta o l'aggiornamento). Poiché esistono diverse strategie per eseguire questa operazione, i provider EF Core in genere non configurano automaticamente la generazione di valori per le colonne di data/ora. è necessario configurarlo manualmente.

### <a name="creation-timestamp"></a>Timestamp di creazione

La configurazione di una colonna di data/ora in modo che il timestamp di creazione della riga riporti in genere la configurazione di un valore predefinito con la funzione SQL appropriata. In SQL Server, ad esempio, è possibile utilizzare quanto segue:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Assicurarsi di selezionare la funzione appropriata, in quanto possono esistere diverse (ad esempio, `GETDATE()` vs. `GETUTCDATE()` ).

### <a name="update-timestamp"></a>Aggiorna timestamp

Sebbene le colonne calcolate archiviate sembrino una soluzione efficace per la gestione dei timestamp aggiornati per ultimi, i database in genere non consentono di specificare funzioni come `GETDATE()` in una colonna calcolata. In alternativa, è possibile configurare un trigger di database per ottenere lo stesso effetto:

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

Per informazioni sulla creazione di trigger, [vedere la documentazione sull'uso di SQL non elaborato nelle migrazioni](xref:core/managing-schemas/migrations/managing#adding-raw-sql).

## <a name="overriding-value-generation"></a>Override della generazione di valori

Sebbene una proprietà sia configurata per la generazione di valori, in molti casi è comunque possibile specificare in modo esplicito un valore. Il funzionamento di questa funzione dipende dal meccanismo di generazione di valori specifico configurato; Sebbene sia possibile specificare un valore esplicito anziché usare il valore predefinito di una colonna, non è possibile eseguire la stessa operazione con le colonne calcolate.

Per eseguire l'override della generazione di valori con un valore esplicito, è sufficiente impostare la proprietà su un valore diverso da quello predefinito di CLR per il tipo di tale proprietà (per, per, `null` `string` per e `0` `int` `Guid.Empty` `Guid` così via).

> [!NOTE]
> Il tentativo di inserire valori espliciti in SQL Server identità ha esito negativo per impostazione predefinita. [vedere questi documenti per una soluzione alternativa](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).

Per fornire un valore esplicito per le proprietà che sono state configurate come valore generato durante l'aggiunta o l'aggiornamento, è necessario configurare anche la proprietà come indicato di seguito:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a>Nessuna generazione di valori

Oltre a scenari specifici come quelli descritti in precedenza, le proprietà non hanno in genere alcuna generazione di valori configurata. Ciò significa che l'applicazione deve sempre fornire un valore da salvare nel database. Questo valore deve essere assegnato a nuove entità prima che vengano aggiunte al contesto.

In alcuni casi, tuttavia, potrebbe essere necessario disabilitare la generazione di valori configurata per convenzione. Una chiave primaria di tipo int, ad esempio, viene in genere configurata in modo implicito come valore generato in base al valore aggiunto (ad esempio, colonna Identity in SQL Server). Questa operazione può essere disabilitata tramite quanto segue:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
