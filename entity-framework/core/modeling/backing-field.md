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
# <a name="backing-fields"></a><span data-ttu-id="2431d-102">Campi sottostanti</span><span class="sxs-lookup"><span data-stu-id="2431d-102">Backing Fields</span></span>

<span data-ttu-id="2431d-103">I campi di backup consentono a EF di leggere e/o scrivere in un campo anziché in una proprietà.</span><span class="sxs-lookup"><span data-stu-id="2431d-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="2431d-104">Ciò può essere utile quando l'incapsulamento nella classe viene utilizzato per limitare l'utilizzo e/o migliorare la semantica intorno all'accesso ai dati da parte del codice dell'applicazione, ma il valore deve essere letto e/o scritto nel database senza utilizzare tali restrizioni/miglioramenti.</span><span class="sxs-lookup"><span data-stu-id="2431d-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="2431d-105">Configurazione di base</span><span class="sxs-lookup"><span data-stu-id="2431d-105">Basic configuration</span></span>

<span data-ttu-id="2431d-106">Per convenzione, i campi seguenti verranno individuati come campi di backup per una determinata proprietà (elencati in ordine di precedenza).</span><span class="sxs-lookup"><span data-stu-id="2431d-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="2431d-107">Nell'esempio seguente, `Url` la proprietà `_url` è configurata per avere come campo di backup:In the following sample, the property is configured to have as its backing field:</span><span class="sxs-lookup"><span data-stu-id="2431d-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="2431d-108">Si noti che i campi di backup vengono individuati solo per le proprietà incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="2431d-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="2431d-109">Per ulteriori informazioni sulle proprietà incluse nel modello, vedere [Inclusione di & l'esclusione di proprietà](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="2431d-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="2431d-110">È inoltre possibile configurare i campi di backup usando un'annotazione dati o l'API Fluent, ad esempio se il nome del campo non corrisponde alle convenzioni precedenti:You can also configure backing fields by using a Data Annotation or the Fluent API, for example if the field name doesn't corresponds to the above conventions:</span><span class="sxs-lookup"><span data-stu-id="2431d-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="2431d-111">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="2431d-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="2431d-112">API Fluent</span><span class="sxs-lookup"><span data-stu-id="2431d-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="2431d-113">Accesso a campi e proprietà</span><span class="sxs-lookup"><span data-stu-id="2431d-113">Field and property access</span></span>

<span data-ttu-id="2431d-114">Per impostazione predefinita, EF leggerà e scriverà sempre nel campo di backup, presupponendo che ne sia stato configurato correttamente, e non utilizzerà mai la proprietà.</span><span class="sxs-lookup"><span data-stu-id="2431d-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="2431d-115">Tuttavia, EF supporta anche altri modelli di accesso.</span><span class="sxs-lookup"><span data-stu-id="2431d-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="2431d-116">Ad esempio, l'esempio seguente indica a EF di scrivere nel campo di backup solo durante la materializzazione e di utilizzare la proprietà in tutti gli altri casi:For example, the following sample in structs EF to write to the backing field only while materializing, and to use the property in all other cases:</span><span class="sxs-lookup"><span data-stu-id="2431d-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="2431d-117">Vedere [l'enumerazione PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) per il set completo di opzioni supportate.</span><span class="sxs-lookup"><span data-stu-id="2431d-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="2431d-118">Con EF Core 3.0, la modalità di accesso alle proprietà predefinita è stata modificata da `PreferFieldDuringConstruction` a `PreferField`.</span><span class="sxs-lookup"><span data-stu-id="2431d-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="2431d-119">Proprietà solo campo</span><span class="sxs-lookup"><span data-stu-id="2431d-119">Field-only properties</span></span>

<span data-ttu-id="2431d-120">È anche possibile creare una proprietà concettuale nel modello che non dispone di una proprietà CLR corrispondente nella classe di entità, ma utilizza invece un campo per archiviare i dati nell'entità.</span><span class="sxs-lookup"><span data-stu-id="2431d-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="2431d-121">Questo è diverso da [Proprietà shadow](shadow-properties.md), in cui i dati vengono archiviati nel rilevamento delle modifiche, anziché nel tipo CLR dell'entità.</span><span class="sxs-lookup"><span data-stu-id="2431d-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="2431d-122">Le proprietà solo campo vengono comunemente usate quando la classe di entità usa metodi anziché proprietà per ottenere/impostare valori o nei casi in cui i campi non devono essere esposti affatto nel modello di dominio (ad esempio chiavi primarie).</span><span class="sxs-lookup"><span data-stu-id="2431d-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="2431d-123">È possibile configurare una proprietà solo campo `Property(...)` specificando un nome nell'API:You can configure a field-only property by providing a name in the API:</span><span class="sxs-lookup"><span data-stu-id="2431d-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="2431d-124">EF tenterà di trovare una proprietà CLR con il nome specificato o un campo se una proprietà non viene trovata.</span><span class="sxs-lookup"><span data-stu-id="2431d-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="2431d-125">Se non viene trovata né una proprietà né un campo, verrà impostata una proprietà shadow.</span><span class="sxs-lookup"><span data-stu-id="2431d-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="2431d-126">Potrebbe essere necessario fare riferimento a una proprietà di solo campo dalle query LINQLINQ, ma tali campi sono in genere privati.</span><span class="sxs-lookup"><span data-stu-id="2431d-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="2431d-127">È possibile `EF.Property(...)` utilizzare il metodo in una query LINQ per fare riferimento al campo:</span><span class="sxs-lookup"><span data-stu-id="2431d-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
