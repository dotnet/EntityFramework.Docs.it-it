---
title: Microsoft SQL Server provider di database-generazione valore-EF Core
description: Modelli di generazione di valori specifici del provider di database Entity Framework Core di SQL Server
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987154"
---
# <a name="sql-server-value-generation"></a>Generazione di valori SQL Server

In questa pagina vengono illustrati i modelli e la configurazione di generazione dei valori specifici del provider SQL Server. È consigliabile leggere prima [la pagina generale sulla generazione dei valori](xref:core/modeling/generated-properties).

## <a name="identity-columns"></a>Colonne IDENTITY

Per convenzione, le colonne numeriche configurate per la generazione dei valori in Aggiungi vengono impostate come [colonne IDENTITY SQL Server](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).

### <a name="seed-and-increment"></a>Valore di inizializzazione e incremento

Per impostazione predefinita, le colonne IDENTITY iniziano da 1 (valore di inizializzazione) e incrementano di 1 ogni volta che viene aggiunta una riga (incremento). È possibile configurare un valore di inizializzazione e un incremento diversi come segue:

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> La possibilità di configurare il valore di inizializzazione e incremento IDENTITY è stata introdotta in EF Core 3,0.

### <a name="inserting-explicit-values-into-identity-columns"></a>Inserimento di valori espliciti in colonne IDENTITY

Per impostazione predefinita, SQL Server non consente l'inserimento di valori espliciti nelle colonne IDENTITY. A tale scopo, è necessario abilitare manualmente `IDENTITY_INSERT` prima di chiamare `SaveChanges()` , come indicato di seguito:

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> Esiste una [richiesta di funzionalità](https://github.com/aspnet/EntityFramework/issues/703) nel backlog per eseguire questa operazione automaticamente all'interno del provider SQL Server.

## <a name="sequences"></a>Sequenze

In alternativa alle colonne IDENTITY, è possibile usare sequenze standard. Questa operazione può essere utile in diversi scenari. ad esempio, è possibile che più colonne disegnino i valori predefiniti da un'unica sequenza.

SQL Server consente di creare sequenze e utilizzarle come descritto in dettaglio nella [pagina generale sulle sequenze](xref:core/modeling/sequences). È possibile configurare le proprietà per l'uso di sequenze tramite `HasDefaultValueSql()` .
