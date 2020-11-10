---
title: Operazioni di migrazione personalizzate-EF Core
description: Gestione di migrazioni SQL personalizzate e non elaborate per la gestione dello schema del database con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429832"
---
# <a name="custom-migrations-operations"></a>Operazioni di migrazione personalizzate

L'API MigrationBuilder consente di eseguire molti tipi diversi di operazioni durante una migrazione, ma è molto più esaustiva. Tuttavia, l'API è anche estendibile e consente di definire le proprie operazioni. Esistono due modi per estendere l'API: usando il `Sql()` metodo o definendo `MigrationOperation` oggetti personalizzati.

Per illustrare l'implementazione di un'operazione che consente di creare un utente del database utilizzando ogni approccio. Nelle migrazioni è necessario abilitare la scrittura del codice seguente:

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Utilizzo di MigrationBuilder. SQL ()

Il modo più semplice per implementare un'operazione personalizzata consiste nel definire un metodo di estensione che chiama `MigrationBuilder.Sql()` . Di seguito è riportato un esempio che genera l'oggetto Transact-SQL appropriato.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> Utilizzare la `EXEC` funzione quando un'istruzione deve essere la prima o una sola in un batch SQL. Potrebbe anche essere necessario aggirare gli errori del parser negli script di migrazione idempotente che possono verificarsi quando le colonne a cui si fa riferimento non sono attualmente presenti in una tabella.

Se le migrazioni devono supportare più provider di database, è possibile usare la `MigrationBuilder.ActiveProvider` Proprietà. Di seguito è riportato un esempio che supporta sia Microsoft SQL Server che PostgreSQL.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

Questo approccio funziona solo se si conoscono tutti i provider in cui verrà applicata l'operazione personalizzata.

## <a name="using-a-migrationoperation"></a>Uso di un MigrationOperation

Per separare l'operazione personalizzata da SQL, è possibile definirne una personalizzata `MigrationOperation` per rappresentarla. L'operazione viene quindi passata al provider in modo da poter determinare il SQL appropriato da generare.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

Con questo approccio, il metodo di estensione deve semplicemente aggiungere una di queste operazioni a `MigrationBuilder.Operations` .

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

Questo approccio richiede che ogni provider sappia come generare SQL per questa operazione nel `IMigrationsSqlGenerator` servizio. Di seguito è riportato un esempio che esegue l'override del generatore del SQL Server per gestire la nuova operazione.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

Sostituire il servizio Generatore SQL per migrazioni predefinite con quello aggiornato.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
