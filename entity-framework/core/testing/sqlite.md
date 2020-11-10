---
title: Test con SQLite-EF Core
description: Uso di SQLite per testare un'applicazione Entity Framework Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: ebfcd36bf236cb83cab8683a8c31d4752d437998
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431612"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a>Uso di SQLite per testare un'applicazione EF Core

> [!WARNING]
> L'uso di SQLite può essere un modo efficace per testare un'applicazione EF Core.
> Tuttavia, i problemi possono verificarsi quando SQLite si comporta in modo diverso rispetto ad altri sistemi di database.
> Vedere [test del codice che usa EF Core](xref:core/testing/index) per una descrizione dei problemi e dei compromessi.  

Questo documento si basa sui concetti introdotti in [esempio che illustrano come testare le applicazioni che usano EF Core](xref:core/testing/testing-sample).
Gli esempi di codice riportati di seguito provengono da questo esempio.

## <a name="using-sqlite-in-memory-databases"></a>Uso di database in memoria SQLite

In genere, SQLite crea database come file semplici e accede al file in-process con l'applicazione.
Questa operazione è molto rapida, soprattutto quando si usa un' [unità SSD](https://en.wikipedia.org/wiki/Solid-state_drive)veloce.

SQLite può anche usare i database creati esclusivamente in memoria.
Questa operazione è facile da usare con EF Core purché si conosca la durata del database in memoria:

* Il database viene creato al momento dell'apertura della connessione.
* Il database viene eliminato quando viene chiusa la connessione a essa.

EF Core utilizzerà una connessione già aperta, quando ne viene specificata una e non tenterà mai di chiuderla.
Quindi, la chiave per l'uso di EF Core con un database SQLite in memoria consiste nell'aprire la connessione prima di passarla a EF.  

L' [esempio](xref:core/testing/testing-sample) ottiene questo risultato con il codice seguente:

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

Notare:

* Il `CreateInMemoryDatabase` metodo crea un database in memoria SQLite e ne apre la connessione.
* L'oggetto creato `DbConnection` viene Estratto da `ContextOptions` e salvato.
* La connessione viene eliminata quando il test viene eliminato in modo da evitare perdite di risorse.

> [!NOTE]
> Il [problema #16103](https://github.com/dotnet/efcore/issues/16103) è tenere traccia dei modi per semplificare la gestione della connessione.
