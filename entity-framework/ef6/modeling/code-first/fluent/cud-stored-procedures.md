---
title: Code First stored procedure INSERT, Update e Delete-EF6
description: Code First stored procedure INSERT, Update e DELETE in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 4db54d7199baa408017159e25ce79a9d70707c59
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618122"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="b769b-103">Stored procedure di inserimento, aggiornamento ed eliminazione di Code First</span><span class="sxs-lookup"><span data-stu-id="b769b-103">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="b769b-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="b769b-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="b769b-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="b769b-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="b769b-106">Per impostazione predefinita, Code First configurerà tutte le entità per eseguire i comandi di inserimento, aggiornamento ed eliminazione mediante l'accesso diretto alle tabelle.</span><span class="sxs-lookup"><span data-stu-id="b769b-106">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="b769b-107">A partire da EF6 è possibile configurare il modello di Code First per l'utilizzo di stored procedure per alcune o tutte le entità nel modello.</span><span class="sxs-lookup"><span data-stu-id="b769b-107">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="b769b-108">Mapping di entità di base</span><span class="sxs-lookup"><span data-stu-id="b769b-108">Basic Entity Mapping</span></span>  

<span data-ttu-id="b769b-109">È possibile scegliere di usare le stored procedure per l'inserimento, l'aggiornamento e l'eliminazione usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="b769b-109">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="b769b-110">In questo modo Code First utilizzerà alcune convenzioni per compilare la forma prevista delle stored procedure nel database.</span><span class="sxs-lookup"><span data-stu-id="b769b-110">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="b769b-111">Tre stored procedure denominate \*\* \<type_name\> _Insert**, \*\* \<type_name\> _update** e \*\* \<type_name\> _Delete\*\* (ad esempio, Blog_Insert, Blog_Update e Blog_Delete).</span><span class="sxs-lookup"><span data-stu-id="b769b-111">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="b769b-112">I nomi dei parametri corrispondono ai nomi di proprietà.</span><span class="sxs-lookup"><span data-stu-id="b769b-112">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="b769b-113">Se si usa HasColumnName () o l'attributo Column per rinominare la colonna per una determinata proprietà, questo nome viene usato per i parametri anziché il nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="b769b-113">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="b769b-114">**Il stored procedure di inserimento** avrà un parametro per ogni proprietà, ad eccezione di quelle contrassegnate come generate dall'archivio (identità o calcolata).</span><span class="sxs-lookup"><span data-stu-id="b769b-114">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="b769b-115">Il stored procedure deve restituire un set di risultati con una colonna per ogni proprietà generata dall'archivio.</span><span class="sxs-lookup"><span data-stu-id="b769b-115">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="b769b-116">**Il stored procedure di aggiornamento** disporrà di un parametro per ogni proprietà, ad eccezione di quelli contrassegnati con un modello generato da un archivio di "calcolato".</span><span class="sxs-lookup"><span data-stu-id="b769b-116">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="b769b-117">Alcuni token di concorrenza richiedono un parametro per il valore originale. per informazioni dettagliate, vedere la sezione *token di concorrenza* riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="b769b-117">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="b769b-118">Il stored procedure deve restituire un set di risultati con una colonna per ogni proprietà calcolata.</span><span class="sxs-lookup"><span data-stu-id="b769b-118">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="b769b-119">**Il stored procedure DELETE** deve avere un parametro per il valore della chiave dell'entità (o più parametri se l'entità ha una chiave composta).</span><span class="sxs-lookup"><span data-stu-id="b769b-119">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="b769b-120">Inoltre, la procedura DELETE deve disporre di parametri per tutte le chiavi esterne di associazione indipendenti nella tabella di destinazione (le relazioni che non dispongono di proprietà di chiave esterna corrispondenti dichiarate nell'entità).</span><span class="sxs-lookup"><span data-stu-id="b769b-120">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="b769b-121">Alcuni token di concorrenza richiedono un parametro per il valore originale. per informazioni dettagliate, vedere la sezione *token di concorrenza* riportata di seguito.</span><span class="sxs-lookup"><span data-stu-id="b769b-121">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="b769b-122">Usare la classe seguente come esempio:</span><span class="sxs-lookup"><span data-stu-id="b769b-122">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="b769b-123">Le stored procedure predefinite sono:</span><span class="sxs-lookup"><span data-stu-id="b769b-123">The default stored procedures would be:</span></span>  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="b769b-124">Override delle impostazioni predefinite</span><span class="sxs-lookup"><span data-stu-id="b769b-124">Overriding the Defaults</span></span>  

<span data-ttu-id="b769b-125">È possibile eseguire l'override di parte o di tutti gli elementi configurati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b769b-125">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="b769b-126">È possibile modificare il nome di una o più stored procedure.</span><span class="sxs-lookup"><span data-stu-id="b769b-126">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="b769b-127">Questo esempio Rinomina solo l'aggiornamento stored procedure.</span><span class="sxs-lookup"><span data-stu-id="b769b-127">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="b769b-128">In questo esempio vengono rinominate tutte e tre le stored procedure.</span><span class="sxs-lookup"><span data-stu-id="b769b-128">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="b769b-129">In questi esempi le chiamate sono concatenate, ma è anche possibile usare la sintassi del blocco lambda.</span><span class="sxs-lookup"><span data-stu-id="b769b-129">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

<span data-ttu-id="b769b-130">Questo esempio rinomina il parametro per la proprietà BlogId nell'aggiornamento stored procedure.</span><span class="sxs-lookup"><span data-stu-id="b769b-130">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="b769b-131">Queste chiamate sono tutte concatenabili e componibili.</span><span class="sxs-lookup"><span data-stu-id="b769b-131">These calls are all chainable and composable.</span></span> <span data-ttu-id="b769b-132">Di seguito è riportato un esempio in cui vengono rinominate tutte e tre le stored procedure e i relativi parametri.</span><span class="sxs-lookup"><span data-stu-id="b769b-132">Here is an example that renames all three stored procedures and their parameters.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

<span data-ttu-id="b769b-133">È inoltre possibile modificare il nome delle colonne nel set di risultati che contiene i valori generati dal database.</span><span class="sxs-lookup"><span data-stu-id="b769b-133">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="b769b-134">Relazioni senza una chiave esterna nella classe (associazioni indipendenti)</span><span class="sxs-lookup"><span data-stu-id="b769b-134">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="b769b-135">Quando una proprietà di chiave esterna è inclusa nella definizione della classe, il parametro corrispondente può essere rinominato in modo analogo a qualsiasi altra proprietà.</span><span class="sxs-lookup"><span data-stu-id="b769b-135">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="b769b-136">Quando esiste una relazione senza una proprietà di chiave esterna nella classe, il nome del parametro predefinito è \*\* \<navigation_property_name\> _ \<primary_key_name\> \*\*.</span><span class="sxs-lookup"><span data-stu-id="b769b-136">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="b769b-137">Le definizioni di classe seguenti, ad esempio, comportano un Blog_BlogId parametro previsto nelle stored procedure per inserire e aggiornare i post.</span><span class="sxs-lookup"><span data-stu-id="b769b-137">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }

  public List<Post> Posts { get; set; }  
}  

public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public Blog Blog { get; set; }  
}
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="b769b-138">Override delle impostazioni predefinite</span><span class="sxs-lookup"><span data-stu-id="b769b-138">Overriding the Defaults</span></span>  

<span data-ttu-id="b769b-139">È possibile modificare i parametri per le chiavi esterne che non sono incluse nella classe fornendo il percorso della proprietà della chiave primaria al Metodo Parameter.</span><span class="sxs-lookup"><span data-stu-id="b769b-139">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="b769b-140">Se non si dispone di una proprietà di navigazione nell'entità dipendente (ad esempio</span><span class="sxs-lookup"><span data-stu-id="b769b-140">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="b769b-141">Nessuna proprietà post. Blog), quindi è possibile usare il metodo di associazione per identificare l'altra entità finale della relazione e quindi configurare i parametri che corrispondono a ognuna delle proprietà chiave.</span><span class="sxs-lookup"><span data-stu-id="b769b-141">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="b769b-142">Token di concorrenza</span><span class="sxs-lookup"><span data-stu-id="b769b-142">Concurrency Tokens</span></span>  

<span data-ttu-id="b769b-143">È anche possibile che le stored procedure Update e Delete debbano gestire la concorrenza:</span><span class="sxs-lookup"><span data-stu-id="b769b-143">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="b769b-144">Se l'entità contiene token di concorrenza, il stored procedure può facoltativamente avere un parametro di output che restituisce il numero di righe aggiornate o eliminate (righe interessate).</span><span class="sxs-lookup"><span data-stu-id="b769b-144">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="b769b-145">Questo parametro deve essere configurato con il metodo RowsAffectedParameter.</span><span class="sxs-lookup"><span data-stu-id="b769b-145">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="b769b-146">Per impostazione predefinita, EF usa il valore restituito da ExecuteNonQuery per determinare il numero di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="b769b-146">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="b769b-147">La specifica di un parametro di output interessato da righe è utile se si esegue qualsiasi logica nel SPROC che comporterebbe il valore restituito di ExecuteNonQuery come errato (dal punto di vista di EF) al termine dell'esecuzione.</span><span class="sxs-lookup"><span data-stu-id="b769b-147">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="b769b-148">Per ogni token di concorrenza sarà presente un parametro denominato \*\* \<property_name\> _Original\*\* , ad esempio Timestamp_Original.</span><span class="sxs-lookup"><span data-stu-id="b769b-148">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="b769b-149">Viene passato il valore originale di questa proprietà, ovvero il valore quando viene eseguita una query dal database.</span><span class="sxs-lookup"><span data-stu-id="b769b-149">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="b769b-150">I token di concorrenza calcolati dal database, ad esempio i timestamp, avranno solo un parametro di valore originale.</span><span class="sxs-lookup"><span data-stu-id="b769b-150">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="b769b-151">Anche le proprietà non calcolate impostate come token di concorrenza avranno un parametro per il nuovo valore nella procedura di aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="b769b-151">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="b769b-152">Vengono utilizzate le convenzioni di denominazione già discusse per i nuovi valori.</span><span class="sxs-lookup"><span data-stu-id="b769b-152">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="b769b-153">Un esempio di un token di questo tipo consiste nell'usare l'URL di un Blog come token di concorrenza, il nuovo valore è necessario perché può essere aggiornato a un nuovo valore dal codice (a differenza di un token timestamp che viene aggiornato solo dal database).</span><span class="sxs-lookup"><span data-stu-id="b769b-153">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="b769b-154">Si tratta di una classe di esempio che Aggiorna stored procedure con un token di concorrenza timestamp.</span><span class="sxs-lookup"><span data-stu-id="b769b-154">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

<span data-ttu-id="b769b-155">Di seguito è riportato un esempio di classe e di aggiornamento stored procedure con token di concorrenza non calcolata.</span><span class="sxs-lookup"><span data-stu-id="b769b-155">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="b769b-156">Override delle impostazioni predefinite</span><span class="sxs-lookup"><span data-stu-id="b769b-156">Overriding the Defaults</span></span>  

<span data-ttu-id="b769b-157">Facoltativamente, è possibile introdurre un parametro di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="b769b-157">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="b769b-158">Per i token di concorrenza calcolati del database, dove viene passato solo il valore originale, è possibile usare semplicemente il meccanismo di ridenominazione dei parametri standard per rinominare il parametro per il valore originale.</span><span class="sxs-lookup"><span data-stu-id="b769b-158">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="b769b-159">Per i token di concorrenza non calcolati, dove vengono passati sia il valore originale che quello nuovo, è possibile usare un overload del parametro che consente di specificare un nome per ogni parametro.</span><span class="sxs-lookup"><span data-stu-id="b769b-159">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="b769b-160">Relazioni many-to-many</span><span class="sxs-lookup"><span data-stu-id="b769b-160">Many to Many Relationships</span></span>  

<span data-ttu-id="b769b-161">Come esempio in questa sezione verranno usate le classi seguenti.</span><span class="sxs-lookup"><span data-stu-id="b769b-161">We’ll use the following classes as an example in this section.</span></span>  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

<span data-ttu-id="b769b-162">Le relazioni many-to-many possono essere mappate alle stored procedure con la sintassi seguente.</span><span class="sxs-lookup"><span data-stu-id="b769b-162">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="b769b-163">Se non viene fornita nessun'altra configurazione, per impostazione predefinita viene usata la forma stored procedure seguente.</span><span class="sxs-lookup"><span data-stu-id="b769b-163">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="b769b-164">Due stored procedure denominate \*\* \<type_one\> \<type_two\> _Insert\*\* e \*\* \<type_one\> \<type_two\> _Delete\*\* , ad esempio PostTag_Insert e PostTag_Delete.</span><span class="sxs-lookup"><span data-stu-id="b769b-164">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="b769b-165">I parametri saranno i valori chiave per ogni tipo.</span><span class="sxs-lookup"><span data-stu-id="b769b-165">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="b769b-166">Nome di ogni parametro \*\* \<type_name\> \<property_name\> \*\* , ad esempio Post_PostId e Tag_TagId.</span><span class="sxs-lookup"><span data-stu-id="b769b-166">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="b769b-167">Di seguito sono riportate le stored procedure di esempio INSERT e Update.</span><span class="sxs-lookup"><span data-stu-id="b769b-167">Here are example insert and update stored procedures.</span></span>  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="b769b-168">Override delle impostazioni predefinite</span><span class="sxs-lookup"><span data-stu-id="b769b-168">Overriding the Defaults</span></span>  

<span data-ttu-id="b769b-169">La procedura e i nomi di parametro possono essere configurati in modo analogo alle stored procedure di entità.</span><span class="sxs-lookup"><span data-stu-id="b769b-169">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
