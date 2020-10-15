---
title: Creare ed eliminare API-EF Core
description: API per la creazione e l'eliminazione di database con Entity Framework Core
author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: c23311fbb8254ba183a6fd1661bba915aedc9a97
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062334"
---
# <a name="create-and-drop-apis"></a><span data-ttu-id="30a94-103">API di creazione ed eliminazione</span><span class="sxs-lookup"><span data-stu-id="30a94-103">Create and Drop APIs</span></span>

<span data-ttu-id="30a94-104">I metodi EnsureCreated e EnsureDeleted forniscono un'alternativa semplice alle [migrazioni](xref:core/managing-schemas/migrations/index) per la gestione dello schema del database.</span><span class="sxs-lookup"><span data-stu-id="30a94-104">The EnsureCreated and EnsureDeleted methods provide a lightweight alternative to [Migrations](xref:core/managing-schemas/migrations/index) for managing the database schema.</span></span> <span data-ttu-id="30a94-105">Questi metodi sono utili negli scenari in cui i dati sono temporanei e possono essere eliminati quando lo schema viene modificato.</span><span class="sxs-lookup"><span data-stu-id="30a94-105">These methods are useful in scenarios when the data is transient and can be dropped when the schema changes.</span></span> <span data-ttu-id="30a94-106">Ad esempio, durante la fase di prototipo, nei test o per le cache locali.</span><span class="sxs-lookup"><span data-stu-id="30a94-106">For example during prototyping, in tests, or for local caches.</span></span>

<span data-ttu-id="30a94-107">Alcuni provider (soprattutto quelli non relazionali) non supportano le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="30a94-107">Some providers (especially non-relational ones) don't support Migrations.</span></span> <span data-ttu-id="30a94-108">Per questi provider, EnsureCreated è spesso il modo più semplice per inizializzare lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="30a94-108">For these providers, EnsureCreated is often the easiest way to initialize the database schema.</span></span>

> [!WARNING]
> <span data-ttu-id="30a94-109">EnsureCreated e le migrazioni non funzionano bene insieme.</span><span class="sxs-lookup"><span data-stu-id="30a94-109">EnsureCreated and Migrations don't work well together.</span></span> <span data-ttu-id="30a94-110">Se si usano le migrazioni, non usare EnsureCreated per inizializzare lo schema.</span><span class="sxs-lookup"><span data-stu-id="30a94-110">If you're using Migrations, don't use EnsureCreated to initialize the schema.</span></span>

<span data-ttu-id="30a94-111">La transizione da EnsureCreated a migrazioni non è un'esperienza senza problemi.</span><span class="sxs-lookup"><span data-stu-id="30a94-111">Transitioning from EnsureCreated to Migrations is not a seamless experience.</span></span> <span data-ttu-id="30a94-112">Il modo più semplice per eseguire questa operazione consiste nell'eliminare il database e ricrearlo usando le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="30a94-112">The simplest way to do it is to drop the database and re-create it using Migrations.</span></span> <span data-ttu-id="30a94-113">Se si prevede di usare le migrazioni in futuro, è preferibile iniziare con le migrazioni invece di usare EnsureCreated.</span><span class="sxs-lookup"><span data-stu-id="30a94-113">If you anticipate using migrations in the future, it's best to just start with Migrations instead of using EnsureCreated.</span></span>

## <a name="ensuredeleted"></a><span data-ttu-id="30a94-114">EnsureDeleted</span><span class="sxs-lookup"><span data-stu-id="30a94-114">EnsureDeleted</span></span>

<span data-ttu-id="30a94-115">Se esistente, il metodo EnsureDeleted eliminerà il database.</span><span class="sxs-lookup"><span data-stu-id="30a94-115">The EnsureDeleted method will drop the database if it exists.</span></span> <span data-ttu-id="30a94-116">Se non si dispone delle autorizzazioni appropriate, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="30a94-116">If you don't have the appropriate permissions, an exception is thrown.</span></span>

```csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a><span data-ttu-id="30a94-117">EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="30a94-117">EnsureCreated</span></span>

<span data-ttu-id="30a94-118">EnsureCreated creerà il database se non esiste e inizializza lo schema del database.</span><span class="sxs-lookup"><span data-stu-id="30a94-118">EnsureCreated will create the database if it doesn't exist and initialize the database schema.</span></span> <span data-ttu-id="30a94-119">Se sono presenti tabelle (incluse le tabelle per un'altra classe DbContext), lo schema non verrà inizializzato.</span><span class="sxs-lookup"><span data-stu-id="30a94-119">If any tables exist (including tables for another DbContext class), the schema won't be initialized.</span></span>

```csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> <span data-ttu-id="30a94-120">Sono disponibili anche le versioni asincrone di questi metodi.</span><span class="sxs-lookup"><span data-stu-id="30a94-120">Async versions of these methods are also available.</span></span>

## <a name="sql-script"></a><span data-ttu-id="30a94-121">Script SQL</span><span class="sxs-lookup"><span data-stu-id="30a94-121">SQL Script</span></span>

<span data-ttu-id="30a94-122">Per ottenere l'oggetto SQL usato da EnsureCreated, è possibile usare il Metodo GenerateCreateScript.</span><span class="sxs-lookup"><span data-stu-id="30a94-122">To get the SQL used by EnsureCreated, you can use the GenerateCreateScript method.</span></span>

```csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a><span data-ttu-id="30a94-123">Più classi DbContext</span><span class="sxs-lookup"><span data-stu-id="30a94-123">Multiple DbContext classes</span></span>

<span data-ttu-id="30a94-124">EnsureCreated funziona solo quando nel database non è presente alcuna tabella.</span><span class="sxs-lookup"><span data-stu-id="30a94-124">EnsureCreated only works when no tables are present in the database.</span></span> <span data-ttu-id="30a94-125">Se necessario, è possibile scrivere un controllo personalizzato per verificare se lo schema deve essere inizializzato e usare il servizio IRelationalDatabaseCreator sottostante per inizializzare lo schema.</span><span class="sxs-lookup"><span data-stu-id="30a94-125">If needed, you can write your own check to see if the schema needs to be initialized, and use the underlying IRelationalDatabaseCreator service to initialize the schema.</span></span>

```csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```
