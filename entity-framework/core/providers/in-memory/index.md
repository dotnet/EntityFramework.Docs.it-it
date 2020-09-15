---
title: Provider di database InMemory - EF Core
description: Informazioni sul provider di database Entity Framework Core InMemory
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 9d0a5d14cdb047b80788fbe4d9d34deccdbd4ce1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071355"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="982f7-103">Provider di database InMemory per Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="982f7-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="982f7-104">Questo provider di database consente l'uso di Entity Framework Core con un database in memoria.</span><span class="sxs-lookup"><span data-stu-id="982f7-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="982f7-105">Ciò può essere utile per il testing, anche se il provider SQLite in modalità in memoria può essere un sostituto per i test più appropriato per i database relazionali.</span><span class="sxs-lookup"><span data-stu-id="982f7-105">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="982f7-106">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="982f7-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="982f7-107">Installazione</span><span class="sxs-lookup"><span data-stu-id="982f7-107">Install</span></span>

<span data-ttu-id="982f7-108">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="982f7-108">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="982f7-109">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="982f7-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="982f7-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="982f7-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="982f7-111">Introduzione</span><span class="sxs-lookup"><span data-stu-id="982f7-111">Get Started</span></span>

<span data-ttu-id="982f7-112">Per acquisire familiarità con il provider, usare le risorse seguenti.</span><span class="sxs-lookup"><span data-stu-id="982f7-112">The following resources will help you get started with this provider.</span></span>

* <span data-ttu-id="982f7-113">[Testing with InMemory](xref:core/miscellaneous/testing/in-memory) (Test con InMemory)</span><span class="sxs-lookup"><span data-stu-id="982f7-113">[Testing with InMemory](xref:core/miscellaneous/testing/in-memory)</span></span>
* <span data-ttu-id="982f7-114">[UnicornStore Sample Application Tests](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs) (Test dell'applicazione di esempio UnicornStore)</span><span class="sxs-lookup"><span data-stu-id="982f7-114">[UnicornStore Sample Application Tests](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="982f7-115">Motori di database supportati</span><span class="sxs-lookup"><span data-stu-id="982f7-115">Supported Database Engines</span></span>

<span data-ttu-id="982f7-116">Database con memoria In-Process (progettato solo a scopo di test)</span><span class="sxs-lookup"><span data-stu-id="982f7-116">In-process memory database (designed for testing purposes only)</span></span>
