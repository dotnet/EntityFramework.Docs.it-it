---
title: Impostazione di valori espliciti per le proprietà generate - EF Core
description: Informazioni sull'impostazione esplicita dei valori per le proprietà configurate come generate con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3f1993c2-cdf5-425b-bac2-a2665a20322b
uid: core/saving/explicit-values-generated-properties
ms.openlocfilehash: 6a10e8d8c056ab66226f229dcb8b032ff83f06fe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617249"
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

``` Console
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

``` Console
100: John Doe
101: Jane Doe
```

## <a name="setting-an-explicit-value-during-update"></a>Impostazione di un valore esplicito durante l'aggiornamento

La proprietà `Employee.LastPayRaise` è configurata per contenere i valori generati dal database durante gli aggiornamenti.

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/EmployeeContext.cs#LastPayRaise)]

> [!NOTE]  
> Per impostazione predefinita, EF Core genererà un'eccezione se si tenta di salvare un valore esplicito per una proprietà che è configurata per la generazione dei valori durante l'aggiornamento. Per evitare questo problema, è necessario passare all'API dei metadati di livello inferiore e impostare `AfterSaveBehavior` (come illustrato in precedenza).

> [!NOTE]  
> **Modifiche in EF Core 2.0:** nelle versioni precedenti il comportamento post-salvataggio era controllato tramite il flag `IsReadOnlyAfterSave`. Questo flag è ora obsoleto ed è stato sostituito da `AfterSaveBehavior`.

Esiste anche un trigger nel database per generare valori per la colonna `LastPayRaise` durante le operazioni `UPDATE`.

[!code-sql[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/employee_UPDATE.sql)]

Il codice seguente consente di aumentare lo stipendio di due dipendenti nel database.

* Per il primo, alla proprietà `Employee.LastPayRaise` non viene assegnato alcun valore, in modo che rimanga impostata su Null.
* Per il secondo viene impostato il valore esplicito di una settimana fa (retrodatazione dell'aumento di stipendio).

[!code-csharp[Main](../../../samples/core/Saving/ExplicitValuesGenerateProperties/Sample.cs#LastPayRaise)]

L'output mostra che il database ha generato un valore per il primo dipendente e che è stato usato il valore esplicito per il secondo.

``` Console
1: John Doe, 1/26/2017 12:00:00 AM
2: Jane Doe, 1/19/2017 12:00:00 AM
```
