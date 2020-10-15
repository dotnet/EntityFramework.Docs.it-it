---
title: Ereditarietà-EF Core
description: Come configurare l'ereditarietà del tipo di entità usando Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 47aae0d57d7203f0e6da5868bdc082ad85d59620
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063868"
---
# <a name="inheritance"></a><span data-ttu-id="f1621-103">Ereditarietà</span><span class="sxs-lookup"><span data-stu-id="f1621-103">Inheritance</span></span>

<span data-ttu-id="f1621-104">EF è in grado di eseguire il mapping di una gerarchia di tipi .NET a un database.</span><span class="sxs-lookup"><span data-stu-id="f1621-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="f1621-105">In questo modo è possibile scrivere le entità .NET nel codice come di consueto, usando i tipi base e derivati e fare in modo che EF crei facilmente lo schema del database appropriato, emette query e così via. I dettagli effettivi su come viene eseguito il mapping di una gerarchia dei tipi sono dipendenti dal provider. in questa pagina viene descritto il supporto dell'ereditarietà nel contesto di un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="f1621-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="f1621-106">Mapping della gerarchia dei tipi di entità</span><span class="sxs-lookup"><span data-stu-id="f1621-106">Entity type hierarchy mapping</span></span>

<span data-ttu-id="f1621-107">Per convenzione, EF non analizzerà automaticamente i tipi di base o derivati; Ciò significa che se si desidera che venga eseguito il mapping di un tipo CLR nella gerarchia, è necessario specificare in modo esplicito quel tipo nel modello.</span><span class="sxs-lookup"><span data-stu-id="f1621-107">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="f1621-108">Se, ad esempio, si specifica solo il tipo di base di una gerarchia, EF Core includere in modo implicito tutti i relativi sottotipi.</span><span class="sxs-lookup"><span data-stu-id="f1621-108">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="f1621-109">Nell'esempio seguente viene esposto un DbSet per `Blog` e la relativa sottoclasse `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="f1621-109">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="f1621-110">Se `Blog` dispone di qualsiasi altra sottoclasse, non verrà inclusa nel modello.</span><span class="sxs-lookup"><span data-stu-id="f1621-110">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="f1621-111">Le colonne del database vengono rese automaticamente Nullable quando necessario quando si usa il mapping di TPH.</span><span class="sxs-lookup"><span data-stu-id="f1621-111">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="f1621-112">Ad esempio, la `RssUrl` colonna ammette valori null perché le `Blog` istanze regolari non dispongono di tale proprietà.</span><span class="sxs-lookup"><span data-stu-id="f1621-112">For example, the `RssUrl` column is nullable because regular `Blog` instances do not have that property.</span></span>

<span data-ttu-id="f1621-113">Se non si vuole esporre un `DbSet` per una o più entità nella gerarchia, è anche possibile usare l'API Fluent per assicurarsi che siano incluse nel modello.</span><span class="sxs-lookup"><span data-stu-id="f1621-113">If you don't want to expose a `DbSet` for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="f1621-114">Se non si basano sulle convenzioni, è possibile specificare il tipo di base in modo esplicito utilizzando `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="f1621-114">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="f1621-115">È inoltre possibile utilizzare `.HasBaseType((Type)null)` per rimuovere un tipo di entità dalla gerarchia.</span><span class="sxs-lookup"><span data-stu-id="f1621-115">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="table-per-hierarchy-and-discriminator-configuration"></a><span data-ttu-id="f1621-116">Configurazione tabella per gerarchia e discriminatore</span><span class="sxs-lookup"><span data-stu-id="f1621-116">Table-per-hierarchy and discriminator configuration</span></span>

<span data-ttu-id="f1621-117">Per impostazione predefinita, EF esegue il mapping dell'ereditarietà usando il modello *tabella per gerarchia* (TPH).</span><span class="sxs-lookup"><span data-stu-id="f1621-117">By default, EF maps the inheritance using the *table-per-hierarchy* (TPH) pattern.</span></span> <span data-ttu-id="f1621-118">TPH usa una singola tabella per archiviare i dati per tutti i tipi nella gerarchia e viene usata una colonna discriminatore per identificare il tipo rappresentato da ogni riga.</span><span class="sxs-lookup"><span data-stu-id="f1621-118">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="f1621-119">Il modello precedente è mappato allo schema di database seguente (si noti la colonna creata in modo implicito `Discriminator` , che identifica il tipo di `Blog` Archiviato in ogni riga).</span><span class="sxs-lookup"><span data-stu-id="f1621-119">The model above is mapped to the following database schema (note the implicitly-created `Discriminator` column, which identifies which type of `Blog` is stored in each row).</span></span>

![image](_static/inheritance-tph-data.png)

<span data-ttu-id="f1621-121">È possibile configurare il nome e il tipo della colonna discriminatore e i valori utilizzati per identificare ogni tipo nella gerarchia:</span><span class="sxs-lookup"><span data-stu-id="f1621-121">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="f1621-122">Negli esempi precedenti, EF ha aggiunto il discriminatore in modo implicito come [proprietà shadow](xref:core/modeling/shadow-properties) nell'entità di base della gerarchia.</span><span class="sxs-lookup"><span data-stu-id="f1621-122">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="f1621-123">Questa proprietà può essere configurata come qualsiasi altra:</span><span class="sxs-lookup"><span data-stu-id="f1621-123">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="f1621-124">Infine, è anche possibile eseguire il mapping del discriminatore a una normale proprietà .NET nell'entità:</span><span class="sxs-lookup"><span data-stu-id="f1621-124">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

### <a name="shared-columns"></a><span data-ttu-id="f1621-125">Colonne condivise</span><span class="sxs-lookup"><span data-stu-id="f1621-125">Shared columns</span></span>

<span data-ttu-id="f1621-126">Per impostazione predefinita, quando due tipi di entità di pari livello nella gerarchia hanno una proprietà con lo stesso nome, verrà eseguito il mapping a due colonne separate.</span><span class="sxs-lookup"><span data-stu-id="f1621-126">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="f1621-127">Tuttavia, se il tipo è identico, è possibile eseguirne il mapping alla stessa colonna di database:</span><span class="sxs-lookup"><span data-stu-id="f1621-127">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a><span data-ttu-id="f1621-128">Configurazione tabella per tipo</span><span class="sxs-lookup"><span data-stu-id="f1621-128">Table-per-type configuration</span></span>

> [!NOTE]
> <span data-ttu-id="f1621-129">La tabella per tipo (TPT) è una nuova funzionalità di EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="f1621-129">The table-per-type (TPT) is a new feature in EF Core 5.0.</span></span> <span data-ttu-id="f1621-130">La tabella per tipo concreto (TPC) è supportata da EF6, ma non è ancora supportata da EF Core.</span><span class="sxs-lookup"><span data-stu-id="f1621-130">Table-per-concrete-type (TPC) is supported by EF6, but is not yet supported by EF Core.</span></span>

<span data-ttu-id="f1621-131">Nel modello di mapping di TPT, viene eseguito il mapping di tutti i tipi a singole tabelle.</span><span class="sxs-lookup"><span data-stu-id="f1621-131">In the TPT mapping pattern, all the types are mapped to individual tables.</span></span> <span data-ttu-id="f1621-132">Le proprietà che appartengono esclusivamente a un tipo di base o derivato sono archiviate in una tabella che viene mappata a quel tipo.</span><span class="sxs-lookup"><span data-stu-id="f1621-132">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="f1621-133">Le tabelle con mapping ai tipi derivati archiviano anche una chiave esterna che unisce la tabella derivata alla tabella di base.</span><span class="sxs-lookup"><span data-stu-id="f1621-133">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

<span data-ttu-id="f1621-134">EF creerà lo schema di database seguente per il modello precedente.</span><span class="sxs-lookup"><span data-stu-id="f1621-134">EF will create the following database schema for the model above.</span></span>

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> <span data-ttu-id="f1621-135">Se il vincolo PRIMARY KEY viene rinominato, il nuovo nome verrà applicato a tutte le tabelle di cui è stato eseguito il mapping alla gerarchia, le versioni future di EF consentiranno di rinominare il vincolo solo per una determinata tabella quando il [problema 19970](https://github.com/dotnet/efcore/issues/19970) è fisso.</span><span class="sxs-lookup"><span data-stu-id="f1621-135">If the primary key constraint is renamed the new name will be applied to all the tables mapped to the hierarchy, future EF versions will allow renaming the constraint only for a particular table when [issue 19970](https://github.com/dotnet/efcore/issues/19970) is fixed.</span></span>

<span data-ttu-id="f1621-136">Se si utilizza la configurazione bulk, è possibile recuperare il nome della colonna per una tabella specifica chiamando <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> .</span><span class="sxs-lookup"><span data-stu-id="f1621-136">If you are employing bulk configuration you can retrieve the column name for a specific table by calling <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
