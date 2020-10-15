---
title: Impostazione di valori espliciti per le proprietà generate - EF Core
description: Informazioni sull'impostazione esplicita dei valori per le proprietà configurate come generate con Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: b3a31d8139b244bec72347cf20600b6c2b65c7d2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062997"
---
# <a name="setting-explicit-values-for-generated-properties"></a>Impostazione di valori espliciti per le proprietà generate

Una proprietà generata è una proprietà il cui valore viene generato (tramite EF o il database) al momento dell'aggiunta e/o dell'aggiornamento dell'entità. Per altre informazioni, vedere [Generated Properties](xref:core/modeling/generated-properties) (Proprietà generate).

Potrebbero verificarsi situazioni in cui si vuole impostare un valore esplicito per una proprietà generata, anziché usarne uno generato.

> [!TIP]
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/ExplicitValuesGenerateProperties/) di questo articolo in GitHub.

## <a name="the-model"></a>Il modello

Il modello usato in questo articolo contiene una singola entità `Employee`.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Employee.cs#Sample)]

## <a name="saving-an-explicit-value-during-add"></a>Salvataggio di un valore esplicito durante l'aggiunta

La proprietà `Employee.EmploymentStarted` è configurata per contenere i valori generati dal database per le nuove entità (usando un valore predefinito).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#EmploymentStarted)]

Il codice seguente inserisce due dipendenti nel database.

* Per il primo, alla proprietà `Employee.EmploymentStarted` non viene assegnato alcun valore, in modo che rimanga impostata sul valore predefinito di CLR per `DateTime`.
* Per il secondo, viene impostato il valore esplicito `1-Jan-2000`.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmploymentStarted)]

L'output mostra che il database ha generato un valore per il primo dipendente e che è stato usato il valore esplicito per il secondo.

```output
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/1/2000 12:00:00 AM
```

### <a name="explicit-values-into-sql-server-identity-columns"></a>Valori espliciti nelle colonne IDENTITY di SQL Server

Per convenzione la proprietà `Employee.EmployeeId` è una colonna `IDENTITY` generata dall'archivio.

Nella maggior parte dei casi, l'approccio illustrato in precedenza funzionerà per le proprietà chiave. Tuttavia, per inserire valori espliciti in una colonna `IDENTITY` di SQL Server, è necessario abilitare manualmente `IDENTITY_INSERT` prima di chiamare `SaveChanges()`.

> [!NOTE]
> Esiste una [richiesta di funzionalità](https://github.com/aspnet/EntityFramework/issues/703) nel backlog per eseguire questa operazione automaticamente all'interno del provider SQL Server.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#EmployeeId)]

L'output mostra che gli ID specificati sono stati salvati nel database.

```output
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>Impostazione di un valore esplicito durante l'aggiornamento

La proprietà `Employee.LastPayRaise` è configurata per contenere i valori generati dal database durante gli aggiornamenti.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]
> Per impostazione predefinita, EF Core genererà un'eccezione se si tenta di salvare un valore esplicito per una proprietà che è configurata per la generazione dei valori durante l'aggiornamento. Per evitare questo problema, è necessario passare all'API dei metadati di livello inferiore e impostare `AfterSaveBehavior` (come illustrato in precedenza).

Esiste anche un trigger nel database per generare valori per la colonna `LastPayRaise` durante le operazioni `UPDATE`.

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

Il codice seguente consente di aumentare lo stipendio di due dipendenti nel database.

* Per il primo, alla proprietà `Employee.LastPayRaise` non viene assegnato alcun valore, in modo che rimanga impostata su Null.
* Per il secondo viene impostato il valore esplicito di una settimana fa (retrodatazione dell'aumento di stipendio).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

L'output mostra che il database ha generato un valore per il primo dipendente e che è stato usato il valore esplicito per il secondo.

```output
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
