---
title: Provider di database Microsoft SQL Server-indici-EF Core
description: Funzionalità di indice specifiche del provider di SQL Server Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: a01ab3d12f5bf5f05f0925c93d90c0ee40fe977c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061866"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="752d3-103">Funzionalità di indice specifiche del provider di SQL Server Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="752d3-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="752d3-104">Questa pagina descrive le opzioni di configurazione dell'indice specifiche per il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="752d3-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="752d3-105">Clustering</span><span class="sxs-lookup"><span data-stu-id="752d3-105">Clustering</span></span>

<span data-ttu-id="752d3-106">Gli indici cluster ordinano e archiviano le righe di dati della tabella in base ai valori di chiave,</span><span class="sxs-lookup"><span data-stu-id="752d3-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="752d3-107">La creazione dell'indice cluster corretto per la tabella può migliorare significativamente la velocità delle query, poiché i dati sono già disposti nell'ordine ottimale.</span><span class="sxs-lookup"><span data-stu-id="752d3-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="752d3-108">Per ogni tabella è disponibile un solo indice cluster, poiché alle righe di dati è possibile applicare un solo tipo di ordinamento.</span><span class="sxs-lookup"><span data-stu-id="752d3-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="752d3-109">Per ulteriori informazioni, vedere [la documentazione di SQL Server sugli indici cluster e non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span><span class="sxs-lookup"><span data-stu-id="752d3-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="752d3-110">Per impostazione predefinita, la colonna chiave primaria di una tabella viene supportata implicitamente da un indice cluster e tutti gli altri indici non sono cluster.</span><span class="sxs-lookup"><span data-stu-id="752d3-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="752d3-111">È possibile configurare un indice o una chiave da raggruppare come segue:</span><span class="sxs-lookup"><span data-stu-id="752d3-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

## <a name="fill-factor"></a><span data-ttu-id="752d3-112">Fattore di riempimento</span><span class="sxs-lookup"><span data-stu-id="752d3-112">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="752d3-113">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="752d3-113">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="752d3-114">Per ottimizzare le prestazioni e l'archiviazione dei dati dell'indice, viene fornita l'opzione del fattore di riempimento degli indici.</span><span class="sxs-lookup"><span data-stu-id="752d3-114">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="752d3-115">Per ulteriori informazioni, vedere [la documentazione SQL Server sul fattore di riempimento](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span><span class="sxs-lookup"><span data-stu-id="752d3-115">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="752d3-116">È possibile configurare il fattore di riempimento di un indice come segue:</span><span class="sxs-lookup"><span data-stu-id="752d3-116">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="752d3-117">Creazione in linea</span><span class="sxs-lookup"><span data-stu-id="752d3-117">Online creation</span></span>

<span data-ttu-id="752d3-118">L'opzione ONLINE consente l'accesso simultaneo degli utenti alla tabella sottostante o ai dati dell'indice cluster e a tutti gli indici non cluster associati durante la creazione dell'indice, in modo che gli utenti possano continuare ad aggiornare ed eseguire query sui dati sottostanti.</span><span class="sxs-lookup"><span data-stu-id="752d3-118">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="752d3-119">Quando si eseguono operazioni DDL (Data Definition Language) offline, ad esempio la compilazione o la ricompilazione di un indice cluster, tali operazioni mantengono blocchi esclusivi sui dati sottostanti e gli indici associati.</span><span class="sxs-lookup"><span data-stu-id="752d3-119">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="752d3-120">Per ulteriori informazioni, vedere [la documentazione di SQL Server sull'opzione per gli indici online](/sql/relational-databases/indexes/perform-index-operations-online).</span><span class="sxs-lookup"><span data-stu-id="752d3-120">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="752d3-121">È possibile configurare un indice con l'opzione ONLINE come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="752d3-121">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
