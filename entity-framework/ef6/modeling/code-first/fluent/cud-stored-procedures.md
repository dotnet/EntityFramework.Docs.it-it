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
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Stored procedure di inserimento, aggiornamento ed eliminazione di Code First
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Per impostazione predefinita, Code First configurerà tutte le entità per eseguire i comandi di inserimento, aggiornamento ed eliminazione mediante l'accesso diretto alle tabelle. A partire da EF6 è possibile configurare il modello di Code First per l'utilizzo di stored procedure per alcune o tutte le entità nel modello.  

## <a name="basic-entity-mapping"></a>Mapping di entità di base  

È possibile scegliere di usare le stored procedure per l'inserimento, l'aggiornamento e l'eliminazione usando l'API Fluent.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

In questo modo Code First utilizzerà alcune convenzioni per compilare la forma prevista delle stored procedure nel database.  

- Tre stored procedure denominate ** \<type_name\> _Insert**, ** \<type_name\> _update** e ** \<type_name\> _Delete** (ad esempio, Blog_Insert, Blog_Update e Blog_Delete).  
- I nomi dei parametri corrispondono ai nomi di proprietà.  
  > [!NOTE]
  > Se si usa HasColumnName () o l'attributo Column per rinominare la colonna per una determinata proprietà, questo nome viene usato per i parametri anziché il nome della proprietà.  
- **Il stored procedure di inserimento** avrà un parametro per ogni proprietà, ad eccezione di quelle contrassegnate come generate dall'archivio (identità o calcolata). Il stored procedure deve restituire un set di risultati con una colonna per ogni proprietà generata dall'archivio.  
- **Il stored procedure di aggiornamento** disporrà di un parametro per ogni proprietà, ad eccezione di quelli contrassegnati con un modello generato da un archivio di "calcolato". Alcuni token di concorrenza richiedono un parametro per il valore originale. per informazioni dettagliate, vedere la sezione *token di concorrenza* riportata di seguito. Il stored procedure deve restituire un set di risultati con una colonna per ogni proprietà calcolata.  
- **Il stored procedure DELETE** deve avere un parametro per il valore della chiave dell'entità (o più parametri se l'entità ha una chiave composta). Inoltre, la procedura DELETE deve disporre di parametri per tutte le chiavi esterne di associazione indipendenti nella tabella di destinazione (le relazioni che non dispongono di proprietà di chiave esterna corrispondenti dichiarate nell'entità). Alcuni token di concorrenza richiedono un parametro per il valore originale. per informazioni dettagliate, vedere la sezione *token di concorrenza* riportata di seguito.  

Usare la classe seguente come esempio:  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

Le stored procedure predefinite sono:  

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

### <a name="overriding-the-defaults"></a>Override delle impostazioni predefinite  

È possibile eseguire l'override di parte o di tutti gli elementi configurati per impostazione predefinita.  

È possibile modificare il nome di una o più stored procedure. Questo esempio Rinomina solo l'aggiornamento stored procedure.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

In questo esempio vengono rinominate tutte e tre le stored procedure.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

In questi esempi le chiamate sono concatenate, ma è anche possibile usare la sintassi del blocco lambda.  

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

Questo esempio rinomina il parametro per la proprietà BlogId nell'aggiornamento stored procedure.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

Queste chiamate sono tutte concatenabili e componibili. Di seguito è riportato un esempio in cui vengono rinominate tutte e tre le stored procedure e i relativi parametri.  

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

È inoltre possibile modificare il nome delle colonne nel set di risultati che contiene i valori generati dal database.  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>Relazioni senza una chiave esterna nella classe (associazioni indipendenti)  

Quando una proprietà di chiave esterna è inclusa nella definizione della classe, il parametro corrispondente può essere rinominato in modo analogo a qualsiasi altra proprietà. Quando esiste una relazione senza una proprietà di chiave esterna nella classe, il nome del parametro predefinito è ** \<navigation_property_name\> _ \<primary_key_name\> **.  

Le definizioni di classe seguenti, ad esempio, comportano un Blog_BlogId parametro previsto nelle stored procedure per inserire e aggiornare i post.  

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

### <a name="overriding-the-defaults"></a>Override delle impostazioni predefinite  

È possibile modificare i parametri per le chiavi esterne che non sono incluse nella classe fornendo il percorso della proprietà della chiave primaria al Metodo Parameter.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

Se non si dispone di una proprietà di navigazione nell'entità dipendente (ad esempio Nessuna proprietà post. Blog), quindi è possibile usare il metodo di associazione per identificare l'altra entità finale della relazione e quindi configurare i parametri che corrispondono a ognuna delle proprietà chiave.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>Token di concorrenza  

È anche possibile che le stored procedure Update e Delete debbano gestire la concorrenza:  

- Se l'entità contiene token di concorrenza, il stored procedure può facoltativamente avere un parametro di output che restituisce il numero di righe aggiornate o eliminate (righe interessate). Questo parametro deve essere configurato con il metodo RowsAffectedParameter.  
Per impostazione predefinita, EF usa il valore restituito da ExecuteNonQuery per determinare il numero di righe interessate. La specifica di un parametro di output interessato da righe è utile se si esegue qualsiasi logica nel SPROC che comporterebbe il valore restituito di ExecuteNonQuery come errato (dal punto di vista di EF) al termine dell'esecuzione.  
- Per ogni token di concorrenza sarà presente un parametro denominato ** \<property_name\> _Original** , ad esempio Timestamp_Original. Viene passato il valore originale di questa proprietà, ovvero il valore quando viene eseguita una query dal database.  
    - I token di concorrenza calcolati dal database, ad esempio i timestamp, avranno solo un parametro di valore originale.  
    - Anche le proprietà non calcolate impostate come token di concorrenza avranno un parametro per il nuovo valore nella procedura di aggiornamento. Vengono utilizzate le convenzioni di denominazione già discusse per i nuovi valori. Un esempio di un token di questo tipo consiste nell'usare l'URL di un Blog come token di concorrenza, il nuovo valore è necessario perché può essere aggiornato a un nuovo valore dal codice (a differenza di un token timestamp che viene aggiornato solo dal database).  

Si tratta di una classe di esempio che Aggiorna stored procedure con un token di concorrenza timestamp.  

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

Di seguito è riportato un esempio di classe e di aggiornamento stored procedure con token di concorrenza non calcolata.  

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

### <a name="overriding-the-defaults"></a>Override delle impostazioni predefinite  

Facoltativamente, è possibile introdurre un parametro di righe interessate.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

Per i token di concorrenza calcolati del database, dove viene passato solo il valore originale, è possibile usare semplicemente il meccanismo di ridenominazione dei parametri standard per rinominare il parametro per il valore originale.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

Per i token di concorrenza non calcolati, dove vengono passati sia il valore originale che quello nuovo, è possibile usare un overload del parametro che consente di specificare un nome per ogni parametro.  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>Relazioni many-to-many  

Come esempio in questa sezione verranno usate le classi seguenti.  

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

Le relazioni many-to-many possono essere mappate alle stored procedure con la sintassi seguente.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

Se non viene fornita nessun'altra configurazione, per impostazione predefinita viene usata la forma stored procedure seguente.  

- Due stored procedure denominate ** \<type_one\> \<type_two\> _Insert** e ** \<type_one\> \<type_two\> _Delete** , ad esempio PostTag_Insert e PostTag_Delete.  
- I parametri saranno i valori chiave per ogni tipo. Nome di ogni parametro ** \<type_name\> \<property_name\> ** , ad esempio Post_PostId e Tag_TagId.

Di seguito sono riportate le stored procedure di esempio INSERT e Update.  

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

### <a name="overriding-the-defaults"></a>Override delle impostazioni predefinite  

La procedura e i nomi di parametro possono essere configurati in modo analogo alle stored procedure di entità.  

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
