---
title: Chiavi-EF Core
description: Come configurare le chiavi per i tipi di entità quando si usa Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: ea744315514b19465aba9a311055a9b5e49fa3f8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063790"
---
# <a name="keys"></a>Chiavi

Una chiave funge da identificatore univoco per ogni istanza dell'entità. Per la maggior parte delle entità in EF è disponibile un'unica chiave, che esegue il mapping al concetto di *chiave primaria* nei database relazionali (per le entità senza chiavi, vedere [entità](xref:core/modeling/keyless-entity-types)senza chiave). Le entità possono avere chiavi aggiuntive oltre la chiave primaria. per ulteriori informazioni, vedere [chiavi alternative](#alternate-keys) .

Per convenzione, una proprietà denominata `Id` o `<type name>Id` verrà configurata come chiave primaria di un'entità.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> I [tipi di entità di proprietà](xref:core/modeling/owned-entities) utilizzano regole diverse per definire le chiavi.

È possibile configurare una singola proprietà come chiave primaria di un'entità come indicato di seguito:

## <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

È anche possibile configurare più proprietà come chiave di un'entità. questa operazione è nota come chiave composta. Le chiavi composite possono essere configurate solo usando l'API Fluent; le convenzioni non installeranno mai una chiave composta e non è possibile usare le annotazioni dei dati per configurarne una.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a>Nome chiave primaria

Per convenzione, le chiavi primarie dei database relazionali vengono create con il nome `PK_<type name>` . È possibile configurare il nome del vincolo PRIMARY KEY come segue:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a>Tipi di chiave e valori

Mentre EF Core supporta l'utilizzo di proprietà di qualsiasi tipo primitivo come chiave primaria, tra cui `string` , `Guid` `byte[]` e altri, non tutti i database supportano tutti i tipi come chiavi. In alcuni casi i valori di chiave possono essere convertiti automaticamente in un tipo supportato. in caso contrario, la conversione deve essere [specificata manualmente](xref:core/modeling/value-conversions).

Quando si aggiunge una nuova entità al contesto, le proprietà chiave devono sempre avere un valore non predefinito, ma alcuni tipi verranno [generati dal database](xref:core/modeling/generated-properties). In tal caso, EF tenterà di generare un valore temporaneo quando l'entità viene aggiunta per finalità di rilevamento. Dopo la chiamata a [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) , il valore temporaneo verrà sostituito dal valore generato dal database.

> [!Important]
> Se una proprietà chiave ha il valore generato dal database e viene specificato un valore non predefinito quando viene aggiunta un'entità, Entity Framework presuppone che l'entità esista già nel database e tenterà di aggiornarla invece di inserirne una nuova. Per evitare questo problema, disabilitare la generazione di valori o vedere [come specificare valori espliciti per le proprietà generate](xref:core/saving/explicit-values-generated-properties).

## <a name="alternate-keys"></a>Chiavi alternative

Una chiave alternativa funge da identificatore univoco alternativo per ogni istanza di entità oltre alla chiave primaria. può essere usato come destinazione di una relazione. Quando si utilizza un database relazionale, viene eseguito il mapping al concetto di indice/vincolo univoco nelle colonne chiave alternative e di uno o più vincoli di chiave esterna che fanno riferimento alle colonne.

> [!TIP]
> Se si vuole semplicemente applicare l'univocità in una colonna, definire un indice univoco anziché una chiave alternativa (vedere [indici](xref:core/modeling/indexes)). In EF le chiavi alternative sono di sola lettura e forniscono una semantica aggiuntiva sugli indici univoci, perché possono essere utilizzate come destinazione di una chiave esterna.

Quando necessario, vengono in genere introdotte chiavi alternative e non è necessario configurarle manualmente. Per convenzione, viene introdotta una chiave alternativa quando si identifica una proprietà che non è la chiave primaria come destinazione di una relazione.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

È anche possibile configurare una singola proprietà in modo che sia una chiave alternativa:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

È anche possibile configurare più proprietà in modo che siano una chiave alternativa (nota come chiave alternativa composta):

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

Infine, per convenzione, gli indici e i vincoli introdotti per una chiave alternativa verranno denominati `AK_<type name>_<property name>` (per le chiavi alternative composite `<property name>` diventa un elenco di nomi di proprietà separati da un carattere di sottolineatura). È possibile configurare il nome dell'indice e del vincolo UNIQUE della chiave alternativa:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
