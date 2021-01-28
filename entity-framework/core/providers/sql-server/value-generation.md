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
# <a name="sql-server-value-generation"></a><span data-ttu-id="159c9-103">Generazione di valori SQL Server</span><span class="sxs-lookup"><span data-stu-id="159c9-103">SQL Server Value Generation</span></span>

<span data-ttu-id="159c9-104">In questa pagina vengono illustrati i modelli e la configurazione di generazione dei valori specifici del provider SQL Server.</span><span class="sxs-lookup"><span data-stu-id="159c9-104">This page details value generation configuration  and patterns that are specific to the SQL Server provider.</span></span> <span data-ttu-id="159c9-105">È consigliabile leggere prima [la pagina generale sulla generazione dei valori](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="159c9-105">It's recommended to first read [the general page on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="identity-columns"></a><span data-ttu-id="159c9-106">Colonne IDENTITY</span><span class="sxs-lookup"><span data-stu-id="159c9-106">IDENTITY columns</span></span>

<span data-ttu-id="159c9-107">Per convenzione, le colonne numeriche configurate per la generazione dei valori in Aggiungi vengono impostate come [colonne IDENTITY SQL Server](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span><span class="sxs-lookup"><span data-stu-id="159c9-107">By convention, numeric columns that are configured to have their values generated on add are set up as [SQL Server IDENTITY columns](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span></span>

### <a name="seed-and-increment"></a><span data-ttu-id="159c9-108">Valore di inizializzazione e incremento</span><span class="sxs-lookup"><span data-stu-id="159c9-108">Seed and increment</span></span>

<span data-ttu-id="159c9-109">Per impostazione predefinita, le colonne IDENTITY iniziano da 1 (valore di inizializzazione) e incrementano di 1 ogni volta che viene aggiunta una riga (incremento).</span><span class="sxs-lookup"><span data-stu-id="159c9-109">By default, IDENTITY columns start off at 1 (the seed), and increment by 1 each time a row is added (the increment).</span></span> <span data-ttu-id="159c9-110">È possibile configurare un valore di inizializzazione e un incremento diversi come segue:</span><span class="sxs-lookup"><span data-stu-id="159c9-110">You can configure a different seed and increment as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> <span data-ttu-id="159c9-111">La possibilità di configurare il valore di inizializzazione e incremento IDENTITY è stata introdotta in EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="159c9-111">The ability to configure IDENTITY seed and increment was introduced in EF Core 3.0.</span></span>

### <a name="inserting-explicit-values-into-identity-columns"></a><span data-ttu-id="159c9-112">Inserimento di valori espliciti in colonne IDENTITY</span><span class="sxs-lookup"><span data-stu-id="159c9-112">Inserting explicit values into IDENTITY columns</span></span>

<span data-ttu-id="159c9-113">Per impostazione predefinita, SQL Server non consente l'inserimento di valori espliciti nelle colonne IDENTITY.</span><span class="sxs-lookup"><span data-stu-id="159c9-113">By default, SQL Server doesn't allow inserting explicit values into IDENTITY columns.</span></span> <span data-ttu-id="159c9-114">A tale scopo, è necessario abilitare manualmente `IDENTITY_INSERT` prima di chiamare `SaveChanges()` , come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="159c9-114">To do so, you must manually enable `IDENTITY_INSERT` before calling `SaveChanges()`, as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> <span data-ttu-id="159c9-115">Esiste una [richiesta di funzionalità](https://github.com/aspnet/EntityFramework/issues/703) nel backlog per eseguire questa operazione automaticamente all'interno del provider SQL Server.</span><span class="sxs-lookup"><span data-stu-id="159c9-115">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

## <a name="sequences"></a><span data-ttu-id="159c9-116">Sequenze</span><span class="sxs-lookup"><span data-stu-id="159c9-116">Sequences</span></span>

<span data-ttu-id="159c9-117">In alternativa alle colonne IDENTITY, è possibile usare sequenze standard.</span><span class="sxs-lookup"><span data-stu-id="159c9-117">As an alternative to IDENTITY columns, you can use standard sequences.</span></span> <span data-ttu-id="159c9-118">Questa operazione può essere utile in diversi scenari. ad esempio, è possibile che più colonne disegnino i valori predefiniti da un'unica sequenza.</span><span class="sxs-lookup"><span data-stu-id="159c9-118">This can be useful in various scenarios; for example, you may want to have multiple columns drawing their default values from a single sequence.</span></span>

<span data-ttu-id="159c9-119">SQL Server consente di creare sequenze e utilizzarle come descritto in dettaglio nella [pagina generale sulle sequenze](xref:core/modeling/sequences).</span><span class="sxs-lookup"><span data-stu-id="159c9-119">SQL Server allows you to create sequences and use them as detailed in [the general page on sequences](xref:core/modeling/sequences).</span></span> <span data-ttu-id="159c9-120">È possibile configurare le proprietà per l'uso di sequenze tramite `HasDefaultValueSql()` .</span><span class="sxs-lookup"><span data-stu-id="159c9-120">It's up to you to configure your properties to use sequences via `HasDefaultValueSql()`.</span></span>
