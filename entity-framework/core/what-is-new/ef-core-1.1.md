---
title: Novità di EF Core 1.1 - EF Core
description: Modifiche e miglioramenti in Entity Framework Core 1,1
author: ajcvickers
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: a865270d0b1b690ed2596e7ed550463ab0877bca
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063465"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="ac580-103">Nuove funzionalità di EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="ac580-103">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="ac580-104">Modellazione</span><span class="sxs-lookup"><span data-stu-id="ac580-104">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="ac580-105">Mapping campi</span><span class="sxs-lookup"><span data-stu-id="ac580-105">Field mapping</span></span>

<span data-ttu-id="ac580-106">Consente di configurare un campo sottostante per una proprietà.</span><span class="sxs-lookup"><span data-stu-id="ac580-106">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="ac580-107">Può essere utile per le proprietà di sola lettura o per i dati che hanno metodi Get/Set invece di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="ac580-107">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="ac580-108">Mapping a tabelle ottimizzate per la memoria in SQL Server</span><span class="sxs-lookup"><span data-stu-id="ac580-108">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="ac580-109">È possibile specificare che la tabella a cui viene eseguito il mapping di un'entità è ottimizzata per la memoria.</span><span class="sxs-lookup"><span data-stu-id="ac580-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="ac580-110">Quando si usa EF Core per creare e gestire un database in base al modello (con le migrazioni o `Database.EnsureCreated()`), per queste entità verrà creata una tabella ottimizzata per la memoria.</span><span class="sxs-lookup"><span data-stu-id="ac580-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="ac580-111">Rilevamento modifiche</span><span class="sxs-lookup"><span data-stu-id="ac580-111">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="ac580-112">Altre API di rilevamento modifiche da EF6</span><span class="sxs-lookup"><span data-stu-id="ac580-112">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="ac580-113">Ad esempio, `Reload`, `GetModifiedProperties`, `GetDatabaseValues` e così via.</span><span class="sxs-lookup"><span data-stu-id="ac580-113">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="ac580-114">Query</span><span class="sxs-lookup"><span data-stu-id="ac580-114">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="ac580-115">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="ac580-115">Explicit Loading</span></span>

<span data-ttu-id="ac580-116">Consente di attivare il popolamento di una proprietà di navigazione in un'entità caricata in precedenza dal database.</span><span class="sxs-lookup"><span data-stu-id="ac580-116">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="ac580-117">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="ac580-117">DbSet.Find</span></span>

<span data-ttu-id="ac580-118">Consente di recuperare facilmente un'entità in base al valore della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="ac580-118">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="ac580-119">Altro</span><span class="sxs-lookup"><span data-stu-id="ac580-119">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="ac580-120">Resilienza delle connessioni</span><span class="sxs-lookup"><span data-stu-id="ac580-120">Connection resiliency</span></span>

<span data-ttu-id="ac580-121">Ritenta automaticamente i comandi del database non riusciti.</span><span class="sxs-lookup"><span data-stu-id="ac580-121">Automatically retries failed database commands.</span></span> <span data-ttu-id="ac580-122">È particolarmente utile durante la connessione a SQL Azure, in cui sono comuni gli errori temporanei.</span><span class="sxs-lookup"><span data-stu-id="ac580-122">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="ac580-123">Sostituzione dei servizi semplificata</span><span class="sxs-lookup"><span data-stu-id="ac580-123">Simplified service replacement</span></span>

<span data-ttu-id="ac580-124">Facilita la sostituzione dei servizi interni usati da EF.</span><span class="sxs-lookup"><span data-stu-id="ac580-124">Makes it easier to replace internal services that EF uses.</span></span>
