---
title: Provider di Azure Cosmos DB - EF Core
description: Documentazione per il provider di database che consente l'uso di Entity Framework Core con l'API SQL di Azure Cosmos DB
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/index
ms.openlocfilehash: 188c5b10aefa784715c0bbf2d50337d65c21fd62
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071368"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="d9dfd-103">Provider di Azure Cosmos DB per EF Core</span><span class="sxs-lookup"><span data-stu-id="d9dfd-103">EF Core Azure Cosmos DB Provider</span></span>

> [!NOTE]
> <span data-ttu-id="d9dfd-104">Questo provider è una novità di EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-104">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="d9dfd-105">Questo provider di database consente l'uso di Entity Framework Core con Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-105">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="d9dfd-106">Il provider viene gestito nell'ambito del [progetto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="d9dfd-107">Prima di leggere questa sezione, è consigliabile acquisire familiarità con la [documentazione di Azure Cosmos DB](/azure/cosmos-db/introduction).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-107">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](/azure/cosmos-db/introduction) before reading this section.</span></span>

> [!NOTE]
> <span data-ttu-id="d9dfd-108">Questo provider funziona solo con l'API SQL di Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-108">This provider only works with the SQL API of Azure Cosmos DB.</span></span>

## <a name="install"></a><span data-ttu-id="d9dfd-109">Installazione</span><span class="sxs-lookup"><span data-stu-id="d9dfd-109">Install</span></span>

<span data-ttu-id="d9dfd-110">Installare il [pacchetto NuGet Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-110">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d9dfd-111">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="d9dfd-111">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[<span data-ttu-id="d9dfd-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9dfd-112">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="d9dfd-113">Introduzione</span><span class="sxs-lookup"><span data-stu-id="d9dfd-113">Get started</span></span>

> [!TIP]  
> <span data-ttu-id="d9dfd-114">È possibile visualizzare l'[esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos) di questo articolo in GitHub.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-114">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="d9dfd-115">Come per gli altri provider, il primo passaggio consiste nel chiamare [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span><span class="sxs-lookup"><span data-stu-id="d9dfd-115">As for other providers the first step is to call [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="d9dfd-116">L'endpoint e la chiave sono hardcoded qui per semplicità, ma in un'app di produzione devono essere [archiviati in modo sicuro](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-116">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securely](/aspnet/core/security/app-secrets#secret-manager).</span></span>

<span data-ttu-id="d9dfd-117">In questo esempio `Order` è un'entità semplice con un riferimento al [tipo di proprietà](xref:core/modeling/owned-entities) `StreetAddress`.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-117">In this example `Order` is a simple entity with a reference to the [owned type](xref:core/modeling/owned-entities) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="d9dfd-118">Il salvataggio e l'esecuzione di query sui dati seguono il modello EF normale:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-118">Saving and querying data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="d9dfd-119">La chiamata di [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) è necessaria per creare i contenitori richiesti e inserire i [dati di inizializzazione](xref:core/modeling/data-seeding) se presenti nel modello.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-119">Calling [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) is necessary to create the required containers and insert the [seed data](xref:core/modeling/data-seeding) if present in the model.</span></span> <span data-ttu-id="d9dfd-120">Tuttavia, `EnsureCreatedAsync` deve essere chiamato solo durante la distribuzione, e non durante il normale funzionamento, perché potrebbe causare problemi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-120">However `EnsureCreatedAsync` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="d9dfd-121">Personalizzazione del modello specifico di Cosmos</span><span class="sxs-lookup"><span data-stu-id="d9dfd-121">Cosmos-specific model customization</span></span>

<span data-ttu-id="d9dfd-122">Per impostazione predefinita, viene eseguito il mapping di tutti i tipi di entità allo stesso contenitore, denominato in base al contesto derivato (`"OrderContext"` in questo caso).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-122">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="d9dfd-123">Per modificare il nome del contenitore predefinito, usare [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span><span class="sxs-lookup"><span data-stu-id="d9dfd-123">To change the default container name use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="d9dfd-124">Per eseguire il mapping di un tipo di entità a un contenitore diverso, usare [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span><span class="sxs-lookup"><span data-stu-id="d9dfd-124">To map an entity type to a different container use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="d9dfd-125">Per identificare il tipo di entità rappresentato da un determinato elemento, EF Core aggiunge un valore discriminatore anche se non sono presenti tipi di entità derivati.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-125">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="d9dfd-126">Il nome e il valore del discriminatore [possono essere modificati](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-126">The name and value of the discriminator [can be changed](xref:core/modeling/inheritance).</span></span>

<span data-ttu-id="d9dfd-127">Se nessun altro tipo di entità verrà archiviato nello stesso contenitore, il discriminatore può essere rimosso chiamando [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span><span class="sxs-lookup"><span data-stu-id="d9dfd-127">If no other entity type will ever be stored in the same container the discriminator can be removed by calling [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span></span>

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a><span data-ttu-id="d9dfd-128">Chiavi di partizione</span><span class="sxs-lookup"><span data-stu-id="d9dfd-128">Partition keys</span></span>

<span data-ttu-id="d9dfd-129">Per impostazione predefinita EF Core creerà contenitori con la chiave di partizione impostata su `"__partitionKey"` senza specificare alcun valore durante l'inserimento di elementi.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-129">By default EF Core will create containers with the partition key set to `"__partitionKey"` without supplying any value for it when inserting items.</span></span> <span data-ttu-id="d9dfd-130">Tuttavia, per sfruttare appieno le capacità a livello di prestazioni di Azure Cosmos è consigliabile usare una [chiave di partizione accuratamente selezionata](/azure/cosmos-db/partition-data).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-130">But to fully leverage the performance capabilities of Azure Cosmos a [carefully selected partition key](/azure/cosmos-db/partition-data) should be used.</span></span> <span data-ttu-id="d9dfd-131">Può essere configurata chiamando [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span><span class="sxs-lookup"><span data-stu-id="d9dfd-131">It can be configured by calling [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
><span data-ttu-id="d9dfd-132">La proprietà della chiave di partizione può essere di qualsiasi tipo, purché sia [convertita in stringa](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="d9dfd-132">The partition key property can be of any type as long as it is [converted to string](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="d9dfd-133">Una volta configurata, la proprietà della chiave di partizione deve avere sempre un valore non Null.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-133">Once configured the partition key property should always have a non-null value.</span></span> <span data-ttu-id="d9dfd-134">Quando si esegue una query, è possibile aggiungere una condizione per renderla a partizione singola.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-134">When issuing a query a condition can be added to make it single-partition.</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a><span data-ttu-id="d9dfd-135">Entità incorporate</span><span class="sxs-lookup"><span data-stu-id="d9dfd-135">Embedded entities</span></span>

<span data-ttu-id="d9dfd-136">Per Cosmos, le entità di proprietà sono incorporate nello stesso elemento del proprietario.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-136">For Cosmos, owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="d9dfd-137">Per modificare il nome di una proprietà, usare [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span><span class="sxs-lookup"><span data-stu-id="d9dfd-137">To change a property name use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="d9dfd-138">Con questa configurazione, l'ordine dell'esempio precedente viene archiviato come segue:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-138">With this configuration the order from the example above is stored like this:</span></span>

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

<span data-ttu-id="d9dfd-139">Anche le raccolte di entità di proprietà sono incorporate.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-139">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="d9dfd-140">Per l'esempio successivo verrà usata la classe `Distributor` con una raccolta di `StreetAddress`:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-140">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="d9dfd-141">Non è necessario che le entità di proprietà forniscano valori di chiave espliciti da archiviare:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-141">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="d9dfd-142">Verranno salvati in modo persistente in questo modo:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-142">They will be persisted in this way:</span></span>

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

<span data-ttu-id="d9dfd-143">Internamente, EF Core deve avere sempre valori di chiave univoca per tutte le entità rilevate.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-143">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="d9dfd-144">La chiave primaria creata per impostazione predefinita per le raccolte di tipi di proprietà è costituita dalle proprietà di chiave esterna che puntano al proprietario e da una proprietà `int` corrispondente all'indice nella matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-144">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="d9dfd-145">Per recuperare questi valori, è possibile usare l'API Entry:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-145">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="d9dfd-146">Quando necessario, è possibile modificare la chiave primaria predefinita per i tipi di entità di proprietà, ma i valori di chiave devono poi essere specificati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-146">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="d9dfd-147">Uso delle entità disconnesse</span><span class="sxs-lookup"><span data-stu-id="d9dfd-147">Working with disconnected entities</span></span>

<span data-ttu-id="d9dfd-148">Ogni elemento deve avere un valore `id` univoco per la chiave di partizione specificata.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-148">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="d9dfd-149">Per impostazione predefinita, EF Core genera il valore concatenando il discriminatore e i valori di chiave primaria usando '|' come delimitatore.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-149">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="d9dfd-150">I valori delle chiavi vengono generati solo quando un'entità entra nello stato `Added`.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-150">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="d9dfd-151">Questo potrebbe rappresentare un problema quando si [collegano le entità](xref:core/saving/disconnected-entities) se non hanno una proprietà `id` per il tipo .NET per archiviare il valore.</span><span class="sxs-lookup"><span data-stu-id="d9dfd-151">This might pose a problem when [attaching entities](xref:core/saving/disconnected-entities) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="d9dfd-152">Per ovviare a questa limitazione, è possibile creare e impostare il valore `id` manualmente oppure contrassegnare prima l'entità come aggiunta, per poi modificarla nello stato desiderato:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-152">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="d9dfd-153">Questo è il codice JSON risultante:</span><span class="sxs-lookup"><span data-stu-id="d9dfd-153">This is the resulting JSON:</span></span>

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
