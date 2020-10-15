---
title: Suddivisione di tabelle-EF Core
description: Come configurare la suddivisione delle tabelle con Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: 71d332ec3f500f48e12863c71ac44ce4d60699f0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063036"
---
# <a name="table-splitting"></a><span data-ttu-id="61545-103">Suddivisione di tabelle</span><span class="sxs-lookup"><span data-stu-id="61545-103">Table Splitting</span></span>

<span data-ttu-id="61545-104">EF Core consente di eseguire il mapping di due o più entità a una singola riga.</span><span class="sxs-lookup"><span data-stu-id="61545-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="61545-105">Questa operazione è denominata _suddivisione tabelle_ o _condivisione tabella_.</span><span class="sxs-lookup"><span data-stu-id="61545-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="61545-106">Configurazione</span><span class="sxs-lookup"><span data-stu-id="61545-106">Configuration</span></span>

<span data-ttu-id="61545-107">Per utilizzare la suddivisione della tabella è necessario eseguire il mapping dei tipi di entità alla stessa tabella, disporre delle chiavi primarie mappate alle stesse colonne e almeno una relazione configurata tra la chiave primaria di un tipo di entità e un'altra nella stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="61545-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="61545-108">Uno scenario comune per la suddivisione delle tabelle consiste nell'utilizzare solo un subset delle colonne della tabella per ottenere un livello superiore di prestazioni o incapsulamento.</span><span class="sxs-lookup"><span data-stu-id="61545-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="61545-109">In questo esempio `Order` rappresenta un subset di `DetailedOrder` .</span><span class="sxs-lookup"><span data-stu-id="61545-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="61545-110">Oltre alla configurazione richiesta, viene chiamato `Property(o => o.Status).HasColumnName("Status")` per eseguire `DetailedOrder.Status` il mapping alla stessa colonna di `Order.Status` .</span><span class="sxs-lookup"><span data-stu-id="61545-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="61545-111">Per ulteriori informazioni sul contesto, vedere il [progetto di esempio completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) .</span><span class="sxs-lookup"><span data-stu-id="61545-111">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="61545-112">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="61545-112">Usage</span></span>

<span data-ttu-id="61545-113">Il salvataggio e l'esecuzione di query sulle entità tramite la suddivisione delle tabelle vengono eseguite in modo analogo alle altre entità:</span><span class="sxs-lookup"><span data-stu-id="61545-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="61545-114">Entità dipendente facoltativa</span><span class="sxs-lookup"><span data-stu-id="61545-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="61545-115">Questa funzionalità è stata introdotta in EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="61545-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="61545-116">Se tutte le colonne utilizzate da un'entità dipendente si trovano `NULL` nel database, non verrà creata alcuna istanza per tale entità quando viene eseguita una query.</span><span class="sxs-lookup"><span data-stu-id="61545-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="61545-117">In questo modo è possibile modellare un'entità dipendente facoltativa, in cui la proprietà relationship nell'entità è null.</span><span class="sxs-lookup"><span data-stu-id="61545-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="61545-118">Si noti che questo si verifica anche se tutte le proprietà del dipendente sono facoltative e sono impostate su `null` , che potrebbe non essere previsto.</span><span class="sxs-lookup"><span data-stu-id="61545-118">Note that this would also happen if all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="61545-119">Token di concorrenza</span><span class="sxs-lookup"><span data-stu-id="61545-119">Concurrency tokens</span></span>

<span data-ttu-id="61545-120">Se uno dei tipi di entità che condividono una tabella include un token di concorrenza, è necessario includerlo anche in tutti gli altri tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="61545-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="61545-121">Questa operazione è necessaria per evitare un valore del token di concorrenza non aggiornato quando viene aggiornata solo una delle entità mappate alla stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="61545-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="61545-122">Per evitare che il token di concorrenza venga esposto al codice consumer, è possibile crearne uno come [proprietà shadow](xref:core/modeling/shadow-properties):</span><span class="sxs-lookup"><span data-stu-id="61545-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
