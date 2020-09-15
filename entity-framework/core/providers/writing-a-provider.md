---
title: Scrittura di un provider di database-EF Core
description: Informazioni sulla scrittura di un nuovo provider di Entity Framework Core
author: anmiller
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: b6054696711eb4bf865841428f58ca41791676f9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071212"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="56ab8-103">Scrittura di un provider di database</span><span class="sxs-lookup"><span data-stu-id="56ab8-103">Writing a Database Provider</span></span>

<span data-ttu-id="56ab8-104">Per informazioni sulla scrittura di un provider di database Entity Framework Core, vedere la pagina relativa alla scrittura di [un provider EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) da [Arthur Vickers](https://github.com/ajcvickers).</span><span class="sxs-lookup"><span data-stu-id="56ab8-104">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="56ab8-105">Questi post non sono stati aggiornati a partire da EF Core 1,1 e sono state apportate modifiche significative da quel momento.</span><span class="sxs-lookup"><span data-stu-id="56ab8-105">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="56ab8-106">Il [problema 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) sta monitorando gli aggiornamenti della documentazione.</span><span class="sxs-lookup"><span data-stu-id="56ab8-106">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="56ab8-107">Il EF Core codebase è open source e contiene diversi provider di database che possono essere utilizzati come riferimento.</span><span class="sxs-lookup"><span data-stu-id="56ab8-107">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="56ab8-108">Il codice sorgente è reperibile in <https://github.com/aspnet/EntityFrameworkCore> .</span><span class="sxs-lookup"><span data-stu-id="56ab8-108">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="56ab8-109">Potrebbe anche essere utile esaminare il codice per i provider di terze parti di uso comune, ad esempio [npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)e [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span><span class="sxs-lookup"><span data-stu-id="56ab8-109">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="56ab8-110">In particolare, questi progetti sono impostati per estendere ed eseguire test funzionali pubblicati in NuGet.</span><span class="sxs-lookup"><span data-stu-id="56ab8-110">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="56ab8-111">Questo tipo di installazione è fortemente consigliato.</span><span class="sxs-lookup"><span data-stu-id="56ab8-111">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="56ab8-112">Mantenersi aggiornati sulle modifiche del provider</span><span class="sxs-lookup"><span data-stu-id="56ab8-112">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="56ab8-113">A partire da lavoro dopo la versione 2,1, è stato creato un [log di modifiche](xref:core/providers/provider-log) che potrebbero richiedere modifiche corrispondenti nel codice del provider.</span><span class="sxs-lookup"><span data-stu-id="56ab8-113">Starting with work after the 2.1 release, we have created a [log of changes](xref:core/providers/provider-log) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="56ab8-114">Questa operazione è utile quando si aggiorna un provider esistente per l'utilizzo con una nuova versione di EF Core.</span><span class="sxs-lookup"><span data-stu-id="56ab8-114">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="56ab8-115">Nelle versioni precedenti alla 2,1 sono state usate le [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) etichette e nei problemi di GitHub e le richieste pull per uno scopo simile.</span><span class="sxs-lookup"><span data-stu-id="56ab8-115">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="56ab8-116">Si continiue di usare queste etichette nei problemi per indicare quali elementi di lavoro in una determinata versione potrebbero richiedere il lavoro da eseguire nei provider.</span><span class="sxs-lookup"><span data-stu-id="56ab8-116">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="56ab8-117">Un' `providers-beware` etichetta indica in genere che l'implementazione di un elemento di lavoro potrebbe interrompere i provider, mentre un' `providers-fyi` etichetta in genere significa che i provider non verranno interrotti, ma potrebbe essere necessario modificare il codice comunque, ad esempio, per abilitare la nuova funzionalità.</span><span class="sxs-lookup"><span data-stu-id="56ab8-117">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="56ab8-118">Denominazione consigliata per i provider di terze parti</span><span class="sxs-lookup"><span data-stu-id="56ab8-118">Suggested naming of third party providers</span></span>

<span data-ttu-id="56ab8-119">È consigliabile usare i nomi seguenti per i pacchetti NuGet.</span><span class="sxs-lookup"><span data-stu-id="56ab8-119">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="56ab8-120">Questo è coerente con i nomi dei pacchetti forniti dal team EF Core.</span><span class="sxs-lookup"><span data-stu-id="56ab8-120">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="56ab8-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="56ab8-121">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
