---
title: Campi di backup - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434175"
---
# <a name="backing-fields"></a>Campi sottostanti

I campi di backup consentono a EF di leggere e/o scrivere in un campo anziché in una proprietà. Ciò può essere utile quando l'incapsulamento nella classe viene utilizzato per limitare l'utilizzo e/o migliorare la semantica intorno all'accesso ai dati da parte del codice dell'applicazione, ma il valore deve essere letto e/o scritto nel database senza utilizzare tali restrizioni/miglioramenti.

## <a name="basic-configuration"></a>Configurazione di base

Per convenzione, i campi seguenti verranno individuati come campi di backup per una determinata proprietà (elencati in ordine di precedenza). 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

Nell'esempio seguente, `Url` la proprietà `_url` è configurata per avere come campo di backup:In the following sample, the property is configured to have as its backing field:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Si noti che i campi di backup vengono individuati solo per le proprietà incluse nel modello. Per ulteriori informazioni sulle proprietà incluse nel modello, vedere [Inclusione di & l'esclusione di proprietà](included-properties.md).

È inoltre possibile configurare i campi di backup usando un'annotazione dati o l'API Fluent, ad esempio se il nome del campo non corrisponde alle convenzioni precedenti:You can also configure backing fields by using a Data Annotation or the Fluent API, for example if the field name doesn't corresponds to the above conventions:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Accesso a campi e proprietà

Per impostazione predefinita, EF leggerà e scriverà sempre nel campo di backup, presupponendo che ne sia stato configurato correttamente, e non utilizzerà mai la proprietà. Tuttavia, EF supporta anche altri modelli di accesso. Ad esempio, l'esempio seguente indica a EF di scrivere nel campo di backup solo durante la materializzazione e di utilizzare la proprietà in tutti gli altri casi:For example, the following sample in structs EF to write to the backing field only while materializing, and to use the property in all other cases:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Vedere [l'enumerazione PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) per il set completo di opzioni supportate.

> [!NOTE]
> Con EF Core 3.0, la modalità di accesso alle proprietà predefinita è stata modificata da `PreferFieldDuringConstruction` a `PreferField`.

## <a name="field-only-properties"></a>Proprietà solo campo

È anche possibile creare una proprietà concettuale nel modello che non dispone di una proprietà CLR corrispondente nella classe di entità, ma utilizza invece un campo per archiviare i dati nell'entità. Questo è diverso da [Proprietà shadow](shadow-properties.md), in cui i dati vengono archiviati nel rilevamento delle modifiche, anziché nel tipo CLR dell'entità. Le proprietà solo campo vengono comunemente usate quando la classe di entità usa metodi anziché proprietà per ottenere/impostare valori o nei casi in cui i campi non devono essere esposti affatto nel modello di dominio (ad esempio chiavi primarie).

È possibile configurare una proprietà solo campo `Property(...)` specificando un nome nell'API:You can configure a field-only property by providing a name in the API:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF tenterà di trovare una proprietà CLR con il nome specificato o un campo se una proprietà non viene trovata. Se non viene trovata né una proprietà né un campo, verrà impostata una proprietà shadow.

Potrebbe essere necessario fare riferimento a una proprietà di solo campo dalle query LINQLINQ, ma tali campi sono in genere privati. È possibile `EF.Property(...)` utilizzare il metodo in una query LINQ per fare riferimento al campo:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
