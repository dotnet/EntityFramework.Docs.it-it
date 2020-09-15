---
title: Creazione e configurazione di un modello - EF Core
description: Panoramica della creazione e configurazione di un modello con Entity Framework Core
author: rowanmiller
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 1380ed0c1af0fdd3ae24de311bea455cb57a7dcd
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071420"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="2a6a6-103">Creazione e configurazione di un modello</span><span class="sxs-lookup"><span data-stu-id="2a6a6-103">Creating and configuring a model</span></span>

<span data-ttu-id="2a6a6-104">Entity Framework usa un insieme di convenzioni per compilare un modello in base alla forma delle classi di entità.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="2a6a6-105">È possibile specificare una configurazione aggiuntiva per integrare e/o sostituire gli elementi individuati dalla convenzione.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="2a6a6-106">Questo articolo descrive la configurazione che può essere applicata a un modello destinato a qualsiasi archivio dati e che può essere applicata per qualsiasi database relazionale.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="2a6a6-107">I provider possono anche abilitare una configurazione specifica di un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="2a6a6-108">Per informazioni sulla configurazione specifica del provider, vedere la sezione  [Provider di database](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="2a6a6-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="2a6a6-109">È possibile visualizzare l' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)  di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="2a6a6-110">Usare l'API Fluent per configurare un modello</span><span class="sxs-lookup"><span data-stu-id="2a6a6-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="2a6a6-111">È possibile eseguire l'override del metodo  `OnModelCreating`  nel contesto derivato e usare  `ModelBuilder API` per configurare il modello.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="2a6a6-112">Questo è il metodo di configurazione più efficace e consente di specificare la configurazione senza modificare le classi di entità.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="2a6a6-113">La configurazione dell'API Fluent ha la precedenza più elevata e sostituisce le convenzioni e le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="2a6a6-114">Usare le annotazioni dei dati per configurare un modello</span><span class="sxs-lookup"><span data-stu-id="2a6a6-114">Use data annotations to configure a model</span></span>

<span data-ttu-id="2a6a6-115">È anche possibile applicare attributi (chiamati annotazioni dei dati) alle classi e alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="2a6a6-116">Le annotazioni dei dati sostituiranno le convenzioni, ma saranno a loro volta ignorate dalla configurazione dell'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="2a6a6-116">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
