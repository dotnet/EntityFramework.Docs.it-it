---
title: Creazione e configurazione di un modello - EF Core
description: Panoramica della creazione e configurazione di un modello con Entity Framework Core
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: b18db0059efd335abe2fc44bbc78e0106717e058
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429611"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="3795c-103">Creazione e configurazione di un modello</span><span class="sxs-lookup"><span data-stu-id="3795c-103">Creating and configuring a model</span></span>

<span data-ttu-id="3795c-104">Entity Framework usa un insieme di convenzioni per compilare un modello in base alla forma delle classi di entità.</span><span class="sxs-lookup"><span data-stu-id="3795c-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="3795c-105">È possibile specificare una configurazione aggiuntiva per integrare e/o sostituire gli elementi individuati dalla convenzione.</span><span class="sxs-lookup"><span data-stu-id="3795c-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="3795c-106">Questo articolo descrive la configurazione che può essere applicata a un modello destinato a qualsiasi archivio dati e che può essere applicata per qualsiasi database relazionale.</span><span class="sxs-lookup"><span data-stu-id="3795c-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="3795c-107">I provider possono anche abilitare una configurazione specifica di un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="3795c-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="3795c-108">Per informazioni sulla configurazione specifica del provider, vedere la sezione [Provider di database](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="3795c-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="3795c-109">È possibile visualizzare l' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="3795c-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="3795c-110">Usare l'API Fluent per configurare un modello</span><span class="sxs-lookup"><span data-stu-id="3795c-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="3795c-111">È possibile eseguire l'override del metodo `OnModelCreating` nel contesto derivato e usare `ModelBuilder API` per configurare il modello.</span><span class="sxs-lookup"><span data-stu-id="3795c-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="3795c-112">Questo è il metodo di configurazione più efficace e consente di specificare la configurazione senza modificare le classi di entità.</span><span class="sxs-lookup"><span data-stu-id="3795c-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="3795c-113">La configurazione dell'API Fluent ha la precedenza più elevata e sostituisce le convenzioni e le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="3795c-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a><span data-ttu-id="3795c-114">Raggruppamento di configurazione</span><span class="sxs-lookup"><span data-stu-id="3795c-114">Grouping configuration</span></span>

<span data-ttu-id="3795c-115">Per ridurre le dimensioni del <xref:System.Data.Entity.DbContext.OnModelCreating%2A> metodo, è possibile estrarre tutte le configurazioni per un tipo di entità in una classe separata che implementa <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> .</span><span class="sxs-lookup"><span data-stu-id="3795c-115">To reduce the size of the <xref:System.Data.Entity.DbContext.OnModelCreating%2A> method all configuration for an entity type can be extracted to a separate class implementing <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

<span data-ttu-id="3795c-116">Richiamare quindi semplicemente il `Configure` metodo da `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="3795c-116">Then just invoke the `Configure` method from `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

<span data-ttu-id="3795c-117">È possibile applicare tutte le configurazioni specificate nei tipi che implementano `IEntityTypeConfiguration` in un determinato assembly.</span><span class="sxs-lookup"><span data-stu-id="3795c-117">It is possible to apply all configuration specified in types implementing `IEntityTypeConfiguration` in a given assembly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> <span data-ttu-id="3795c-118">L'ordine in cui verranno applicate le configurazioni non è definito, pertanto questo metodo deve essere usato solo quando l'ordine non è rilevante.</span><span class="sxs-lookup"><span data-stu-id="3795c-118">The order in which the configurations will be applied is undefined, therefore this method should only be used when the order doesn't matter.</span></span>

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="3795c-119">Usare le annotazioni dei dati per configurare un modello</span><span class="sxs-lookup"><span data-stu-id="3795c-119">Use data annotations to configure a model</span></span>

<span data-ttu-id="3795c-120">È anche possibile applicare attributi (chiamati annotazioni dei dati) alle classi e alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="3795c-120">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="3795c-121">Le annotazioni dei dati sostituiranno le convenzioni, ma saranno a loro volta ignorate dalla configurazione dell'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="3795c-121">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
