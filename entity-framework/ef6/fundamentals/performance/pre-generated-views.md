---
title: Viste di mapping pre-generate-EF6
description: Viste di mapping pre-generate in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: b69c1eea1e219d76a8de1b2cf3b88de2cf74c8f0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073552"
---
# <a name="pre-generated-mapping-views"></a><span data-ttu-id="7742c-103">Viste di mapping pre-generate</span><span class="sxs-lookup"><span data-stu-id="7742c-103">Pre-generated mapping views</span></span>
<span data-ttu-id="7742c-104">Prima che l'Entity Framework possa eseguire una query o salvare le modifiche apportate all'origine dati, deve generare un set di visualizzazioni di mapping per accedere al database.</span><span class="sxs-lookup"><span data-stu-id="7742c-104">Before the Entity Framework can execute a query or save changes to the data source, it must generate a set of mapping views to access the database.</span></span> <span data-ttu-id="7742c-105">Queste viste di mapping sono un set di Entity SQL istruzione che rappresentano il database in modo astratto e fanno parte dei metadati memorizzati nella cache per ogni dominio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7742c-105">These mapping views are a set of Entity SQL statement that represent the database in an abstract way, and are part of the metadata which is cached per application domain.</span></span> <span data-ttu-id="7742c-106">Se si creano più istanze dello stesso contesto nello stesso dominio applicazione, verranno riutilizzate le visualizzazioni di mapping dei metadati memorizzati nella cache anziché rigenerarle.</span><span class="sxs-lookup"><span data-stu-id="7742c-106">If you create multiple instances of the same context in the same application domain, they will reuse mapping views from the cached metadata rather than regenerating them.</span></span> <span data-ttu-id="7742c-107">Poiché la generazione della visualizzazione del mapping è una parte significativa del costo complessivo dell'esecuzione della prima query, il Entity Framework consente di pregenerare le visualizzazioni di mapping e includerle nel progetto compilato.</span><span class="sxs-lookup"><span data-stu-id="7742c-107">Because mapping view generation is a significant part of the overall cost of executing the first query, the Entity Framework enables you to pre-generate mapping views and include them in the compiled project.</span></span><span data-ttu-id="7742c-108">Per ulteriori informazioni, vedere  [considerazioni sulle prestazioni (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span><span class="sxs-lookup"><span data-stu-id="7742c-108"> For more information, see  [Performance Considerations (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).</span></span>

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a><span data-ttu-id="7742c-109">Generazione di visualizzazioni di mapping con EF Power Tools Community Edition</span><span class="sxs-lookup"><span data-stu-id="7742c-109">Generating Mapping Views with the EF Power Tools Community Edition</span></span>

<span data-ttu-id="7742c-110">Il modo più semplice per pre-generare le visualizzazioni consiste nell'usare [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span><span class="sxs-lookup"><span data-stu-id="7742c-110">The easiest way to pre-generate views is to use the [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition).</span></span> <span data-ttu-id="7742c-111">Dopo aver installato Power Tools, sarà presente un'opzione di menu per generare le visualizzazioni, come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="7742c-111">Once you have the Power Tools installed you will have a menu option to Generate Views, as below.</span></span>

-   <span data-ttu-id="7742c-112">Per **Code First** modelli, fare clic con il pulsante destro del mouse sul file di codice che contiene la classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="7742c-112">For **Code First** models right-click on the code file that contains your DbContext class.</span></span>
-   <span data-ttu-id="7742c-113">Per i modelli **EF designer** fare clic con il pulsante destro del mouse sul file edmx.</span><span class="sxs-lookup"><span data-stu-id="7742c-113">For **EF Designer** models right-click on your EDMX file.</span></span>

![Genera viste](~/ef6/media/generateviews.png)

<span data-ttu-id="7742c-115">Al termine del processo, sarà presente una classe simile alla seguente generata</span><span class="sxs-lookup"><span data-stu-id="7742c-115">Once the process is finished you will have a class similar to the following generated</span></span>

![Visualizzazioni generate](~/ef6/media/generatedviews.png)

<span data-ttu-id="7742c-117">A questo punto, quando si esegue l'applicazione EF utilizzerà questa classe per caricare le visualizzazioni come richiesto.</span><span class="sxs-lookup"><span data-stu-id="7742c-117">Now when you run your application EF will use this class to load views as required.</span></span> <span data-ttu-id="7742c-118">Se il modello viene modificato e non si genera di nuovo questa classe, EF genererà un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="7742c-118">If your model changes and you do not re-generate this class then EF will throw an exception.</span></span>

## <a name="generating-mapping-views-from-code---ef6-onwards"></a><span data-ttu-id="7742c-119">Generazione di viste di mapping dal codice EF6 e versioni successive</span><span class="sxs-lookup"><span data-stu-id="7742c-119">Generating Mapping Views from Code - EF6 Onwards</span></span>

<span data-ttu-id="7742c-120">L'altro modo per generare le visualizzazioni consiste nell'usare le API fornite da EF.</span><span class="sxs-lookup"><span data-stu-id="7742c-120">The other way to generate views is to use the APIs that EF provides.</span></span> <span data-ttu-id="7742c-121">Quando si usa questo metodo, è possibile serializzare le visualizzazioni, ma è necessario anche caricare le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7742c-121">When using this method you have the freedom to serialize the views however you like, but you also need to load the views yourself.</span></span>

> [!NOTE]
> <span data-ttu-id="7742c-122">**Solo EF6 e versioni successive** : le API illustrate in questa sezione sono state introdotte nella Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="7742c-122">**EF6 Onwards Only** - The APIs shown in this section were introduced in Entity Framework 6.</span></span> <span data-ttu-id="7742c-123">Se si usa una versione precedente, queste informazioni non sono valide.</span><span class="sxs-lookup"><span data-stu-id="7742c-123">If you are using an earlier version this information does not apply.</span></span>

### <a name="generating-views"></a><span data-ttu-id="7742c-124">Generazione di visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="7742c-124">Generating Views</span></span>

<span data-ttu-id="7742c-125">Le API per generare le visualizzazioni si trovano nella classe System. Data. Entity. Core. mapping. StorageMappingItemCollection.</span><span class="sxs-lookup"><span data-stu-id="7742c-125">The APIs to generate views are on the System.Data.Entity.Core.Mapping.StorageMappingItemCollection class.</span></span> <span data-ttu-id="7742c-126">È possibile recuperare un StorageMappingCollection per un contesto usando l'elemento MetadataWorkspace di un ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="7742c-126">You can retrieve a StorageMappingCollection for a Context by using the MetadataWorkspace of an ObjectContext.</span></span> <span data-ttu-id="7742c-127">Se si usa l'API DbContext più recente, è possibile accedere a questa operazione usando IObjectContextAdapter come indicato di seguito, in questo codice è presente un'istanza del DbContext derivato denominato dbContext:</span><span class="sxs-lookup"><span data-stu-id="7742c-127">If you are using the newer DbContext API then you can access this by using the IObjectContextAdapter like below, in this code we have an instance of your derived DbContext called dbContext:</span></span>

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

<span data-ttu-id="7742c-128">Dopo aver creato il StorageMappingItemCollection, è possibile ottenere l'accesso ai metodi GenerateViews e ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="7742c-128">Once you have the StorageMappingItemCollection then you can get access to the GenerateViews and ComputeMappingHashValue methods.</span></span>

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

<span data-ttu-id="7742c-129">Il primo metodo crea un dizionario con una voce per ogni visualizzazione nel mapping del contenitore.</span><span class="sxs-lookup"><span data-stu-id="7742c-129">The first method creates a dictionary with an entry for each view in the container mapping.</span></span> <span data-ttu-id="7742c-130">Il secondo metodo calcola un valore hash per il mapping del singolo contenitore e viene usato in fase di esecuzione per convalidare che il modello non è stato modificato dopo la pre-generazione delle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7742c-130">The second method computes a hash value for the single container mapping and is used at runtime to validate that the model has not changed since the views were pre-generated.</span></span> <span data-ttu-id="7742c-131">Gli override dei due metodi vengono forniti per scenari complessi che coinvolgono più mapping di contenitori.</span><span class="sxs-lookup"><span data-stu-id="7742c-131">Overrides of the two methods are provided for complex scenarios involving multiple container mappings.</span></span>

<span data-ttu-id="7742c-132">Quando si generano visualizzazioni, si chiamerà il Metodo GenerateViews e quindi si scriveranno il EntitySetBase e DbMappingView risultante.</span><span class="sxs-lookup"><span data-stu-id="7742c-132">When generating views you will call the GenerateViews method and then write out the resulting EntitySetBase and DbMappingView.</span></span> <span data-ttu-id="7742c-133">Sarà inoltre necessario archiviare l'hash generato dal metodo ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="7742c-133">You will also need to store the hash generated by the ComputeMappingHashValue method.</span></span>

### <a name="loading-views"></a><span data-ttu-id="7742c-134">Caricamento delle visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="7742c-134">Loading Views</span></span>

<span data-ttu-id="7742c-135">Per caricare le visualizzazioni generate dal Metodo GenerateViews, è possibile fornire a EF una classe che eredita dalla classe astratta DbMappingViewCache.</span><span class="sxs-lookup"><span data-stu-id="7742c-135">In order to load the views generated by the GenerateViews method, you can provide EF with a class that inherits from the DbMappingViewCache abstract class.</span></span> <span data-ttu-id="7742c-136">DbMappingViewCache specifica due metodi che è necessario implementare:</span><span class="sxs-lookup"><span data-stu-id="7742c-136">DbMappingViewCache specifies two methods that you must implement:</span></span>

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

<span data-ttu-id="7742c-137">La proprietà MappingHashValue deve restituire l'hash generato dal metodo ComputeMappingHashValue.</span><span class="sxs-lookup"><span data-stu-id="7742c-137">The MappingHashValue property must return the hash generated by the ComputeMappingHashValue method.</span></span> <span data-ttu-id="7742c-138">Quando EF chiederà le visualizzazioni, genera innanzitutto e confronta il valore hash del modello con l'hash restituito da questa proprietà.</span><span class="sxs-lookup"><span data-stu-id="7742c-138">When EF is going to ask for views it will first generate and compare the hash value of the model with the hash returned by this property.</span></span> <span data-ttu-id="7742c-139">Se non corrispondono, EF genererà un'eccezione EntityCommandCompilationException.</span><span class="sxs-lookup"><span data-stu-id="7742c-139">If they do not match then EF will throw an EntityCommandCompilationException exception.</span></span>

<span data-ttu-id="7742c-140">Il metodo GetView accetterà un EntitySetBase ed è necessario restituire un DbMappingVIew contenente il EntitySql generato per associato al EntitySetBase specificato nel dizionario generato dal Metodo GenerateViews.</span><span class="sxs-lookup"><span data-stu-id="7742c-140">The GetView method will accept an EntitySetBase and you need to return a DbMappingVIew containing the EntitySql that was generated for that was associated with the given EntitySetBase in the dictionary generated by the GenerateViews method.</span></span> <span data-ttu-id="7742c-141">Se EF chiede una visualizzazione che non è presente, GetView deve restituire null.</span><span class="sxs-lookup"><span data-stu-id="7742c-141">If EF asks for a view that you do not have then GetView should return null.</span></span>

<span data-ttu-id="7742c-142">Di seguito è riportato un estratto dal DbMappingViewCache generato con Power Tools, come descritto in precedenza, in cui viene visualizzato un modo per archiviare e recuperare il EntitySql obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="7742c-142">The following is an extract from the DbMappingViewCache that is generated with the Power Tools as described above, in it we see one way to store and retrieve the EntitySql required.</span></span>

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

<span data-ttu-id="7742c-143">Per fare in modo che EF usi il DbMappingViewCache, Aggiungi usare DbMappingViewCacheTypeAttribute, specificando il contesto per il quale è stato creato.</span><span class="sxs-lookup"><span data-stu-id="7742c-143">To have EF use your DbMappingViewCache you add use the DbMappingViewCacheTypeAttribute, specifying the context that it was created for.</span></span> <span data-ttu-id="7742c-144">Nel codice riportato di seguito viene associato BlogContext alla classe MyMappingViewCache.</span><span class="sxs-lookup"><span data-stu-id="7742c-144">In the code below we associate the BlogContext with the MyMappingViewCache class.</span></span>

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

<span data-ttu-id="7742c-145">Per scenari più complessi, è possibile fornire istanze della cache di visualizzazione del mapping specificando una factory della cache di visualizzazione del mapping.</span><span class="sxs-lookup"><span data-stu-id="7742c-145">For more complex scenarios, mapping view cache instances can be provided by specifying a mapping view cache factory.</span></span> <span data-ttu-id="7742c-146">Questa operazione può essere eseguita implementando la classe astratta System. Data. Entity. Infrastructure. MappingViews. DbMappingViewCacheFactory.</span><span class="sxs-lookup"><span data-stu-id="7742c-146">This can be done by implementing the abstract class System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory.</span></span> <span data-ttu-id="7742c-147">L'istanza della factory della cache della vista di mapping utilizzata può essere recuperata o impostata utilizzando StorageMappingItemCollection. MappingViewCacheFactoryproperty.</span><span class="sxs-lookup"><span data-stu-id="7742c-147">The instance of the mapping view cache factory that is used can be retrieved or set using the StorageMappingItemCollection.MappingViewCacheFactoryproperty.</span></span>
