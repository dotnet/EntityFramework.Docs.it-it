---
title: Token di concorrenza-EF Core
description: Configurazione dei token di concorrenza per il controllo della concorrenza ottimistica in un modello di Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/concurrency
ms.openlocfilehash: fab9ad99609cd8e724b550000f6f06ef8c172c81
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071615"
---
# <a name="concurrency-tokens"></a>Token di concorrenza

> [!NOTE]
> Questa pagina illustra come configurare i token di concorrenza. Vedere [gestione dei conflitti di concorrenza](xref:core/saving/concurrency) per una spiegazione dettagliata del funzionamento del controllo della concorrenza in EF core ed esempi di come gestire i conflitti di concorrenza nell'applicazione.

Le proprietà configurate come token di concorrenza vengono usate per implementare il controllo della concorrenza ottimistica.

## <a name="configuration"></a>Configurazione

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Un timestamp/rowversion è una proprietà per la quale un nuovo valore viene generato automaticamente dal database ogni volta che viene inserita o aggiornata una riga. La proprietà viene inoltre considerata come un token di concorrenza, assicurando che venga generata un'eccezione se una riga che si sta aggiornando è cambiata dopo la relativa query. I dettagli precisi dipendono dal provider di database in uso. per SQL Server, viene in genere usata una proprietà *byte []* , che verrà configurata come colonna *rowversion* nel database.

È possibile configurare una proprietà come timestamp/rowversion nel modo seguente:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
