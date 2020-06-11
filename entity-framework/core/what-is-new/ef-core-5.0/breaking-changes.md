---
title: Modifiche di rilievo in EF Core 5,0-EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666195"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="d4828-102">Modifiche di rilievo nella EF Core 5,0</span><span class="sxs-lookup"><span data-stu-id="d4828-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="d4828-103">Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.</span><span class="sxs-lookup"><span data-stu-id="d4828-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="d4828-104">Summary</span><span class="sxs-lookup"><span data-stu-id="d4828-104">Summary</span></span>

| <span data-ttu-id="d4828-105">**Modifica di rilievo**</span><span class="sxs-lookup"><span data-stu-id="d4828-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="d4828-106">**Impatto**</span><span class="sxs-lookup"><span data-stu-id="d4828-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="d4828-107">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="d4828-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="d4828-108">Basso</span><span class="sxs-lookup"><span data-stu-id="d4828-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="d4828-109">Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS</span><span class="sxs-lookup"><span data-stu-id="d4828-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="d4828-110">Rilevamento del problema #14257</span><span class="sxs-lookup"><span data-stu-id="d4828-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="d4828-111">**Comportamento precedente**</span><span class="sxs-lookup"><span data-stu-id="d4828-111">**Old behavior**</span></span>

<span data-ttu-id="d4828-112">HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry.</span><span class="sxs-lookup"><span data-stu-id="d4828-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="d4828-113">Tuttavia, la creazione del database ha avuto solo effetto.</span><span class="sxs-lookup"><span data-stu-id="d4828-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="d4828-114">Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="d4828-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="d4828-115">Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="d4828-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="d4828-116">**Nuovo comportamento**</span><span class="sxs-lookup"><span data-stu-id="d4828-116">**New behavior**</span></span>

<span data-ttu-id="d4828-117">Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="d4828-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="d4828-118">Questo corrisponde maggiormente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="d4828-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="d4828-119">**Perché**</span><span class="sxs-lookup"><span data-stu-id="d4828-119">**Why**</span></span>

<span data-ttu-id="d4828-120">L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.</span><span class="sxs-lookup"><span data-stu-id="d4828-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="d4828-121">**Soluzioni di prevenzione**</span><span class="sxs-lookup"><span data-stu-id="d4828-121">**Mitigations**</span></span>

<span data-ttu-id="d4828-122">Utilizzare `HasColumnType` per specificare la dimensione:</span><span class="sxs-lookup"><span data-stu-id="d4828-122">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
