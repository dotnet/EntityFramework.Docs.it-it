---
title: Campi sottoposti a backup-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: e015c4f3fca767d25bee179c027813bd9fcf4c07
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526758"
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

Si noti che i campi sottoposti a backup vengono individuati solo per le proprietà incluse nel modello. Per ulteriori informazioni su quali proprietà sono incluse nel modello, vedere [inclusione & escluse le proprietà](included-properties.md).

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

È anche possibile creare una proprietà concettuale nel modello che non disponga di una proprietà CLR corrispondente nella classe di entità, ma usa invece un campo per archiviare i dati nell'entità. Questo è diverso dalle [proprietà shadow](shadow-properties.md), in cui i dati vengono archiviati nello strumento di rilevamento delle modifiche, anziché nel tipo CLR dell'entità. Le proprietà di solo campo vengono in genere utilizzate quando la classe di entità utilizza metodi anziché proprietà per ottenere o impostare valori oppure nei casi in cui i campi non devono essere esposti nel modello di dominio, ad esempio le chiavi primarie.

È possibile configurare una proprietà di solo campo fornendo un nome nell' `Property(...)` API:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF tenterà di trovare una proprietà CLR con il nome specificato o un campo se non viene trovata una proprietà. Se non viene trovata alcuna proprietà né un campo, verrà invece configurata una proprietà shadow.

Potrebbe essere necessario fare riferimento a una proprietà solo campo dalle query LINQ, ma tali campi sono in genere privati. È possibile usare il `EF.Property(...)` metodo in una query LINQ per fare riferimento al campo:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
