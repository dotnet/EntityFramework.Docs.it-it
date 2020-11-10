---
title: Tipi di entità autofirmati-EF Core
description: Come configurare i tipi di entità autochiave utilizzando Entity Framework Core
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: d1a60e0504b22623b97c1a4963d2e3f70faa365c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429507"
---
# <a name="keyless-entity-types"></a>Tipi di entità senza chiave

> [!NOTE]
> Questa funzionalità è stata aggiunta con il nome dei tipi di query. In EF Core 3,0 il concetto è stato rinominato in tipi di entità senza chiave. L' `[Keyless]` annotazione dei dati è diventata disponibile in EFCore 5,0.

Oltre ai normali tipi di entità, un modello di EF Core può contenere _tipi di entità_ senza chiave, che possono essere usati per eseguire query di database su dati che non contengono valori di chiave.

## <a name="defining-keyless-entity-types"></a>Definizione di tipi di entità autochiave

È possibile definire i tipi di entità con chiave fissa usando l'annotazione dei dati o l'API Fluent:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Caratteristiche di tipi di entità autochiave

I tipi di entità autonome supportano molte delle stesse funzionalità di mapping dei normali tipi di entità, ad esempio il mapping di ereditarietà e le proprietà di navigazione. Negli archivi relazionali è possibile configurare gli oggetti e le colonne di database di destinazione tramite metodi API Fluent o annotazioni di dati.

Tuttavia, sono diversi dai normali tipi di entità in quanto:

- Non è possibile definire una chiave.
- Non vengono mai rilevate per le modifiche apportate in _DbContext_ e pertanto non vengono mai inserite, aggiornate o eliminate nel database.
- Non vengono mai individuati per convenzione.
- Supporta solo un subset di funzionalità di mapping di navigazione, in particolare:
  - Non possono mai fungere da entità finale principale di una relazione.
  - Potrebbero non avere spostamenti sulle entità di proprietà
  - Possono contenere solo proprietà di navigazione di riferimento che puntano a entità regolari.
  - Le entità non possono contenere proprietà di navigazione per i tipi di entità autochiave.
- Deve essere configurato con un' `[Keyless]` annotazione dati o una `.HasNoKey()` chiamata al metodo.
- Può essere mappato a una _query di definizione_. Una query di definizione è una query dichiarata nel modello che funge da origine dati per un tipo di entità autonome.

## <a name="usage-scenarios"></a>Scenari di utilizzo

Di seguito sono riportati alcuni degli scenari di utilizzo principali per i tipi di entità autochiave:

- Fungendo da tipo restituito per le [query SQL non elaborate](xref:core/querying/raw-sql).
- Mapping a viste di database che non contengono una chiave primaria.
- Mapping a tabelle in cui non è definita una chiave primaria.
- Mapping a query definite nel modello.

## <a name="mapping-to-database-objects"></a>Mapping a oggetti di database

Il mapping di un tipo di entità autochiave a un oggetto di database viene eseguito tramite l' `ToTable` `ToView` API o Fluent. Dal punto di vista del EF Core, l'oggetto di database specificato in questo metodo è una _vista_ , ovvero viene considerato come un'origine di query di sola lettura e non può essere la destinazione di operazioni di aggiornamento, inserimento o eliminazione. Tuttavia, ciò non significa che l'oggetto di database debba essere effettivamente una vista di database. In alternativa, può essere una tabella di database che verrà considerata di sola lettura. Viceversa, per i tipi di entità regolari, EF Core presuppone che un oggetto di database specificato nel `ToTable` metodo possa essere considerato come una _tabella_ , ovvero può essere utilizzato come origine della query, ma anche come destinazione da operazioni di aggiornamento, eliminazione e inserimento. In realtà, è possibile specificare il nome di una vista di database in `ToTable` e tutto dovrebbe funzionare correttamente purché la vista sia configurata per essere aggiornabile nel database.

> [!NOTE]
> `ToView` presuppone che l'oggetto sia già presente nel database e non venga creato dalle migrazioni.

## <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come utilizzare i tipi di entità autochiave per eseguire una query su una vista di database.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) di questo articolo in GitHub.

In primo luogo, viene definito un semplice modello di Blog e post:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

Viene quindi definita una semplice vista di database che consente di eseguire una query sul numero di post associati a ogni Blog:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

Viene quindi definita una classe che conterrà il risultato della vista di database:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

Successivamente, si configura il tipo di entità autochiave in _OnModelCreating_ usando l' `HasNoKey` API.
Si usa l'API di configurazione Fluent per configurare il mapping per il tipo di entità autochiave:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

Successivamente, viene configurato `DbContext` per includere `DbSet<T>` :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Infine, è possibile eseguire una query sulla vista di database nel modo standard:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Si noti che è stata definita anche una proprietà di query a livello di contesto (DbSet) che funge da radice per le query su questo tipo.

> [!TIP]
> Per testare i tipi di entità senza chiave mappati alle viste usando il provider in memoria, eseguirne il mapping a una query tramite `ToInMemoryQuery` . Per altri dettagli, vedere un [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) usando questa tecnica.
