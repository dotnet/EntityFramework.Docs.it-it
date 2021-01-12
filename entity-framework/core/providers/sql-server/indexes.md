---
title: Provider di database Microsoft SQL Server-indici-EF Core
description: Funzionalità di indice specifiche del provider di SQL Server Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129161"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a>Funzionalità di indice specifiche del provider di SQL Server Entity Framework Core

Questa pagina descrive le opzioni di configurazione dell'indice specifiche per il provider di SQL Server.

## <a name="clustering"></a>Clustering

Gli indici cluster ordinano e archiviano le righe di dati della tabella in base ai valori di chiave, La creazione dell'indice cluster corretto per la tabella può migliorare significativamente la velocità delle query, poiché i dati sono già disposti nell'ordine ottimale. Per ogni tabella è disponibile un solo indice cluster, poiché alle righe di dati è possibile applicare un solo tipo di ordinamento. Per ulteriori informazioni, vedere [la documentazione di SQL Server sugli indici cluster e non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).

Per impostazione predefinita, la colonna chiave primaria di una tabella viene supportata implicitamente da un indice cluster e tutti gli altri indici non sono cluster.

È possibile configurare un indice o una chiave da raggruppare come segue:

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> SQL Server supporta solo un indice cluster per ogni tabella e la chiave primaria è per impostazione predefinita in cluster. Se si desidera disporre di un indice cluster in una colonna non chiave, è necessario impostare in modo esplicito la chiave non in cluster.

## <a name="fill-factor"></a>Fattore di riempimento

> [!NOTE]
> Questa funzionalità è stata introdotta in EF Core 5,0.

Per ottimizzare le prestazioni e l'archiviazione dei dati dell'indice, viene fornita l'opzione del fattore di riempimento degli indici. Per ulteriori informazioni, vedere [la documentazione SQL Server sul fattore di riempimento](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).

È possibile configurare il fattore di riempimento di un indice come segue:

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a>Creazione in linea

L'opzione ONLINE consente l'accesso simultaneo degli utenti alla tabella sottostante o ai dati dell'indice cluster e a tutti gli indici non cluster associati durante la creazione dell'indice, in modo che gli utenti possano continuare ad aggiornare ed eseguire query sui dati sottostanti. Quando si eseguono operazioni DDL (Data Definition Language) offline, ad esempio la compilazione o la ricompilazione di un indice cluster, tali operazioni mantengono blocchi esclusivi sui dati sottostanti e gli indici associati. Per ulteriori informazioni, vedere [la documentazione di SQL Server sull'opzione per gli indici online](/sql/relational-databases/indexes/perform-index-operations-online).

È possibile configurare un indice con l'opzione ONLINE come indicato di seguito:

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
