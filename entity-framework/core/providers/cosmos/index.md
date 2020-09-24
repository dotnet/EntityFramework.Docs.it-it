---
title: Provider di Azure Cosmos DB - EF Core
description: Documentazione per il provider di database che consente l'uso di Entity Framework Core con l'API SQL di Azure Cosmos DB
author: AndriySvyryd
ms.date: 09/14/2020
uid: core/providers/cosmos/index
ms.openlocfilehash: 94ba29f3f2643e8f563a460e17dce9d15cb7c2df
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210341"
---
# <a name="ef-core-azure-cosmos-db-provider"></a>Provider di Azure Cosmos DB per EF Core

> [!NOTE]
> Questo provider è una novità di EF Core 3.0.

Questo provider di database consente l'uso di Entity Framework Core con Azure Cosmos DB. Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).

Prima di leggere questa sezione, è consigliabile acquisire familiarità con la [documentazione di Azure Cosmos DB](/azure/cosmos-db/introduction).

> [!NOTE]
> Questo provider funziona solo con l'API SQL di Azure Cosmos DB.

## <a name="install"></a>Installazione

Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a>Introduzione

> [!TIP]  
> È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) di questo articolo in GitHub.

Come per gli altri provider, il primo passaggio consiste nel chiamare [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> L'endpoint e la chiave sono hardcoded qui per semplicità, ma in un'app di produzione devono essere [archiviati in modo sicuro](/aspnet/core/security/app-secrets#secret-manager).

In questo esempio `Order` è un'entità semplice con un riferimento al [tipo di proprietà](xref:core/modeling/owned-entities) `StreetAddress`.

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

Il salvataggio e l'esecuzione di query sui dati seguono il modello EF normale:

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> La chiamata di [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) è necessaria per creare i contenitori richiesti e inserire i [dati di inizializzazione](xref:core/modeling/data-seeding) se presenti nel modello. Tuttavia, `EnsureCreatedAsync` deve essere chiamato solo durante la distribuzione, e non durante il normale funzionamento, perché potrebbe causare problemi di prestazioni.

## <a name="cosmos-options"></a>Opzioni di Cosmos

È anche possibile configurare il provider di Cosmos DB con una singola stringa di connessione e specificare altre opzioni per personalizzare la connessione:

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OptionsContext.cs?name=Configuration)]

> [!NOTE]
> La maggior parte di queste opzioni è una novità di EF Core Cosmos 5,0.

> [!TIP]
> Per una descrizione dettagliata dell'effetto di ogni opzione indicata in precedenza, vedere la [documentazione relativa alle opzioni di Azure Cosmos DB](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) .

## <a name="cosmos-specific-model-customization"></a>Personalizzazione del modello specifico di Cosmos

Per impostazione predefinita, viene eseguito il mapping di tutti i tipi di entità allo stesso contenitore, denominato in base al contesto derivato (`"OrderContext"` in questo caso). Per modificare il nome del contenitore predefinito, usare [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

Per eseguire il mapping di un tipo di entità a un contenitore diverso, usare [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

Per identificare il tipo di entità rappresentato da un determinato elemento, EF Core aggiunge un valore discriminatore anche se non sono presenti tipi di entità derivati. Il nome e il valore del discriminatore [possono essere modificati](xref:core/modeling/inheritance).

Se nessun altro tipo di entità verrà archiviato nello stesso contenitore, il discriminatore può essere rimosso chiamando [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a>Chiavi di partizione

Per impostazione predefinita EF Core creerà contenitori con la chiave di partizione impostata su `"__partitionKey"` senza specificare alcun valore durante l'inserimento di elementi. Tuttavia, per sfruttare appieno le capacità a livello di prestazioni di Azure Cosmos è consigliabile usare una [chiave di partizione accuratamente selezionata](/azure/cosmos-db/partition-data). Può essere configurata chiamando [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
>La proprietà della chiave di partizione può essere di qualsiasi tipo, purché sia [convertita in stringa](xref:core/modeling/value-conversions).

Una volta configurata, la proprietà della chiave di partizione deve avere sempre un valore non Null. Quando si esegue una query, è possibile aggiungere una condizione per renderla a partizione singola.

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a>Entità incorporate

Per Cosmos, le entità di proprietà sono incorporate nello stesso elemento del proprietario. Per modificare il nome di una proprietà, usare [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

Con questa configurazione, l'ordine dell'esempio precedente viene archiviato come segue:

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
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

Anche le raccolte di entità di proprietà sono incorporate. Per l'esempio successivo verrà usata la classe `Distributor` con una raccolta di `StreetAddress`:

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

Non è necessario che le entità di proprietà forniscano valori di chiave espliciti da archiviare:

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

Verranno salvati in modo persistente in questo modo:

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

Internamente, EF Core deve avere sempre valori di chiave univoca per tutte le entità rilevate. La chiave primaria creata per impostazione predefinita per le raccolte di tipi di proprietà è costituita dalle proprietà di chiave esterna che puntano al proprietario e da una proprietà `int` corrispondente all'indice nella matrice JSON. Per recuperare questi valori, è possibile usare l'API Entry:

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> Quando necessario, è possibile modificare la chiave primaria predefinita per i tipi di entità di proprietà, ma i valori di chiave devono poi essere specificati in modo esplicito.

## <a name="working-with-disconnected-entities"></a>Uso delle entità disconnesse

Ogni elemento deve avere un valore `id` univoco per la chiave di partizione specificata. Per impostazione predefinita, EF Core genera il valore concatenando il discriminatore e i valori di chiave primaria usando '|' come delimitatore. I valori delle chiavi vengono generati solo quando un'entità entra nello stato `Added`. Questo potrebbe rappresentare un problema quando si [collegano le entità](xref:core/saving/disconnected-entities) se non hanno una proprietà `id` per il tipo .NET per archiviare il valore.

Per ovviare a questa limitazione, è possibile creare e impostare il valore `id` manualmente oppure contrassegnare prima l'entità come aggiunta, per poi modificarla nello stato desiderato:

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

Questo è il codice JSON risultante:

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        }
    ],
    "_rid": "JBwtAN8oNYEBAAAAAAAAAA==",
    "_self": "dbs/JBwtAA==/colls/JBwtAN8oNYE=/docs/JBwtAN8oNYEBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-9377-d7a1ae7c01d5\"",
    "_attachments": "attachments/",
    "_ts": 1572917100
}
```
