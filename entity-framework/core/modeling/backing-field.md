---
title: Campi sottoposti a backup-EF Core
description: Configurazione dei campi di backup per le proprietà in un modello di Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/modeling/backing-field
ms.openlocfilehash: 4f5680b14c7f0e1fa5128d366f2960c4ae9b3735
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063686"
---
# <a name="backing-fields"></a>Campi sottostanti

I campi di backup consentono a EF di leggere e/o scrivere in un campo anziché in una proprietà. Questa operazione può essere utile quando si utilizza l'incapsulamento della classe per limitare l'utilizzo di e/o migliorare la semantica di accesso ai dati da parte del codice dell'applicazione, ma il valore deve essere letto e/o scritto nel database senza utilizzare tali restrizioni/miglioramenti.

## <a name="basic-configuration"></a>Configurazione di base

Per convenzione, i campi seguenti vengono individuati come campi di backup per una determinata proprietà (elencati in ordine di precedenza).

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

Nell'esempio seguente, la `Url` proprietà è configurata in modo da avere `_url` come campo di supporto:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Si noti che i campi sottoposti a backup vengono individuati solo per le proprietà incluse nel modello. Per ulteriori informazioni su quali proprietà sono incluse nel modello, vedere [inclusione & escluse le proprietà](xref:core/modeling/entity-properties).

È anche possibile configurare i campi di backup usando un'annotazione dei dati (disponibile in EFCore 5,0) o l'API Fluent, ad esempio se il nome del campo non corrisponde alle convenzioni precedenti:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Accesso ai campi e alle proprietà

Per impostazione predefinita, EF leggerà e scriverà sempre nel campo sottostante, presumendo che sia stato configurato correttamente, e non userà mai la proprietà. Tuttavia, EF supporta anche altri modelli di accesso. Ad esempio, l'esempio seguente indica a EF di scrivere nel campo sottostante solo quando materializzazione e di usare la proprietà in tutti gli altri casi:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Per il set completo di opzioni supportate, vedere l' [enumerazione PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) .

> [!NOTE]
> Con EF Core 3,0, la modalità di accesso alla proprietà predefinita è cambiata da `PreferFieldDuringConstruction` a `PreferField` .

## <a name="field-only-properties"></a>Proprietà solo campo

È anche possibile creare una proprietà concettuale nel modello che non disponga di una proprietà CLR corrispondente nella classe di entità, ma usa invece un campo per archiviare i dati nell'entità. Questo è diverso dalle [proprietà shadow](xref:core/modeling/shadow-properties), in cui i dati vengono archiviati nello strumento di rilevamento delle modifiche, anziché nel tipo CLR dell'entità. Le proprietà di solo campo vengono in genere utilizzate quando la classe di entità utilizza metodi anziché proprietà per ottenere o impostare valori oppure nei casi in cui i campi non devono essere esposti nel modello di dominio, ad esempio le chiavi primarie.

È possibile configurare una proprietà di solo campo fornendo un nome nell' `Property(...)` API:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF tenterà di trovare una proprietà CLR con il nome specificato o un campo se non viene trovata una proprietà. Se non viene trovata alcuna proprietà né un campo, verrà invece configurata una proprietà shadow.

Potrebbe essere necessario fare riferimento a una proprietà solo campo dalle query LINQ, ma tali campi sono in genere privati. È possibile usare il `EF.Property(...)` metodo in una query LINQ per fare riferimento al campo:

```csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
