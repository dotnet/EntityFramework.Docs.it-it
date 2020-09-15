---
title: Provider di database SQLite - EF Core
description: Informazioni sul provider di database SQLite per Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616555"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="f210a-103">Provider di database SQLite per Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f210a-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="f210a-104">Questo provider di database consente l'uso di Entity Framework Core (EF Core) con SQLite.</span><span class="sxs-lookup"><span data-stu-id="f210a-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="f210a-105">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="f210a-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="f210a-106">Installazione</span><span class="sxs-lookup"><span data-stu-id="f210a-106">Install</span></span>

<span data-ttu-id="f210a-107">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="f210a-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f210a-108">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="f210a-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="f210a-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f210a-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="f210a-110">Motori di database supportati</span><span class="sxs-lookup"><span data-stu-id="f210a-110">Supported Database Engines</span></span>

* <span data-ttu-id="f210a-111">SQLite (3.7 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="f210a-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="f210a-112">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="f210a-112">Limitations</span></span>

<span data-ttu-id="f210a-113">Vedere [Limitazioni SQLite](xref:core/providers/sqlite/limitations) per alcune importanti limitazioni del provider di SQLite.</span><span class="sxs-lookup"><span data-stu-id="f210a-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
