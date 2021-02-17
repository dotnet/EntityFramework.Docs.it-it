---
title: Viste di mapping pre-generate-EF6
description: Viste di mapping pre-generate in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: bea0cdc59161068a8186ad2106516ba4f34910a9
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543146"
---
# <a name="pre-generated-mapping-views"></a>Viste di mapping pre-generate
Prima che l'Entity Framework possa eseguire una query o salvare le modifiche apportate all'origine dati, deve generare un set di visualizzazioni di mapping per accedere al database. Queste viste di mapping sono un set di Entity SQL istruzione che rappresentano il database in modo astratto e fanno parte dei metadati memorizzati nella cache per ogni dominio dell'applicazione. Se si creano più istanze dello stesso contesto nello stesso dominio applicazione, verranno riutilizzate le visualizzazioni di mapping dei metadati memorizzati nella cache anziché rigenerarle. Poiché la generazione della visualizzazione del mapping è una parte significativa del costo complessivo dell'esecuzione della prima query, il Entity Framework consente di pregenerare le visualizzazioni di mapping e includerle nel progetto compilato. Per ulteriori informazioni, vedere  [considerazioni sulle prestazioni (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper).

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>Generazione di visualizzazioni di mapping con EF Power Tools Community Edition

Il modo più semplice per pre-generare le visualizzazioni consiste nell'usare [EF Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition). Dopo aver installato Power Tools, sarà presente un'opzione di menu per generare le visualizzazioni, come indicato di seguito.

-   Per **Code First** modelli, fare clic con il pulsante destro del mouse sul file di codice che contiene la classe DbContext.
-   Per i modelli **EF designer** fare clic con il pulsante destro del mouse sul file edmx.

![Genera viste](~/ef6/media/generateviews.png)

Al termine del processo, sarà presente una classe simile alla seguente generata

![Visualizzazioni generate](~/ef6/media/generatedviews.png)

A questo punto, quando si esegue l'applicazione EF utilizzerà questa classe per caricare le visualizzazioni come richiesto. Se il modello viene modificato e non si genera di nuovo questa classe, EF genererà un'eccezione.

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>Generazione di viste di mapping dal codice EF6 e versioni successive

L'altro modo per generare le visualizzazioni consiste nell'usare le API fornite da EF. Quando si usa questo metodo, è possibile serializzare le visualizzazioni, ma è necessario anche caricare le visualizzazioni.

> [!NOTE]
> **Solo EF6 e versioni successive** : le API illustrate in questa sezione sono state introdotte nella Entity Framework 6. Se si usa una versione precedente, queste informazioni non sono valide.

### <a name="generating-views"></a>Generazione di visualizzazioni

Le API per generare le visualizzazioni si trovano nella classe System. Data. Entity. Core. mapping. StorageMappingItemCollection. È possibile recuperare un StorageMappingCollection per un contesto usando l'elemento MetadataWorkspace di un ObjectContext. Se si usa l'API DbContext più recente, è possibile accedere a questa operazione usando IObjectContextAdapter come indicato di seguito, in questo codice è presente un'istanza del DbContext derivato denominato dbContext:

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

Dopo aver creato il StorageMappingItemCollection, è possibile ottenere l'accesso ai metodi GenerateViews e ComputeMappingHashValue.

``` csharp
    public Dictionary<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

Il primo metodo crea un dizionario con una voce per ogni visualizzazione nel mapping del contenitore. Il secondo metodo calcola un valore hash per il mapping del singolo contenitore e viene usato in fase di esecuzione per convalidare che il modello non è stato modificato dopo la pre-generazione delle visualizzazioni. Gli override dei due metodi vengono forniti per scenari complessi che coinvolgono più mapping di contenitori.

Quando si generano visualizzazioni, si chiamerà il Metodo GenerateViews e quindi si scriveranno il EntitySetBase e DbMappingView risultante. Sarà inoltre necessario archiviare l'hash generato dal metodo ComputeMappingHashValue.

### <a name="loading-views"></a>Caricamento delle visualizzazioni

Per caricare le visualizzazioni generate dal Metodo GenerateViews, è possibile fornire a EF una classe che eredita dalla classe astratta DbMappingViewCache. DbMappingViewCache specifica due metodi che è necessario implementare:

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

La proprietà MappingHashValue deve restituire l'hash generato dal metodo ComputeMappingHashValue. Quando EF chiederà le visualizzazioni, genera innanzitutto e confronta il valore hash del modello con l'hash restituito da questa proprietà. Se non corrispondono, EF genererà un'eccezione EntityCommandCompilationException.

Il metodo GetView accetterà un EntitySetBase ed è necessario restituire un DbMappingVIew contenente il EntitySql generato per associato al EntitySetBase specificato nel dizionario generato dal Metodo GenerateViews. Se EF chiede una visualizzazione che non è presente, GetView deve restituire null.

Di seguito è riportato un estratto dal DbMappingViewCache generato con Power Tools, come descritto in precedenza, in cui viene visualizzato un modo per archiviare e recuperare il EntitySql obbligatorio.

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

Per fare in modo che EF usi il DbMappingViewCache, Aggiungi usare DbMappingViewCacheTypeAttribute, specificando il contesto per il quale è stato creato. Nel codice riportato di seguito viene associato BlogContext alla classe MyMappingViewCache.

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

Per scenari più complessi, è possibile fornire istanze della cache di visualizzazione del mapping specificando una factory della cache di visualizzazione del mapping. Questa operazione può essere eseguita implementando la classe astratta System. Data. Entity. Infrastructure. MappingViews. DbMappingViewCacheFactory. L'istanza della factory della cache della vista di mapping utilizzata può essere recuperata o impostata utilizzando StorageMappingItemCollection. MappingViewCacheFactoryproperty.
