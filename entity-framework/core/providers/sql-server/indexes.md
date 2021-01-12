---
title: Provider di database Microsoft SQL Server-indici-EF Core
description: Funzionalità di indice specifiche del provider di SQL Server Entity Framework Core
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129161"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="a5057-103">Funzionalità di indice specifiche del provider di SQL Server Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a5057-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="a5057-104">Questa pagina descrive le opzioni di configurazione dell'indice specifiche per il provider di SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a5057-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="a5057-105">Clustering</span><span class="sxs-lookup"><span data-stu-id="a5057-105">Clustering</span></span>

<span data-ttu-id="a5057-106">Gli indici cluster ordinano e archiviano le righe di dati della tabella in base ai valori di chiave,</span><span class="sxs-lookup"><span data-stu-id="a5057-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="a5057-107">La creazione dell'indice cluster corretto per la tabella può migliorare significativamente la velocità delle query, poiché i dati sono già disposti nell'ordine ottimale.</span><span class="sxs-lookup"><span data-stu-id="a5057-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="a5057-108">Per ogni tabella è disponibile un solo indice cluster, poiché alle righe di dati è possibile applicare un solo tipo di ordinamento.</span><span class="sxs-lookup"><span data-stu-id="a5057-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="a5057-109">Per ulteriori informazioni, vedere [la documentazione di SQL Server sugli indici cluster e non cluster](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span><span class="sxs-lookup"><span data-stu-id="a5057-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="a5057-110">Per impostazione predefinita, la colonna chiave primaria di una tabella viene supportata implicitamente da un indice cluster e tutti gli altri indici non sono cluster.</span><span class="sxs-lookup"><span data-stu-id="a5057-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="a5057-111">È possibile configurare un indice o una chiave da raggruppare come segue:</span><span class="sxs-lookup"><span data-stu-id="a5057-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> <span data-ttu-id="a5057-112">SQL Server supporta solo un indice cluster per ogni tabella e la chiave primaria è per impostazione predefinita in cluster.</span><span class="sxs-lookup"><span data-stu-id="a5057-112">SQL Server only supports one clustered index per table, and the primary key is by default clustered.</span></span> <span data-ttu-id="a5057-113">Se si desidera disporre di un indice cluster in una colonna non chiave, è necessario impostare in modo esplicito la chiave non in cluster.</span><span class="sxs-lookup"><span data-stu-id="a5057-113">If you'd like to have a clustered index on a non-key column, you must explicitly make your key non-clustered.</span></span>

## <a name="fill-factor"></a><span data-ttu-id="a5057-114">Fattore di riempimento</span><span class="sxs-lookup"><span data-stu-id="a5057-114">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="a5057-115">Questa funzionalità è stata introdotta in EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="a5057-115">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="a5057-116">Per ottimizzare le prestazioni e l'archiviazione dei dati dell'indice, viene fornita l'opzione del fattore di riempimento degli indici.</span><span class="sxs-lookup"><span data-stu-id="a5057-116">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="a5057-117">Per ulteriori informazioni, vedere [la documentazione SQL Server sul fattore di riempimento](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span><span class="sxs-lookup"><span data-stu-id="a5057-117">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="a5057-118">È possibile configurare il fattore di riempimento di un indice come segue:</span><span class="sxs-lookup"><span data-stu-id="a5057-118">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="a5057-119">Creazione in linea</span><span class="sxs-lookup"><span data-stu-id="a5057-119">Online creation</span></span>

<span data-ttu-id="a5057-120">L'opzione ONLINE consente l'accesso simultaneo degli utenti alla tabella sottostante o ai dati dell'indice cluster e a tutti gli indici non cluster associati durante la creazione dell'indice, in modo che gli utenti possano continuare ad aggiornare ed eseguire query sui dati sottostanti.</span><span class="sxs-lookup"><span data-stu-id="a5057-120">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="a5057-121">Quando si eseguono operazioni DDL (Data Definition Language) offline, ad esempio la compilazione o la ricompilazione di un indice cluster, tali operazioni mantengono blocchi esclusivi sui dati sottostanti e gli indici associati.</span><span class="sxs-lookup"><span data-stu-id="a5057-121">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="a5057-122">Per ulteriori informazioni, vedere [la documentazione di SQL Server sull'opzione per gli indici online](/sql/relational-databases/indexes/perform-index-operations-online).</span><span class="sxs-lookup"><span data-stu-id="a5057-122">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="a5057-123">È possibile configurare un indice con l'opzione ONLINE come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="a5057-123">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
