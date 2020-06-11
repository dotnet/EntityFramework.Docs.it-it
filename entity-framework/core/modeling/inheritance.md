---
title: Ereditarietà-EF Core
description: Come configurare l'ereditarietà del tipo di entità usando Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664052"
---
# <a name="inheritance"></a><span data-ttu-id="77681-103">Ereditarietà</span><span class="sxs-lookup"><span data-stu-id="77681-103">Inheritance</span></span>

<span data-ttu-id="77681-104">EF è in grado di eseguire il mapping di una gerarchia di tipi .NET a un database.</span><span class="sxs-lookup"><span data-stu-id="77681-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="77681-105">In questo modo è possibile scrivere le entità .NET nel codice come di consueto, usando i tipi base e derivati e fare in modo che EF crei facilmente lo schema del database appropriato, emette query e così via. I dettagli effettivi su come viene eseguito il mapping di una gerarchia dei tipi sono dipendenti dal provider. in questa pagina viene descritto il supporto dell'ereditarietà nel contesto di un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="77681-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="77681-106">Al momento, EF Core supporta solo il modello tabella per gerarchia (TPH).</span><span class="sxs-lookup"><span data-stu-id="77681-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="77681-107">TPH usa una singola tabella per archiviare i dati per tutti i tipi nella gerarchia e viene usata una colonna discriminatore per identificare il tipo rappresentato da ogni riga.</span><span class="sxs-lookup"><span data-stu-id="77681-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="77681-108">La tabella per tipo (TPT) e la tabella per tipo concreto (TPC), supportati da EF6, non sono ancora supportate da EF Core.</span><span class="sxs-lookup"><span data-stu-id="77681-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="77681-109">TPT è una funzionalità principale progettata per EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="77681-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="77681-110">Mapping della gerarchia dei tipi di entità</span><span class="sxs-lookup"><span data-stu-id="77681-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="77681-111">Per convenzione, EF non analizzerà automaticamente i tipi di base o derivati; Ciò significa che se si desidera che venga eseguito il mapping di un tipo CLR nella gerarchia, è necessario specificare in modo esplicito quel tipo nel modello.</span><span class="sxs-lookup"><span data-stu-id="77681-111">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="77681-112">Se, ad esempio, si specifica solo il tipo di base di una gerarchia, EF Core includere in modo implicito tutti i relativi sottotipi.</span><span class="sxs-lookup"><span data-stu-id="77681-112">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="77681-113">Nell'esempio seguente viene esposto un DbSet per `Blog` e la relativa sottoclasse `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="77681-113">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="77681-114">Se `Blog` dispone di qualsiasi altra sottoclasse, non verrà inclusa nel modello.</span><span class="sxs-lookup"><span data-stu-id="77681-114">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="77681-115">Questo modello è mappato allo schema di database seguente (si noti la colonna *discriminatore* creata in modo implicito, che identifica il tipo di *Blog* archiviato in ogni riga):</span><span class="sxs-lookup"><span data-stu-id="77681-115">This model is mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="77681-117">Le colonne del database vengono rese automaticamente Nullable quando necessario quando si usa il mapping di TPH.</span><span class="sxs-lookup"><span data-stu-id="77681-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="77681-118">Ad esempio, la colonna *RssUrl* ammette i valori null perché le istanze di *Blog* normali non hanno tale proprietà.</span><span class="sxs-lookup"><span data-stu-id="77681-118">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="77681-119">Se non si vuole esporre un DbSet per una o più entità nella gerarchia, è anche possibile usare l'API Fluent per assicurarsi che siano incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="77681-119">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="77681-120">Se non si basano sulle convenzioni, è possibile specificare il tipo di base in modo esplicito utilizzando `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="77681-120">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="77681-121">È inoltre possibile utilizzare `.HasBaseType((Type)null)` per rimuovere un tipo di entità dalla gerarchia.</span><span class="sxs-lookup"><span data-stu-id="77681-121">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="77681-122">Configurazione discriminatore</span><span class="sxs-lookup"><span data-stu-id="77681-122">Discriminator configuration</span></span>

<span data-ttu-id="77681-123">È possibile configurare il nome e il tipo della colonna discriminatore e i valori utilizzati per identificare ogni tipo nella gerarchia:</span><span class="sxs-lookup"><span data-stu-id="77681-123">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="77681-124">Negli esempi precedenti, EF ha aggiunto il discriminatore in modo implicito come [proprietà shadow](xref:core/modeling/shadow-properties) nell'entità di base della gerarchia.</span><span class="sxs-lookup"><span data-stu-id="77681-124">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="77681-125">Questa proprietà può essere configurata come qualsiasi altra:</span><span class="sxs-lookup"><span data-stu-id="77681-125">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="77681-126">Infine, è anche possibile eseguire il mapping del discriminatore a una normale proprietà .NET nell'entità:</span><span class="sxs-lookup"><span data-stu-id="77681-126">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="77681-127">Colonne condivise</span><span class="sxs-lookup"><span data-stu-id="77681-127">Shared columns</span></span>

<span data-ttu-id="77681-128">Per impostazione predefinita, quando due tipi di entità di pari livello nella gerarchia hanno una proprietà con lo stesso nome, verrà eseguito il mapping a due colonne separate.</span><span class="sxs-lookup"><span data-stu-id="77681-128">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="77681-129">Tuttavia, se il tipo è identico, è possibile eseguirne il mapping alla stessa colonna di database:</span><span class="sxs-lookup"><span data-stu-id="77681-129">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
