---
title: Creare ed eliminare API-EF Core
description: API per la creazione e l'eliminazione di database con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 25e7352269531e881e83e44ea90108f12d4dcbea
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619220"
---
# <a name="create-and-drop-apis"></a>API di creazione ed eliminazione

I metodi EnsureCreated e EnsureDeleted forniscono un'alternativa semplice alle [migrazioni](xref:core/managing-schemas/migrations/index) per la gestione dello schema del database. Questi metodi sono utili negli scenari in cui i dati sono temporanei e possono essere eliminati quando lo schema viene modificato. Ad esempio, durante la fase di prototipo, nei test o per le cache locali.

Alcuni provider (soprattutto quelli non relazionali) non supportano le migrazioni. Per questi provider, EnsureCreated è spesso il modo più semplice per inizializzare lo schema del database.

> [!WARNING]
> EnsureCreated e le migrazioni non funzionano bene insieme. Se si usano le migrazioni, non usare EnsureCreated per inizializzare lo schema.

La transizione da EnsureCreated a migrazioni non è un'esperienza senza problemi. Il modo più semplice per eseguire questa operazione consiste nell'eliminare il database e ricrearlo usando le migrazioni. Se si prevede di usare le migrazioni in futuro, è preferibile iniziare con le migrazioni invece di usare EnsureCreated.

## <a name="ensuredeleted"></a>EnsureDeleted

Se esistente, il metodo EnsureDeleted eliminerà il database. Se non si dispone delle autorizzazioni appropriate, viene generata un'eccezione.

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

EnsureCreated creerà il database se non esiste e inizializza lo schema del database. Se sono presenti tabelle (incluse le tabelle per un'altra classe DbContext), lo schema non verrà inizializzato.

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> Sono disponibili anche le versioni asincrone di questi metodi.

## <a name="sql-script"></a>Script SQL

Per ottenere l'oggetto SQL usato da EnsureCreated, è possibile usare il Metodo GenerateCreateScript.

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>Più classi DbContext

EnsureCreated funziona solo quando nel database non è presente alcuna tabella. Se necessario, è possibile scrivere un controllo personalizzato per verificare se lo schema deve essere inizializzato e usare il servizio IRelationalDatabaseCreator sottostante per inizializzare lo schema.

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
