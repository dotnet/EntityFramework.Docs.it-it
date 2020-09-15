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
# <a name="stored-procedures-with-multiple-result-sets"></a>Stored procedure con più set di risultati
In alcuni casi, quando si utilizzano stored procedure è necessario restituire più di un set di risultati. Questo scenario viene in genere usato per ridurre il numero di round trip del database necessari per comporre una singola schermata.Prima di EF5, Entity Framework avrebbe consentito la chiamata al stored procedure ma restituirebbe solo il primo set di risultati al codice chiamante.

In questo articolo vengono illustrati due modi per accedere a più di un set di risultati da un stored procedure in Entity Framework. Uno che usa solo il codice e funziona sia con Code First sia con la finestra di progettazione di Entity Framework e uno che funziona solo con la finestra di progettazione EF. Il supporto per gli strumenti e le API per questa operazione dovrebbe migliorare nelle future versioni di Entity Framework.

## <a name="model"></a>Modellare

Gli esempi in questo articolo usano un modello di Blog e post di base in cui un Blog contiene molti post e un post appartiene a un singolo Blog. Si utilizzerà un stored procedure nel database che restituisce tutti i Blog e i post, come indicato di seguito:

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>Accesso a più set di risultati con codice

È possibile eseguire Use Code per emettere un comando SQL non elaborato per eseguire il stored procedure. Il vantaggio di questo approccio è che funziona sia con Code First sia con la finestra di progettazione EF.

Per ottenere il funzionamento di più set di risultati, è necessario rilasciarlo all'API ObjectContext usando l'interfaccia IObjectContextAdapter.

Una volta creato un ObjectContext, è possibile usare il metodo Translate per tradurre i risultati del stored procedure in entità che possono essere rilevate e usate in EF come di consueto. Nell'esempio di codice riportato di seguito viene illustrato questo comportamento.

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

Il metodo translate accetta il lettore ricevuto quando è stata eseguita la procedura, un nome di EntitySet e un oggetto MergeOption. Il nome di EntitySet sarà uguale a quello della proprietà DbSet nel contesto derivato. L'enumerazione MergeOption controlla il modo in cui i risultati vengono gestiti se la stessa entità esiste già in memoria.

Qui si scorre la raccolta di Blog prima di chiamare NextResult, questo è importante dato il codice precedente, perché il primo set di risultati deve essere utilizzato prima di passare al set di risultati successivo.

Una volta chiamati i due metodi translate, le entità Blog e post vengono rilevate da EF allo stesso modo di qualsiasi altra entità, quindi possono essere modificate o eliminate e salvate come di consueto.

>[!NOTE]
> EF non prende in considerazione alcun mapping quando crea entità usando il metodo Translate. Corrisponderà semplicemente ai nomi di colonna nel set di risultati con i nomi delle proprietà nelle classi.

>[!NOTE]
> Se il caricamento lazy è abilitato, accedendo alla proprietà post in una delle entità del Blog, EF si connetterà al database per caricare in modo differito tutti i post, anche se sono già stati caricati. Questo perché EF non è in grado di sapere se tutti i post sono stati caricati o se sono presenti più nel database. Se si vuole evitare questo problema, sarà necessario disabilitare il caricamento lazy.

## <a name="multiple-result-sets-with-configured-in-edmx"></a>Più set di risultati con configurato in EDMX

>[!NOTE]
> Per poter configurare più set di risultati in EDMX, è necessario destinare .NET Framework 4,5. Se la destinazione è .NET 4,0, è possibile usare il metodo basato sul codice illustrato nella sezione precedente.

Se si usa la finestra di progettazione di Entity Framework, è anche possibile modificare il modello in modo da conoscere i diversi set di risultati che verranno restituiti. Un aspetto importante da tenere presente è che gli strumenti non sono più compatibili con i set di risultati, pertanto sarà necessario modificare manualmente il file edmx. La modifica del file edmx come questa funzionerà, ma comporterà anche la convalida del modello in Visual Studio. Se quindi si convalida il modello, si otterranno sempre degli errori.

-   A tale scopo, è necessario aggiungere il stored procedure al modello come per una singola query del set di risultati.
-   Al termine di questa operazione, è necessario fare clic con il pulsante destro del mouse sul modello e selezionare **Apri con.** quindi **XML**

    ![Apri come](~/ef6/media/openas.png)

Una volta aperto il modello come XML, è necessario eseguire i passaggi seguenti:

-   Trovare il tipo complesso e l'importazione di funzioni nel modello:

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

 

-   Rimuovere il tipo complesso
-   Aggiornare l'importazione di funzioni in modo che sia mappata alle entità, in questo caso sarà simile alla seguente:

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

Indica al modello che l'stored procedure restituirà due raccolte, una delle voci di Blog e una delle voci post.

-   Trovare l'elemento di mapping della funzione:

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

-   Sostituire il mapping dei risultati con uno per ogni entità restituita, come la seguente:

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

È anche possibile eseguire il mapping dei set di risultati a tipi complessi, ad esempio quello creato per impostazione predefinita. A tale scopo, è necessario creare un nuovo tipo complesso, anziché rimuoverli, e utilizzare i tipi complessi in tutti i casi in cui sono stati utilizzati i nomi di entità negli esempi precedenti.

Una volta che questi mapping sono stati modificati, è possibile salvare il modello ed eseguire il codice seguente per usare la stored procedure:

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
> Se si modifica manualmente il file edmx per il modello, questo verrà sovrascritto se si rigenera mai il modello dal database.

## <a name="summary"></a>Riepilogo

In questo articolo sono stati illustrati due diversi metodi di accesso a più set di risultati con Entity Framework. Entrambi sono ugualmente validi in base alla situazione e alle preferenze ed è consigliabile scegliere quello più adatto alle proprie esigenze. Si prevede che il supporto per più set di risultati sarà migliorato nelle versioni future di Entity Framework e che l'esecuzione dei passaggi descritti in questo documento non sarà più necessaria.
