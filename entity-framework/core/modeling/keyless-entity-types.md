---
title: Tipi di entità senza chiave - Entity Framework CoreKeyless Entity Types - EF Core
description: Come configurare i tipi di entità senza chiave utilizzando Entity Framework CoreHow to configure keyless entity types using Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434214"
---
# <a name="keyless-entity-types"></a>Tipi di entità senza chiave

> [!NOTE]
> Questa funzionalità è stata aggiunta in EF Core 2.1 con il nome dei tipi di query. In Entity Framework Core 3.0 il concetto è stato rinominato in tipi di entità senza chiave.

Oltre ai tipi di entità regolari, un modello di Entity Framework Core può contenere tipi di _entità senza chiave_, che possono essere utilizzati per eseguire query di database su dati che non contengono valori di chiave.

## <a name="defining-keyless-entity-types"></a>Definizione di tipi di entità senza chiaveDefining Keyless entity types

I tipi di entità senza chiave possono essere definiti usando l'annotazione dei dati o l'API Fluent:Keyless entity types can be defined using either the Data Annotation or the Fluent API:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Caratteristiche dei tipi di entità senza chiaveKeyless entity types characteristics

I tipi di entità senza chiave supportano molte delle stesse funzionalità di mapping dei tipi di entità regolari, ad esempio il mapping di ereditarietà e le proprietà di navigazione. Negli archivi relazionali possono configurare le colonne e gli oggetti di database di destinazione tramite metodi API fluenti o annotazioni dei dati.

Tuttavia, sono diversi dai tipi di entità regolari in quanto:However, they are different from regular entity types in that they:

- Non può avere una chiave definita.
- Non vengono mai rilevati per le modifiche in _DbContext_ e pertanto non vengono mai inseriti, aggiornati o eliminati nel database.
- Non vengono mai scoperti per convenzione.
- Supporta solo un sottoinsieme delle funzionalità di mapping di navigazione, in particolare:Only support a subset of navigation mapping capabilities, specifically:
  - Non possono mai agire come la fine principale di una relazione.
  - Potrebbero non avere spostamenti verso entità di proprietà
  - Possono contenere solo proprietà di navigazione di riferimento che puntano a entità regolari.
  - Le entità non possono contenere proprietà di navigazione per i tipi di entità senza chiave.
- È necessario essere `[Keyless]` configurati con `.HasNoKey()` un'annotazione dati o una chiamata al metodo.
- Può essere mappato a una query di _definizione._ Una query di definizione è una query dichiarata nel modello che funge da origine dati per un tipo di entità senza chiave.

## <a name="usage-scenarios"></a>Scenari di utilizzo

Alcuni degli scenari di utilizzo principali per i tipi di entità senza chiave sono:Some of the main usage scenarios for keyless entity types are:

- Funge da tipo restituito per [le query SQL non elaborate.](xref:core/querying/raw-sql)
- Mapping a viste di database che non contengono una chiave primaria.
- Mapping a tabelle per le cui tabelle non è stata definita una chiave primaria.
- Mapping alle query definite nel modello.

## <a name="mapping-to-database-objects"></a>Mapping a oggetti di database

Il mapping di un tipo di entità `ToTable` senza `ToView` chiave a un oggetto di database viene ottenuto utilizzando l'API o fluent. Dal punto di vista di EF Core, l'oggetto di database specificato in questo metodo è una _visualizzazione_, ovvero viene considerato come un'origine di query di sola lettura e non può essere la destinazione delle operazioni di aggiornamento, inserimento o eliminazione. Tuttavia, ciò non significa che l'oggetto di database è effettivamente necessario per essere una vista di database. In alternativa può essere una tabella di database che verrà considerata di sola lettura. Al contrario, per i tipi di entità regolari, Entity `ToTable` Framework Core presuppone che un oggetto di database specificato nel metodo può essere considerato come una _tabella_, ovvero può essere utilizzato come origine di query, ma anche destinato da operazioni di aggiornamento, eliminazione e inserimento. In effetti, è possibile specificare il `ToTable` nome di una vista di database in e tutto dovrebbe funzionare correttamente finché la vista è configurata per essere aggiornabile sul database.

> [!NOTE]
> `ToView`presuppone che l'oggetto esista già nel database e che non verrà creato dalle migrazioni.

## <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato come utilizzare i tipi di entità senza chiave per eseguire una query su una vista di database.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) di questo articolo in GitHub.

In primo luogo, definiamo un semplice modello di Blog e Post:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

Successivamente, definiamo una semplice vista di database che ci permetterà di interrogare il numero di post associati a ogni blog:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

Successivamente, definiamo una classe per contenere il risultato dalla vista del database:Next, we define a class to hold the result from the database view:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

Successivamente, si configura il tipo di entità `HasNoKey` senza chiave in _OnModelCreating_ usando l'API.
Usiamo l'API di configurazione fluente per configurare il mapping per il tipo di entità keyless:We use fluent configuration API to configure the mapping for the keyless entity type:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

Successivamente, configuriamo `DbContext` il `DbSet<T>`per includere il :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Infine, è possibile eseguire una query sulla vista del database nel modo standard:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Si noti che è stata definita anche una proprietà di query a livello di contesto (DbSet) che funge da radice per le query su questo tipo.
