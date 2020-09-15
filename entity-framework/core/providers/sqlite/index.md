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
# <a name="sqlite-ef-core-database-provider"></a>Provider di database SQLite per Entity Framework Core

Questo provider di database consente l'uso di Entity Framework Core (EF Core) con SQLite. Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

## <a name="install"></a>Installazione

Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a>Motori di database supportati

* SQLite (3.7 e versioni successive)

## <a name="limitations"></a>Limitazioni

Vedere [Limitazioni SQLite](xref:core/providers/sqlite/limitations) per alcune importanti limitazioni del provider di SQLite.
