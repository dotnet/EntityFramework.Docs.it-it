---
title: Creazione e configurazione di un modello - EF Core
description: Panoramica della creazione e configurazione di un modello con Entity Framework Core
author: rowanmiller
ms.date: 11/05/2019
ms.assetid: 88253ff3-174e-485c-b3f8-768243d01ee1
uid: core/modeling/index
ms.openlocfilehash: e980f11b08bee7b07156a80c6bead829e7a8b654
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616752"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="ce5a4-103">Creazione e configurazione di un modello</span><span class="sxs-lookup"><span data-stu-id="ce5a4-103">Creating and configuring a model</span></span>

<span data-ttu-id="ce5a4-104">Entity Framework usa un insieme di convenzioni per compilare un modello in base alla forma delle classi di entità.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="ce5a4-105">È possibile specificare una configurazione aggiuntiva per integrare e/o sostituire gli elementi individuati dalla convenzione.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="ce5a4-106">Questo articolo descrive la configurazione che può essere applicata a un modello destinato a qualsiasi archivio dati e che può essere applicata per qualsiasi database relazionale.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="ce5a4-107">I provider possono anche abilitare una configurazione specifica di un archivio dati.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="ce5a4-108">Per informazioni sulla configurazione specifica del provider, vedere la sezione  [Provider di database](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="ce5a4-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="ce5a4-109">È possibile visualizzare l' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)  di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="ce5a4-110">Usare l'API Fluent per configurare un modello</span><span class="sxs-lookup"><span data-stu-id="ce5a4-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="ce5a4-111">È possibile eseguire l'override del metodo  `OnModelCreating`  nel contesto derivato e usare  `ModelBuilder API` per configurare il modello.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="ce5a4-112">Questo è il metodo di configurazione più efficace e consente di specificare la configurazione senza modificare le classi di entità.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="ce5a4-113">La configurazione dell'API Fluent ha la precedenza più elevata e sostituisce le convenzioni e le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="ce5a4-114">Usare le annotazioni dei dati per configurare un modello</span><span class="sxs-lookup"><span data-stu-id="ce5a4-114">Use data annotations to configure a model</span></span>

<span data-ttu-id="ce5a4-115">È anche possibile applicare attributi (chiamati annotazioni dei dati) alle classi e alle proprietà.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="ce5a4-116">Le annotazioni dei dati sostituiranno le convenzioni, ma saranno a loro volta ignorate dalla configurazione dell'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="ce5a4-116">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
