---
title: Provider di database Microsoft SQL Server - EF Core
description: Documentazione per il provider di database che consente l'uso di Entity Framework Core con Microsoft SQL Server
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 4118cd5737ece1ad084bb85d409523d217065353
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430324"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="41a67-103">Provider di database Microsoft SQL Server per EF Core</span><span class="sxs-lookup"><span data-stu-id="41a67-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="41a67-104">Questo provider di database consente l'uso di Entity Framework Core con Microsoft SQL Server (incluso il database SQL di Azure).</span><span class="sxs-lookup"><span data-stu-id="41a67-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="41a67-105">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="41a67-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="41a67-106">Installazione</span><span class="sxs-lookup"><span data-stu-id="41a67-106">Install</span></span>

<span data-ttu-id="41a67-107">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="41a67-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="41a67-108">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="41a67-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="41a67-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41a67-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="41a67-110">A partire dalla versione 3.0.0, il provider fa riferimento a Microsoft.Data.SqlClient (le versioni precedenti dipendono da System.Data.SqlClient).</span><span class="sxs-lookup"><span data-stu-id="41a67-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="41a67-111">Se il progetto assume una dipendenza diretta da SqlClient, assicurarsi che faccia riferimento al pacchetto Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="41a67-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="41a67-112">Motori di database supportati</span><span class="sxs-lookup"><span data-stu-id="41a67-112">Supported Database Engines</span></span>

* <span data-ttu-id="41a67-113">Microsoft SQL Server (2012 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="41a67-113">Microsoft SQL Server (2012 onwards)</span></span>
