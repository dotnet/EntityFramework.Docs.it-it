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
# <a name="working-with-unstructured-data-in-ef-core-azure-cosmos-db-provider"></a>Utilizzo di dati non strutturati nel provider EF Core Azure Cosmos DB

EF Core è stato progettato per semplificare l'utilizzo dei dati che seguono uno schema definito nel modello. Tuttavia uno dei punti di forza di Azure Cosmos DB è la flessibilità nella forma dei dati archiviati.

## <a name="accessing-the-raw-json"></a>Accesso al file JSON non elaborato

È possibile accedere alle proprietà che non vengono rilevate da EF Core tramite una proprietà speciale nello [stato Shadow](xref:core/modeling/shadow-properties) denominato contenente `"__jObject"` un oggetto che `JObject` rappresenta i dati ricevuti dall'archivio e i dati che verranno archiviati:

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
> La `"__jObject"` proprietà fa parte dell'infrastruttura di EF core e deve essere usata solo come ultima risorsa, perché probabilmente avrà un comportamento diverso nelle versioni future.

> [!NOTE]
> Le modifiche apportate all'entità sostituiranno i valori archiviati in `"__jObject"` durante `SaveChanges` .

## <a name="using-cosmosclient"></a>Uso di CosmosClient

Per separare completamente da EF Core ottenere l'oggetto [CosmosClient](/dotnet/api/Microsoft.Azure.Cosmos.CosmosClient) che fa [parte di Azure Cosmos DB SDK](/azure/cosmos-db/sql-api-get-started) da `DbContext` :

[!code-csharp[CosmosClient](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?highlight=3&name=CosmosClient)]

## <a name="missing-property-values"></a>Valori di proprietà mancanti

Nell'esempio precedente la proprietà è stata rimossa `"TrackingNumber"` dall'ordine. A causa del funzionamento dell'indicizzazione in Cosmos DB, le query che fanno riferimento alla proprietà mancante altrove rispetto alla proiezione potrebbero restituire risultati imprevisti. Ad esempio:

[!code-csharp[MissingProperties](../../../../samples/core/Cosmos/UnstructuredData/Sample.cs?name=MissingProperties)]

La query ordinata non restituisce effettivamente alcun risultato. Ciò significa che è necessario prestare attenzione a popolare sempre le proprietà mappate da EF Core quando si utilizza direttamente l'archivio.

> [!NOTE]
> Questo comportamento potrebbe cambiare nelle versioni future di Cosmos. Attualmente, ad esempio, se i criteri di indicizzazione definiscono l'indice composito {ID/? ASC, TrackingNumber/? ASC)}, una query __con "Order__ by c.ID ASC, c. Discriminator ASC" restituisce gli elementi che non dispongono della `"TrackingNumber"` Proprietà.
