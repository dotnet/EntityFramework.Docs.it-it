---
title: Mapping di funzioni-provider Azure Cosmos DB-EF Core
description: Mapping di funzioni del provider di EF Core Azure Cosmos DB
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066608"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Mapping di funzioni del provider di EF Core Azure Cosmos DB

Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider di Azure Cosmos DB.

.NET                          | SQL                              | Aggiunta in
----------------------------- | -------------------------------- | --------
raccolta. Contains (item)     | @item IN @collection
stringValue. Contains (valore)   | CONTAINs ( @stringValue , @value )   | EF Core 5.0
stringValue. EndsWith (valore)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
stringValue. FirstOrDefault ()  | LEFT ( @stringValue ,1)            | EF Core 5.0
stringValue. LastOrDefault ()   | RIGHT ( @stringValue ,1)           | EF Core 5.0
stringValue. StartsWith (valore) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
