---
title: Stored procedure con più set di risultati-EF6
description: Stored procedure con più set di risultati in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 6b213b944ca06a7ca141746d86f1127be2fd7d87
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070224"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="3709c-103">Stored procedure con più set di risultati</span><span class="sxs-lookup"><span data-stu-id="3709c-103">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="3709c-104">In alcuni casi, quando si utilizzano stored procedure è necessario restituire più di un set di risultati.</span><span class="sxs-lookup"><span data-stu-id="3709c-104">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="3709c-105">Questo scenario viene in genere usato per ridurre il numero di round trip del database necessari per comporre una singola schermata.</span><span class="sxs-lookup"><span data-stu-id="3709c-105">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="3709c-106">Prima di EF5, Entity Framework avrebbe consentito la chiamata al stored procedure ma restituirebbe solo il primo set di risultati al codice chiamante.</span><span class="sxs-lookup"><span data-stu-id="3709c-106"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="3709c-107">In questo articolo vengono illustrati due modi per accedere a più di un set di risultati da un stored procedure in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3709c-107">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="3709c-108">Uno che usa solo il codice e funziona sia con Code First sia con la finestra di progettazione di Entity Framework e uno che funziona solo con la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="3709c-108">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="3709c-109">Il supporto per gli strumenti e le API per questa operazione dovrebbe migliorare nelle future versioni di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3709c-109">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="3709c-110">Modellare</span><span class="sxs-lookup"><span data-stu-id="3709c-110">Model</span></span>

<span data-ttu-id="3709c-111">Gli esempi in questo articolo usano un modello di Blog e post di base in cui un Blog contiene molti post e un post appartiene a un singolo Blog.</span><span class="sxs-lookup"><span data-stu-id="3709c-111">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="3709c-112">Si utilizzerà un stored procedure nel database che restituisce tutti i Blog e i post, come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="3709c-112">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="3709c-113">Accesso a più set di risultati con codice</span><span class="sxs-lookup"><span data-stu-id="3709c-113">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="3709c-114">È possibile eseguire Use Code per emettere un comando SQL non elaborato per eseguire il stored procedure.</span><span class="sxs-lookup"><span data-stu-id="3709c-114">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="3709c-115">Il vantaggio di questo approccio è che funziona sia con Code First sia con la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="3709c-115">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="3709c-116">Per ottenere il funzionamento di più set di risultati, è necessario rilasciarlo all'API ObjectContext usando l'interfaccia IObjectContextAdapter.</span><span class="sxs-lookup"><span data-stu-id="3709c-116">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="3709c-117">Una volta creato un ObjectContext, è possibile usare il metodo Translate per tradurre i risultati del stored procedure in entità che possono essere rilevate e usate in EF come di consueto.</span><span class="sxs-lookup"><span data-stu-id="3709c-117">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="3709c-118">Nell'esempio di codice riportato di seguito viene illustrato questo comportamento.</span><span class="sxs-lookup"><span data-stu-id="3709c-118">The following code sample demonstrates this in action.</span></span>

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

<span data-ttu-id="3709c-119">Il metodo translate accetta il lettore ricevuto quando è stata eseguita la procedura, un nome di EntitySet e un oggetto MergeOption.</span><span class="sxs-lookup"><span data-stu-id="3709c-119">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="3709c-120">Il nome di EntitySet sarà uguale a quello della proprietà DbSet nel contesto derivato.</span><span class="sxs-lookup"><span data-stu-id="3709c-120">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="3709c-121">L'enumerazione MergeOption controlla il modo in cui i risultati vengono gestiti se la stessa entità esiste già in memoria.</span><span class="sxs-lookup"><span data-stu-id="3709c-121">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="3709c-122">Qui si scorre la raccolta di Blog prima di chiamare NextResult, questo è importante dato il codice precedente, perché il primo set di risultati deve essere utilizzato prima di passare al set di risultati successivo.</span><span class="sxs-lookup"><span data-stu-id="3709c-122">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="3709c-123">Una volta chiamati i due metodi translate, le entità Blog e post vengono rilevate da EF allo stesso modo di qualsiasi altra entità, quindi possono essere modificate o eliminate e salvate come di consueto.</span><span class="sxs-lookup"><span data-stu-id="3709c-123">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="3709c-124">EF non prende in considerazione alcun mapping quando crea entità usando il metodo Translate.</span><span class="sxs-lookup"><span data-stu-id="3709c-124">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="3709c-125">Corrisponderà semplicemente ai nomi di colonna nel set di risultati con i nomi delle proprietà nelle classi.</span><span class="sxs-lookup"><span data-stu-id="3709c-125">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="3709c-126">Se il caricamento lazy è abilitato, accedendo alla proprietà post in una delle entità del Blog, EF si connetterà al database per caricare in modo differito tutti i post, anche se sono già stati caricati.</span><span class="sxs-lookup"><span data-stu-id="3709c-126">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="3709c-127">Questo perché EF non è in grado di sapere se tutti i post sono stati caricati o se sono presenti più nel database.</span><span class="sxs-lookup"><span data-stu-id="3709c-127">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="3709c-128">Se si vuole evitare questo problema, sarà necessario disabilitare il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="3709c-128">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="3709c-129">Più set di risultati con configurato in EDMX</span><span class="sxs-lookup"><span data-stu-id="3709c-129">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="3709c-130">Per poter configurare più set di risultati in EDMX, è necessario destinare .NET Framework 4,5.</span><span class="sxs-lookup"><span data-stu-id="3709c-130">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="3709c-131">Se la destinazione è .NET 4,0, è possibile usare il metodo basato sul codice illustrato nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="3709c-131">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="3709c-132">Se si usa la finestra di progettazione di Entity Framework, è anche possibile modificare il modello in modo da conoscere i diversi set di risultati che verranno restituiti.</span><span class="sxs-lookup"><span data-stu-id="3709c-132">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="3709c-133">Un aspetto importante da tenere presente è che gli strumenti non sono più compatibili con i set di risultati, pertanto sarà necessario modificare manualmente il file edmx.</span><span class="sxs-lookup"><span data-stu-id="3709c-133">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="3709c-134">La modifica del file edmx come questa funzionerà, ma comporterà anche la convalida del modello in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3709c-134">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="3709c-135">Se quindi si convalida il modello, si otterranno sempre degli errori.</span><span class="sxs-lookup"><span data-stu-id="3709c-135">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="3709c-136">A tale scopo, è necessario aggiungere il stored procedure al modello come per una singola query del set di risultati.</span><span class="sxs-lookup"><span data-stu-id="3709c-136">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="3709c-137">Al termine di questa operazione, è necessario fare clic con il pulsante destro del mouse sul modello e selezionare **Apri con.**</span><span class="sxs-lookup"><span data-stu-id="3709c-137">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="3709c-138">quindi **XML**</span><span class="sxs-lookup"><span data-stu-id="3709c-138">then **Xml**</span></span>

    ![Apri come](~/ef6/media/openas.png)

<span data-ttu-id="3709c-140">Una volta aperto il modello come XML, è necessario eseguire i passaggi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3709c-140">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="3709c-141">Trovare il tipo complesso e l'importazione di funzioni nel modello:</span><span class="sxs-lookup"><span data-stu-id="3709c-141">Find the complex type and function import in your model:</span></span>

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   <span data-ttu-id="3709c-142">Rimuovere il tipo complesso</span><span class="sxs-lookup"><span data-stu-id="3709c-142">Remove the complex type</span></span>
-   <span data-ttu-id="3709c-143">Aggiornare l'importazione di funzioni in modo che sia mappata alle entità, in questo caso sarà simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="3709c-143">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="3709c-144">Indica al modello che l'stored procedure restituirà due raccolte, una delle voci di Blog e una delle voci post.</span><span class="sxs-lookup"><span data-stu-id="3709c-144">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="3709c-145">Trovare l'elemento di mapping della funzione:</span><span class="sxs-lookup"><span data-stu-id="3709c-145">Find the function mapping element:</span></span>

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   <span data-ttu-id="3709c-146">Sostituire il mapping dei risultati con uno per ogni entità restituita, come la seguente:</span><span class="sxs-lookup"><span data-stu-id="3709c-146">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

<span data-ttu-id="3709c-147">È anche possibile eseguire il mapping dei set di risultati a tipi complessi, ad esempio quello creato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="3709c-147">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="3709c-148">A tale scopo, è necessario creare un nuovo tipo complesso, anziché rimuoverli, e utilizzare i tipi complessi in tutti i casi in cui sono stati utilizzati i nomi di entità negli esempi precedenti.</span><span class="sxs-lookup"><span data-stu-id="3709c-148">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="3709c-149">Una volta che questi mapping sono stati modificati, è possibile salvare il modello ed eseguire il codice seguente per usare la stored procedure:</span><span class="sxs-lookup"><span data-stu-id="3709c-149">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> <span data-ttu-id="3709c-150">Se si modifica manualmente il file edmx per il modello, questo verrà sovrascritto se si rigenera mai il modello dal database.</span><span class="sxs-lookup"><span data-stu-id="3709c-150">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="3709c-151">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3709c-151">Summary</span></span>

<span data-ttu-id="3709c-152">In questo articolo sono stati illustrati due diversi metodi di accesso a più set di risultati con Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3709c-152">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="3709c-153">Entrambi sono ugualmente validi in base alla situazione e alle preferenze ed è consigliabile scegliere quello più adatto alle proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="3709c-153">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="3709c-154">Si prevede che il supporto per più set di risultati sarà migliorato nelle versioni future di Entity Framework e che l'esecuzione dei passaggi descritti in questo documento non sarà più necessaria.</span><span class="sxs-lookup"><span data-stu-id="3709c-154">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
