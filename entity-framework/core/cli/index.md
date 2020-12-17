---
title: Informazioni di riferimento sugli strumenti di Entity Framework Core - EF Core
description: Guida di riferimento per lo strumento dell'interfaccia della riga di comando Entity Framework Core e la console di gestione pacchetti di Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635380"
---
# <a name="entity-framework-core-tools-reference"></a>Informazioni di riferimento sugli strumenti di Entity Framework Core

Gli strumenti di Entity Framework Core semplificano le attività di sviluppo in fase di progettazione. Vengono usati principalmente per gestire le migrazioni ed eseguire lo scaffolding di un elemento `DbContext` e dei tipi di entità decompilando lo schema di un database.

È possibile installare uno degli strumenti seguenti, poiché entrambi gli strumenti espongono la stessa funzionalità:

* Gli [strumenti della console di Gestione pacchetti di EF Core](xref:core/cli/powershell) vengono eseguiti nella [console di Gestione pacchetti](/nuget/tools/package-manager-console) in Visual Studio. Si consiglia di usare questi strumenti se si sviluppa in Visual Studio perché forniscono un'esperienza più integrata.

* Gli [strumenti dell'interfaccia della riga di comando di .NET EF Core](xref:core/cli/dotnet) sono un'estensione per gli [strumenti dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools/) multipiattaforma. Tali strumenti richiedono un progetto .NET Core SDK (uno con `Sdk="Microsoft.NET.Sdk"` o simili nel file di progetto).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sugli strumenti della console di Gestione pacchetti di EF Core](xref:core/cli/powershell)
* [Informazioni di riferimento sugli strumenti dell'interfaccia della riga di comando di .NET di EF Core](xref:core/cli/dotnet)
