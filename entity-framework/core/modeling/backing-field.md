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
# <a name="backing-fields"></a><span data-ttu-id="fc97f-103">Campi sottostanti</span><span class="sxs-lookup"><span data-stu-id="fc97f-103">Backing Fields</span></span>

<span data-ttu-id="fc97f-104">I campi di backup consentono a EF di leggere e/o scrivere in un campo anziché in una proprietà.</span><span class="sxs-lookup"><span data-stu-id="fc97f-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="fc97f-105">Questa operazione può essere utile quando si utilizza l'incapsulamento della classe per limitare l'utilizzo di e/o migliorare la semantica di accesso ai dati da parte del codice dell'applicazione, ma il valore deve essere letto e/o scritto nel database senza utilizzare tali restrizioni/miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="fc97f-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="fc97f-106">Configurazione di base</span><span class="sxs-lookup"><span data-stu-id="fc97f-106">Basic configuration</span></span>

<span data-ttu-id="fc97f-107">Per convenzione, i campi seguenti vengono individuati come campi di backup per una determinata proprietà (elencati in ordine di precedenza).</span><span class="sxs-lookup"><span data-stu-id="fc97f-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="fc97f-108">Nell'esempio seguente, la `Url` proprietà è configurata in modo da avere `_url` come campo di supporto:</span><span class="sxs-lookup"><span data-stu-id="fc97f-108">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="fc97f-109">Si noti che i campi sottoposti a backup vengono individuati solo per le proprietà incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="fc97f-109">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="fc97f-110">Per ulteriori informazioni su quali proprietà sono incluse nel modello, vedere [inclusione & escluse le proprietà](xref:core/modeling/entity-properties).</span><span class="sxs-lookup"><span data-stu-id="fc97f-110">For more information on which properties are included in the model, see [Including & Excluding Properties](xref:core/modeling/entity-properties).</span></span>

<span data-ttu-id="fc97f-111">È anche possibile configurare i campi di backup usando un'annotazione dei dati (disponibile in EFCore 5,0) o l'API Fluent, ad esempio se il nome del campo non corrisponde alle convenzioni precedenti:</span><span class="sxs-lookup"><span data-stu-id="fc97f-111">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="fc97f-112">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="fc97f-112">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="fc97f-113">API Fluent</span><span class="sxs-lookup"><span data-stu-id="fc97f-113">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="fc97f-114">Accesso ai campi e alle proprietà</span><span class="sxs-lookup"><span data-stu-id="fc97f-114">Field and property access</span></span>

<span data-ttu-id="fc97f-115">Per impostazione predefinita, EF leggerà e scriverà sempre nel campo sottostante, presumendo che sia stato configurato correttamente, e non userà mai la proprietà.</span><span class="sxs-lookup"><span data-stu-id="fc97f-115">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="fc97f-116">Tuttavia, EF supporta anche altri modelli di accesso.</span><span class="sxs-lookup"><span data-stu-id="fc97f-116">However, EF also supports other access patterns.</span></span> <span data-ttu-id="fc97f-117">Ad esempio, l'esempio seguente indica a EF di scrivere nel campo sottostante solo quando materializzazione e di usare la proprietà in tutti gli altri casi:</span><span class="sxs-lookup"><span data-stu-id="fc97f-117">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="fc97f-118">Per il set completo di opzioni supportate, vedere l' [enumerazione PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) .</span><span class="sxs-lookup"><span data-stu-id="fc97f-118">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="fc97f-119">Con EF Core 3,0, la modalità di accesso alla proprietà predefinita è cambiata da `PreferFieldDuringConstruction` a `PreferField` .</span><span class="sxs-lookup"><span data-stu-id="fc97f-119">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="fc97f-120">Proprietà solo campo</span><span class="sxs-lookup"><span data-stu-id="fc97f-120">Field-only properties</span></span>

<span data-ttu-id="fc97f-121">È anche possibile creare una proprietà concettuale nel modello che non disponga di una proprietà CLR corrispondente nella classe di entità, ma usa invece un campo per archiviare i dati nell'entità.</span><span class="sxs-lookup"><span data-stu-id="fc97f-121">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="fc97f-122">Questo è diverso dalle [proprietà shadow](xref:core/modeling/shadow-properties), in cui i dati vengono archiviati nello strumento di rilevamento delle modifiche, anziché nel tipo CLR dell'entità.</span><span class="sxs-lookup"><span data-stu-id="fc97f-122">This is different from [Shadow Properties](xref:core/modeling/shadow-properties), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="fc97f-123">Le proprietà di solo campo vengono in genere utilizzate quando la classe di entità utilizza metodi anziché proprietà per ottenere o impostare valori oppure nei casi in cui i campi non devono essere esposti nel modello di dominio, ad esempio le chiavi primarie.</span><span class="sxs-lookup"><span data-stu-id="fc97f-123">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="fc97f-124">È possibile configurare una proprietà di solo campo fornendo un nome nell' `Property(...)` API:</span><span class="sxs-lookup"><span data-stu-id="fc97f-124">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="fc97f-125">EF tenterà di trovare una proprietà CLR con il nome specificato o un campo se non viene trovata una proprietà.</span><span class="sxs-lookup"><span data-stu-id="fc97f-125">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="fc97f-126">Se non viene trovata alcuna proprietà né un campo, verrà invece configurata una proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="fc97f-126">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="fc97f-127">Potrebbe essere necessario fare riferimento a una proprietà solo campo dalle query LINQ, ma tali campi sono in genere privati.</span><span class="sxs-lookup"><span data-stu-id="fc97f-127">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="fc97f-128">È possibile usare il `EF.Property(...)` metodo in una query LINQ per fare riferimento al campo:</span><span class="sxs-lookup"><span data-stu-id="fc97f-128">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

```csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
