---
title: Salvataggio asincrono - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: aa1fbfdc44895eace04354063435f98370aed2c4
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526914"
---
# <a name="asynchronous-saving"></a>Salvataggio asincrono

Il salvataggio asincrono consente di evitare il blocco di un thread mentre le modifiche vengono scritte nel database. Questo può essere utile per evitare di bloccare l'interfaccia utente di un'applicazione thick-client. Le operazioni asincrone possono anche aumentare la velocità effettiva in un'applicazione Web, in cui il thread può essere liberato per gestire altre richieste mentre viene completata l'operazione di database. Per altre informazioni, vedere [programmazione asincrona in C#](/dotnet/csharp/async).

> [!WARNING]  
> EF Core non supporta più operazioni parallele in esecuzione nella stessa istanza di contesto. È sempre necessario attendere il completamento di un'operazione prima di iniziare l'operazione successiva. Questa operazione viene in genere eseguita tramite la parola chiave `await` per ogni operazione asincrona.

Entity Framework Core offre `DbContext.SaveChangesAsync()` come alternativa asincrona a `DbContext.SaveChanges()`.

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
