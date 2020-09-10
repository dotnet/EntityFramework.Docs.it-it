---
title: Provider di Azure Cosmos DB-utilizzo di dati non strutturati-EF Core
description: Come usare Azure Cosmos DB dati non strutturati con Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/unstructured-data
ms.openlocfilehash: 9f96af00e8fcb012c33fc7528787560ea3a5e481
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619041"
---
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="9e814-103">Utilizzo di dati non strutturati nel provider EF Core Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="9e814-103">Working with Unstructured Data in EF Core Azure Cosmos DB Provider</span></span>

<span data-ttu-id="9e814-104">EF Core è stato progettato per semplificare l'utilizzo dei dati che seguono uno schema definito nel modello.</span><span class="sxs-lookup"><span data-stu-id="9e814-104">EF Core was designed to make it easy to work with data that follows a schema defined in the model.</span></span> <span data-ttu-id="9e814-105">Tuttavia uno dei punti di forza di Azure Cosmos DB è la flessibilità nella forma dei dati archiviati.</span><span class="sxs-lookup"><span data-stu-id="9e814-105">However one of the strengths of Azure Cosmos DB is the flexibility in the shape of the data stored.</span></span>

## <a name="accessing-the-raw-json"></a><span data-ttu-id="9e814-106">Accesso al file JSON non elaborato</span><span class="sxs-lookup"><span data-stu-id="9e814-106">Accessing the raw JSON</span></span>

<span data-ttu-id="9e814-107">È possibile accedere alle proprietà che non vengono rilevate da EF Core tramite una proprietà speciale nello [stato Shadow](xref:core/modeling/shadow-properties) denominato contenente `"__jObject"` un oggetto che `JObject` rappresenta i dati ricevuti dall'archivio e i dati che verranno archiviati:</span><span class="sxs-lookup"><span data-stu-id="9e814-107">It is possible to access the properties that are not tracked by EF Core through a special property in [shadow-state](xref:core/modeling/shadow-properties) named `"__jObject"` that contains a `JObject` representing the data recieved from the store and data that will be stored:</span></span>

[!code-csharp[Unmapped](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=23,24&name=Unmapped)]

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "eLMaAK8TzkIBAAAAAAAAAA==",
    "_self": "dbs/eLMaAA==/colls/eLMaAK8TzkI=/docs/eLMaAK8TzkIBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683e-0a12bf8d01d5\"",
    "_attachments": "attachments/",
    "BillingAddress": "Clarence House",
    "_ts": 1568164374
}
```

> [!WARNING]
> <span data-ttu-id="9e814-108">La `"__jObject"` proprietà fa parte dell'infrastruttura di EF core e deve essere usata solo come ultima risorsa, perché probabilmente avrà un comportamento diverso nelle versioni future.</span><span class="sxs-lookup"><span data-stu-id="9e814-108">The `"__jObject"` property is part of the EF Core infrastructure and should only be used as a last resort as it is likely to have different behavior in future releases.</span></span>

> [!NOTE]
> <span data-ttu-id="9e814-109">Le modifiche apportate all'entità sostituiranno i valori archiviati in `"__jObject"` durante `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="9e814-109">Changes to the entity will override the values stored in `"__jObject"` during `SaveChanges`.</span></span>

## <a name="using-cosmosclient"></a><span data-ttu-id="9e814-110">Uso di CosmosClient</span><span class="sxs-lookup"><span data-stu-id="9e814-110">Using CosmosClient</span></span>

<span data-ttu-id="9e814-111">Per separare completamente da EF Core ottenere l'oggetto [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) che fa [parte di Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) da `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="9e814-111">To decouple completely from EF Core get the [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) object that is [part of the Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) from `DbContext`:</span></span>

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a><span data-ttu-id="9e814-112">Valori di proprietà mancanti</span><span class="sxs-lookup"><span data-stu-id="9e814-112">Missing property values</span></span>

<span data-ttu-id="9e814-113">Nell'esempio precedente la proprietà è stata rimossa `"TrackingNumber"` dall'ordine.</span><span class="sxs-lookup"><span data-stu-id="9e814-113">In the previous example we removed the `"TrackingNumber"` property from the order.</span></span> <span data-ttu-id="9e814-114">A causa del funzionamento dell'indicizzazione in Cosmos DB, le query che fanno riferimento alla proprietà mancante altrove rispetto alla proiezione potrebbero restituire risultati imprevisti.</span><span class="sxs-lookup"><span data-stu-id="9e814-114">Because of how indexing works in Cosmos DB, queries that reference the missing property somewhere else than in the projection could return unexpected results.</span></span> <span data-ttu-id="9e814-115">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="9e814-115">For example:</span></span>

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

<span data-ttu-id="9e814-116">La query ordinata non restituisce effettivamente alcun risultato.</span><span class="sxs-lookup"><span data-stu-id="9e814-116">The sorted query actually returns no results.</span></span> <span data-ttu-id="9e814-117">Ciò significa che è necessario prestare attenzione a popolare sempre le proprietà mappate da EF Core quando si utilizza direttamente l'archivio.</span><span class="sxs-lookup"><span data-stu-id="9e814-117">This means that one should take care to always populate properties mapped by EF Core when working with the store directly.</span></span>

> [!NOTE]
> <span data-ttu-id="9e814-118">Questo comportamento potrebbe cambiare nelle versioni future di Cosmos.</span><span class="sxs-lookup"><span data-stu-id="9e814-118">This behavior might change in future versions of Cosmos.</span></span> <span data-ttu-id="9e814-119">Attualmente, ad esempio, se i criteri di indicizzazione definiscono l'indice composito {ID/?</span><span class="sxs-lookup"><span data-stu-id="9e814-119">For instance, currently if the indexing policy defines the composite index {Id/?</span></span> <span data-ttu-id="9e814-120">ASC, TrackingNumber/?</span><span class="sxs-lookup"><span data-stu-id="9e814-120">ASC, TrackingNumber/?</span></span> <span data-ttu-id="9e814-121">ASC)}, una query __con "Order__ by c.ID ASC, c. Discriminator ASC" restituisce gli elementi che non dispongono della `"TrackingNumber"` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="9e814-121">ASC)}, then a query that has 'ORDER BY c.Id ASC, c.Discriminator ASC' __would__ return items that are missing the `"TrackingNumber"` property.</span></span>
