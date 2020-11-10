---
title: Provider di database InMemory - EF Core
description: Informazioni sul provider di database Entity Framework Core InMemory
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430170"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="88a00-103">Provider di database InMemory per Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="88a00-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="88a00-104">Questo provider di database consente l'uso di Entity Framework Core con un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="88a00-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="88a00-105">Il database in memoria può essere utile per il testing, anche se il provider SQLite in modalità in memoria può essere una sostituzione dei test più appropriata per i database relazionali.</span><span class="sxs-lookup"><span data-stu-id="88a00-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="88a00-106">Il database in memoria è progettato solo per i test.</span><span class="sxs-lookup"><span data-stu-id="88a00-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="88a00-107">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="88a00-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="88a00-108">Installazione</span><span class="sxs-lookup"><span data-stu-id="88a00-108">Install</span></span>

<span data-ttu-id="88a00-109">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="88a00-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="88a00-110">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="88a00-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="88a00-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="88a00-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="88a00-112">Introduzione</span><span class="sxs-lookup"><span data-stu-id="88a00-112">Get Started</span></span>

<span data-ttu-id="88a00-113">Per acquisire familiarità con il provider, usare le risorse seguenti.</span><span class="sxs-lookup"><span data-stu-id="88a00-113">The following resources will help you get started with this provider.</span></span>

* <span data-ttu-id="88a00-114">[Testing with InMemory](xref:core/testing/in-memory) (Test con InMemory)</span><span class="sxs-lookup"><span data-stu-id="88a00-114">[Testing with InMemory](xref:core/testing/in-memory)</span></span>
* <span data-ttu-id="88a00-115">[UnicornStore Sample Application Tests](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs) (Test dell'applicazione di esempio UnicornStore)</span><span class="sxs-lookup"><span data-stu-id="88a00-115">[UnicornStore Sample Application Tests](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="88a00-116">Motori di database supportati</span><span class="sxs-lookup"><span data-stu-id="88a00-116">Supported Database Engines</span></span>

<span data-ttu-id="88a00-117">Database di memoria in-process, progettato solo a scopo di test.</span><span class="sxs-lookup"><span data-stu-id="88a00-117">In-process memory database, designed for testing purposes only.</span></span>
