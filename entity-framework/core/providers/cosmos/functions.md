---
title: Mapping di funzioni-provider Azure Cosmos DB-EF Core
description: Mapping di funzioni del provider di EF Core Azure Cosmos DB
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543588"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Mapping di funzioni del provider di EF Core Azure Cosmos DB

Questa pagina mostra i membri .NET che vengono tradotti in funzioni SQL quando si usa il provider di Azure Cosmos DB.

.NET                          | SQL                              | Aggiunta in
----------------------------- | -------------------------------- | --------
raccolta. Contains (item)     | @item IN @collection
EF. Functions. Random ()         | RAND ()                           | EF Core 6.0
stringValue. Contains (valore)   | CONTAINs ( @stringValue , @value )   | EF Core 5.0
stringValue. EndsWith (valore)   | ENDSWITH ( @stringValue , @value )   | EF Core 5.0
stringValue. FirstOrDefault ()  | LEFT ( @stringValue ,1)            | EF Core 5.0
stringValue. LastOrDefault ()   | RIGHT ( @stringValue ,1)           | EF Core 5.0
stringValue. StartsWith (valore) | STARTSWITH ( @stringValue , @value ) | EF Core 5.0
