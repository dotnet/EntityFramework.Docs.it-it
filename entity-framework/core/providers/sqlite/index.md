---
title: Provider di database SQLite - EF Core
description: Informazioni sul provider di database SQLite per Entity Framework Core
author: bricelam
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: 6dac555456ed347b3eb07bbd0f2a89ab9179d20e
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430300"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="af56a-103">Provider di database SQLite per Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="af56a-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="af56a-104">Questo provider di database consente l'uso di Entity Framework Core (EF Core) con SQLite.</span><span class="sxs-lookup"><span data-stu-id="af56a-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="af56a-105">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="af56a-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="af56a-106">Installazione</span><span class="sxs-lookup"><span data-stu-id="af56a-106">Install</span></span>

<span data-ttu-id="af56a-107">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="af56a-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="af56a-108">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="af56a-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="af56a-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af56a-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="af56a-110">Motori di database supportati</span><span class="sxs-lookup"><span data-stu-id="af56a-110">Supported Database Engines</span></span>

* <span data-ttu-id="af56a-111">SQLite (3.7 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="af56a-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="af56a-112">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="af56a-112">Limitations</span></span>

<span data-ttu-id="af56a-113">Vedere [Limitazioni SQLite](xref:core/providers/sqlite/limitations) per alcune importanti limitazioni del provider di SQLite.</span><span class="sxs-lookup"><span data-stu-id="af56a-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
