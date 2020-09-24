---
title: Pool DbContext
description: DbContext pooling in Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215600"
---
# <a name="dbcontext-pooling"></a>Pooling DbContext

`AddDbContextPool` consente di raggruppare le `DbContext` istanze. Il pool di contesto può aumentare la velocità effettiva in scenari a scalabilità elevata, ad esempio i server Web riutilizzando le istanze di contesto, anziché creare nuove istanze per ogni richiesta.

Il modello tipico in un'app ASP.NET Core con EF Core prevede la registrazione di un <xref:Microsoft.EntityFrameworkCore.DbContext> tipo personalizzato nel contenitore di [inserimento delle dipendenze](/aspnet/core/fundamentals/dependency-injection) e l'ottenimento di istanze del tipo tramite i parametri del costruttore nei controller o Razor Pages. Con l'inserimento del costruttore viene creata una nuova istanza del contesto per ogni richiesta.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> Abilita un pool di istanze di contesto riutilizzabili. Per utilizzare il pool di contesto, utilizzare il `AddDbContextPool` metodo anziché `AddDbContext` durante la registrazione del servizio:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Quando `AddDbContextPool` si usa, nel momento in cui viene richiesta un'istanza del contesto, EF verifica innanzitutto se è disponibile un'istanza nel pool. Dopo che l'elaborazione della richiesta è stata finalizzata, lo stato dell'istanza viene reimpostato e quest'ultima viene restituita al pool.

Questa situazione è concettualmente simile al modo in cui il pool di connessioni opera nei provider ADO.NET e offre il vantaggio di risparmiare parte del costo di inizializzazione dell'istanza del contesto.

Il `poolSize` parametro di <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> imposta il numero massimo di istanze conservate dal pool. Una volta `poolSize` superato il limite, le nuove istanze del contesto non vengono memorizzate nella cache e EF esegue il fallback al comportamento non in pool della creazione di istanze su richiesta.

## <a name="limitations"></a>Limitazioni

Le app devono essere profilate e testate per mostrare che l'inizializzazione del contesto è un costo significativo.

`AddDbContextPool` presenta alcune limitazioni sulle operazioni che possono essere eseguite nel `OnConfiguring` metodo del contesto.

> [!WARNING]  
> Evitare di usare il pool di contesto nelle app che mantengono lo stato. Ad esempio, i campi privati nel contesto che non devono essere condivisi tra le richieste. EF Core reimposta solo lo stato di cui è consapevole prima di aggiungere un'istanza del contesto al pool.

Il pool di contesto funziona riutilizzando la stessa istanza del contesto tra le richieste. Ciò significa che viene registrato in modo efficace come [singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in termini di istanza stessa, in modo che sia in grado di renderlo persistente.

Il pool di contesto è destinato agli scenari in cui la configurazione del contesto, che include servizi risolti, viene fissata tra le richieste. Per i casi in cui i servizi con [ambito](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) sono obbligatori o è necessario modificare la configurazione, non usare il pool. Il miglioramento delle prestazioni del pool è in genere trascurabile tranne che negli scenari altamente ottimizzati.
