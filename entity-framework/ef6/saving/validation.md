---
title: Convalida-EF6
description: Convalida in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 29f5e22d7f2e388d6e8271a35d10bbf6e318eafa
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073691"
---
# <a name="data-validation"></a><span data-ttu-id="3e470-103">Convalida dei dati</span><span class="sxs-lookup"><span data-stu-id="3e470-103">Data Validation</span></span>
> [!NOTE]
> <span data-ttu-id="3e470-104">**EF 4.1 solo e versioni successive** : le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 4,1.</span><span class="sxs-lookup"><span data-stu-id="3e470-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="3e470-105">Se si usa una versione precedente, alcune o tutte le informazioni non sono valide</span><span class="sxs-lookup"><span data-stu-id="3e470-105">If you are using an earlier version, some or all of the information does not apply</span></span>

<span data-ttu-id="3e470-106">Il contenuto di questa pagina è stato adattato da un articolo scritto in origine da Julie Lerman ( [https://thedatafarm.com](https://thedatafarm.com) ).</span><span class="sxs-lookup"><span data-stu-id="3e470-106">The content on this page is adapted from an article originally written by Julie Lerman ([https://thedatafarm.com](https://thedatafarm.com)).</span></span>

<span data-ttu-id="3e470-107">Entity Framework offre un'ampia gamma di funzionalità di convalida che consentono di passare a un'interfaccia utente per la convalida sul lato client o per la convalida sul lato server.</span><span class="sxs-lookup"><span data-stu-id="3e470-107">Entity Framework provides a great variety of validation features that can feed through to a user interface for client-side validation or be used for server-side validation.</span></span> <span data-ttu-id="3e470-108">Quando si usa Code First, è possibile specificare convalide usando un'annotazione o configurazioni API Fluent.</span><span class="sxs-lookup"><span data-stu-id="3e470-108">When using code first, you can specify validations using annotation or fluent API configurations.</span></span> <span data-ttu-id="3e470-109">Le convalide aggiuntive, e più complesse, possono essere specificate nel codice e funzionano indipendentemente dal fatto che il modello sia in primo luogo, prima del modello o del database.</span><span class="sxs-lookup"><span data-stu-id="3e470-109">Additional validations, and more complex, can be specified in code and will work whether your model hails from code first, model first or database first.</span></span>

## <a name="the-model"></a><span data-ttu-id="3e470-110">Il modello</span><span class="sxs-lookup"><span data-stu-id="3e470-110">The model</span></span>

<span data-ttu-id="3e470-111">Illustrerò le convalide con una semplice coppia di classi: Blog e post.</span><span class="sxs-lookup"><span data-stu-id="3e470-111">I'll demonstrate the validations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public DateTime DateCreated { get; set; }
    public string Content { get; set; }
    public int BlogId { get; set; }
    public ICollection<Comment> Comments { get; set; }
}
```

## <a name="data-annotations"></a><span data-ttu-id="3e470-112">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="3e470-112">Data Annotations</span></span>

<span data-ttu-id="3e470-113">Code First utilizza le annotazioni dell' `System.ComponentModel.DataAnnotations` assembly come mezzo per la configurazione delle classi code first.</span><span class="sxs-lookup"><span data-stu-id="3e470-113">Code First uses annotations from the `System.ComponentModel.DataAnnotations` assembly as one means of configuring code first classes.</span></span> <span data-ttu-id="3e470-114">Tra queste annotazioni sono disponibili regole quali `Required` , `MaxLength` e `MinLength` .</span><span class="sxs-lookup"><span data-stu-id="3e470-114">Among these annotations are those which provide rules such as the `Required`, `MaxLength` and `MinLength`.</span></span> <span data-ttu-id="3e470-115">Alcune applicazioni client .NET riconoscono anche queste annotazioni, ad esempio ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="3e470-115">A number of .NET client applications also recognize these annotations, for example, ASP.NET MVC.</span></span> <span data-ttu-id="3e470-116">Con queste annotazioni è possibile ottenere la convalida lato client e lato server.</span><span class="sxs-lookup"><span data-stu-id="3e470-116">You can achieve both client side and server side validation with these annotations.</span></span> <span data-ttu-id="3e470-117">Ad esempio, è possibile forzare la proprietà del titolo del Blog come una proprietà obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="3e470-117">For example, you can force the Blog Title property to be a required property.</span></span>

``` csharp
[Required]
public string Title { get; set; }
```

<span data-ttu-id="3e470-118">Senza modifiche al codice o al markup aggiuntive nell'applicazione, un'applicazione MVC esistente eseguirà la convalida lato client, anche creando dinamicamente un messaggio usando i nomi di proprietà e annotazione.</span><span class="sxs-lookup"><span data-stu-id="3e470-118">With no additional code or markup changes in the application, an existing MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![Figura 1](~/ef6/media/figure01.png)

<span data-ttu-id="3e470-120">Nel metodo di postback di questa visualizzazione di creazione, Entity Framework viene usato per salvare il nuovo blog nel database, ma la convalida lato client di MVC viene attivata prima che l'applicazione raggiunga tale codice.</span><span class="sxs-lookup"><span data-stu-id="3e470-120">In the post back method of this Create view, Entity Framework is used to save the new blog to the database, but MVC's client-side validation is triggered before the application reaches that code.</span></span>

<span data-ttu-id="3e470-121">La convalida lato client non è tuttavia a prova di Bullet.</span><span class="sxs-lookup"><span data-stu-id="3e470-121">Client side validation is not bullet-proof however.</span></span> <span data-ttu-id="3e470-122">Gli utenti possono indurre le funzionalità del browser o peggio ancora, un pirata informatico può usare alcuni espedienti per evitare le convalide dell'interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="3e470-122">Users can impact features of their browser or worse yet, a hacker might use some trickery to avoid the UI validations.</span></span> <span data-ttu-id="3e470-123">Tuttavia Entity Framework rileverà anche l' `Required` annotazione e la convaliderà.</span><span class="sxs-lookup"><span data-stu-id="3e470-123">But Entity Framework will also recognize the `Required` annotation and validate it.</span></span>

<span data-ttu-id="3e470-124">Un modo semplice per verificare questo problema è disabilitare la funzionalità di convalida lato client di MVC.</span><span class="sxs-lookup"><span data-stu-id="3e470-124">A simple way to test this is to disable MVC's client-side validation feature.</span></span> <span data-ttu-id="3e470-125">È possibile eseguire questa operazione nel file di web.config dell'applicazione MVC.</span><span class="sxs-lookup"><span data-stu-id="3e470-125">You can do this in the MVC application's web.config file.</span></span> <span data-ttu-id="3e470-126">La sezione appSettings contiene una chiave per ClientValidationEnabled.</span><span class="sxs-lookup"><span data-stu-id="3e470-126">The appSettings section has a key for ClientValidationEnabled.</span></span> <span data-ttu-id="3e470-127">Se si imposta questa chiave su false, l'interfaccia utente non potrà eseguire convalide.</span><span class="sxs-lookup"><span data-stu-id="3e470-127">Setting this key to false will prevent the UI from performing validations.</span></span>

``` xml
<appSettings>
    <add key="ClientValidationEnabled"value="false"/>
    ...
</appSettings>
```

<span data-ttu-id="3e470-128">Anche se la convalida lato client è disabilitata, si otterrà la stessa risposta nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3e470-128">Even with the client-side validation disabled, you will get the same response in your application.</span></span> <span data-ttu-id="3e470-129">Il messaggio di errore "il campo titolo è obbligatorio" verrà visualizzato come prima.</span><span class="sxs-lookup"><span data-stu-id="3e470-129">The error message "The Title field is required" will be displayed as before.</span></span> <span data-ttu-id="3e470-130">Eccetto ora, sarà il risultato della convalida sul lato server.</span><span class="sxs-lookup"><span data-stu-id="3e470-130">Except now it will be a result of server-side validation.</span></span> <span data-ttu-id="3e470-131">Entity Framework eseguirà la convalida sull' `Required` annotazione (prima che venga creato un `INSERT` comando da inviare al database) e restituisca l'errore a MVC che visualizzerà il messaggio.</span><span class="sxs-lookup"><span data-stu-id="3e470-131">Entity Framework will perform the validation on the `Required` annotation (before it even bothers to build an `INSERT` command to send to the database) and return the error to MVC which will display the message.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="3e470-132">API Fluent</span><span class="sxs-lookup"><span data-stu-id="3e470-132">Fluent API</span></span>

<span data-ttu-id="3e470-133">È possibile usare l'API Fluent Code First anziché le annotazioni per ottenere lo stesso lato client & convalida lato server.</span><span class="sxs-lookup"><span data-stu-id="3e470-133">You can use code first's fluent API instead of annotations to get the same client side & server side validation.</span></span> <span data-ttu-id="3e470-134">Invece di usare `Required` , vi mostrerò questa operazione usando una convalida MaxLength.</span><span class="sxs-lookup"><span data-stu-id="3e470-134">Rather than use `Required`, I'll show you this using a MaxLength validation.</span></span>

<span data-ttu-id="3e470-135">Le configurazioni API Fluent vengono applicate come Code First creando il modello dalle classi.</span><span class="sxs-lookup"><span data-stu-id="3e470-135">Fluent API configurations are applied as code first is building the model from the classes.</span></span> <span data-ttu-id="3e470-136">È possibile inserire le configurazioni eseguendo l'override del metodo OnModelCreating della classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="3e470-136">You can inject the configurations by overriding the DbContext class' OnModelCreating  method.</span></span> <span data-ttu-id="3e470-137">Di seguito è riportato un esempio di configurazione che specifica che la proprietà BloggerName non può contenere più di 10 caratteri.</span><span class="sxs-lookup"><span data-stu-id="3e470-137">Here is a configuration specifying that the BloggerName property can be no longer than 10 characters.</span></span>

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(p => p.BloggerName).HasMaxLength(10);
    }
}
```

<span data-ttu-id="3e470-138">Gli errori di convalida generati in base alle configurazioni dell'API Fluent non raggiungeranno automaticamente l'interfaccia utente, ma è possibile acquisirli nel codice e quindi rispondere di conseguenza.</span><span class="sxs-lookup"><span data-stu-id="3e470-138">Validation errors thrown based on the Fluent API configurations will not automatically reach the UI, but you can capture it in code and then respond to it accordingly.</span></span>

<span data-ttu-id="3e470-139">Di seguito è riportato un codice di errore di gestione delle eccezioni nella classe BlogController dell'applicazione che acquisisce l'errore di convalida quando Entity Framework tenta di salvare un Blog con un BloggerName che supera il limite massimo di 10 caratteri.</span><span class="sxs-lookup"><span data-stu-id="3e470-139">Here's some exception handling error code in the application's BlogController class that captures that validation error when Entity Framework attempts to save a blog with a BloggerName that exceeds the 10 character maximum.</span></span>

``` csharp
[HttpPost]
public ActionResult Edit(int id, Blog blog)
{
    try
    {
        db.Entry(blog).State = EntityState.Modified;
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    catch (DbEntityValidationException ex)
    {
        var error = ex.EntityValidationErrors.First().ValidationErrors.First();
        this.ModelState.AddModelError(error.PropertyName, error.ErrorMessage);
        return View();
    }
}
```

<span data-ttu-id="3e470-140">La convalida non viene restituita automaticamente nella visualizzazione ed è per questo motivo che viene usato il codice aggiuntivo che usa `ModelState.AddModelError` .</span><span class="sxs-lookup"><span data-stu-id="3e470-140">The validation doesn't automatically get passed back into the view which is why the additional code that uses `ModelState.AddModelError` is being used.</span></span> <span data-ttu-id="3e470-141">In questo modo si garantisce che i dettagli dell'errore lo rendano nella visualizzazione che utilizzerà quindi il `ValidationMessageFor` htmlHelper per visualizzare l'errore.</span><span class="sxs-lookup"><span data-stu-id="3e470-141">This ensures that the error details make it to the view which will then use the `ValidationMessageFor` Htmlhelper to display the error.</span></span>

``` csharp
@Html.ValidationMessageFor(model => model.BloggerName)
```

## <a name="ivalidatableobject"></a><span data-ttu-id="3e470-142">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="3e470-142">IValidatableObject</span></span>

<span data-ttu-id="3e470-143">`IValidatableObject` è un'interfaccia che risiede in `System.ComponentModel.DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="3e470-143">`IValidatableObject` is an interface that lives in `System.ComponentModel.DataAnnotations`.</span></span> <span data-ttu-id="3e470-144">Sebbene non faccia parte dell'API Entity Framework, è comunque possibile usarla per la convalida lato server nelle classi Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3e470-144">While it is not part of the Entity Framework API, you can still leverage it for server-side validation in your Entity Framework classes.</span></span> <span data-ttu-id="3e470-145">`IValidatableObject` fornisce un `Validate` metodo che Entity Framework chiamerà durante SaveChanges oppure è possibile chiamarsi in qualsiasi momento in cui si desidera convalidare le classi.</span><span class="sxs-lookup"><span data-stu-id="3e470-145">`IValidatableObject` provides a `Validate` method that Entity Framework will call during SaveChanges or you can call yourself any time you want to validate the classes.</span></span>

<span data-ttu-id="3e470-146">Configurazioni come `Required` ed `MaxLength` eseguono la convalida su un singolo campo.</span><span class="sxs-lookup"><span data-stu-id="3e470-146">Configurations such as `Required` and `MaxLength` perform validation on a single field.</span></span> <span data-ttu-id="3e470-147">Nel `Validate` metodo è possibile avere una logica ancora più complessa, ad esempio confrontando due campi.</span><span class="sxs-lookup"><span data-stu-id="3e470-147">In the `Validate` method you can have even more complex logic, for example, comparing two fields.</span></span>

<span data-ttu-id="3e470-148">Nell'esempio seguente, la `Blog` classe è stata estesa per implementare `IValidatableObject` e quindi fornire una regola in base alla quale `Title` e `BloggerName` non possono corrispondere.</span><span class="sxs-lookup"><span data-stu-id="3e470-148">In the following example, the `Blog` class has been extended to implement `IValidatableObject` and then provide a rule that the `Title` and `BloggerName` cannot match.</span></span>

``` csharp
public class Blog : IValidatableObject
{
    public int Id { get; set; }

    [Required]
    public string Title { get; set; }

    public string BloggerName { get; set; }
    public DateTime DateCreated { get; set; }
    public virtual ICollection<Post> Posts { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        if (Title == BloggerName)
        {
            yield return new ValidationResult(
                "Blog Title cannot match Blogger Name",
                new[] { nameof(Title), nameof(BloggerName) });
        }
    }
}
```

<span data-ttu-id="3e470-149">Il `ValidationResult` costruttore accetta un oggetto `string` che rappresenta il messaggio di errore e una matrice di oggetti `string` che rappresentano i nomi dei membri associati alla convalida.</span><span class="sxs-lookup"><span data-stu-id="3e470-149">The `ValidationResult` constructor takes a `string` that represents the error message and an array of `string`s that represent the member names that are associated with the validation.</span></span> <span data-ttu-id="3e470-150">Poiché questa convalida controlla sia `Title` che e `BloggerName` , vengono restituiti entrambi i nomi di proprietà.</span><span class="sxs-lookup"><span data-stu-id="3e470-150">Since this validation checks both the `Title` and the `BloggerName`, both property names are returned.</span></span>

<span data-ttu-id="3e470-151">A differenza della convalida fornita dall'API Fluent, questo risultato di convalida verrà riconosciuto dalla vista e il gestore di eccezioni usato in precedenza per aggiungere l'errore non `ModelState` è necessario.</span><span class="sxs-lookup"><span data-stu-id="3e470-151">Unlike the validation provided by the Fluent API, this validation result will be recognized by the View and the exception handler that I used earlier to add the error into `ModelState` is unnecessary.</span></span> <span data-ttu-id="3e470-152">Poiché i nomi delle proprietà sono stati impostati in `ValidationResult` , le HtmlHelper di MVC visualizzano il messaggio di errore per entrambe le proprietà.</span><span class="sxs-lookup"><span data-stu-id="3e470-152">Because I set both property names in the `ValidationResult`, the MVC HtmlHelpers display the error message for both of those properties.</span></span>

![Figura 2](~/ef6/media/figure02.png)

## <a name="dbcontextvalidateentity"></a><span data-ttu-id="3e470-154">DbContext. ValidateEntity</span><span class="sxs-lookup"><span data-stu-id="3e470-154">DbContext.ValidateEntity</span></span>

<span data-ttu-id="3e470-155">`DbContext` dispone di un metodo sottoponibile a override chiamato `ValidateEntity` .</span><span class="sxs-lookup"><span data-stu-id="3e470-155">`DbContext` has an overridable method called `ValidateEntity`.</span></span> <span data-ttu-id="3e470-156">Quando si chiama `SaveChanges` , Entity Framework chiamerà questo metodo per ogni entità nella cache il cui stato non è `Unchanged` .</span><span class="sxs-lookup"><span data-stu-id="3e470-156">When you call `SaveChanges`, Entity Framework will call this method for each entity in its cache whose state is not `Unchanged`.</span></span> <span data-ttu-id="3e470-157">È possibile inserire la logica di convalida direttamente qui o usare questo metodo per chiamare, ad esempio, il `Blog.Validate` metodo aggiunto nella sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="3e470-157">You can put validation logic directly in here or even use this method to call, for example, the `Blog.Validate` method added in the previous section.</span></span>

<span data-ttu-id="3e470-158">Di seguito è riportato un esempio di `ValidateEntity` override che convalida `Post` i nuovi per assicurarsi che il titolo post non sia già stato utilizzato.</span><span class="sxs-lookup"><span data-stu-id="3e470-158">Here's an example of a `ValidateEntity` override that validates new `Post`s to ensure that the post title hasn't been used already.</span></span> <span data-ttu-id="3e470-159">Verifica prima di tutto se l'entità è un post e viene aggiunto il relativo stato.</span><span class="sxs-lookup"><span data-stu-id="3e470-159">It first checks to see if the entity is a post and that its state is Added.</span></span> <span data-ttu-id="3e470-160">In tal caso, viene eseguita una ricerca nel database per verificare se è già presente un post con lo stesso titolo.</span><span class="sxs-lookup"><span data-stu-id="3e470-160">If that's the case, then it looks in the database to see if there is already a post with the same title.</span></span> <span data-ttu-id="3e470-161">Se è già presente un post esistente, viene creato un nuovo oggetto `DbEntityValidationResult` .</span><span class="sxs-lookup"><span data-stu-id="3e470-161">If there is an existing post already, then a new `DbEntityValidationResult` is created.</span></span>

<span data-ttu-id="3e470-162">`DbEntityValidationResult` ospita un oggetto `DbEntityEntry` e un oggetto `ICollection<DbValidationErrors>` per una singola entità.</span><span class="sxs-lookup"><span data-stu-id="3e470-162">`DbEntityValidationResult` houses a `DbEntityEntry` and an `ICollection<DbValidationErrors>` for a single entity.</span></span> <span data-ttu-id="3e470-163">All'inizio di questo metodo, viene creata un'istanza di un oggetto `DbEntityValidationResult` e quindi tutti gli errori individuati vengono aggiunti alla relativa `ValidationErrors` raccolta.</span><span class="sxs-lookup"><span data-stu-id="3e470-163">At the start of this method, a `DbEntityValidationResult` is instantiated and then any errors that are discovered are added into its `ValidationErrors` collection.</span></span>

``` csharp
protected override DbEntityValidationResult ValidateEntity (
    System.Data.Entity.Infrastructure.DbEntityEntry entityEntry,
    IDictionary<object, object> items)
{
    var result = new DbEntityValidationResult(entityEntry, new List<DbValidationError>());

    if (entityEntry.Entity is Post post && entityEntry.State == EntityState.Added)
    {
        // Check for uniqueness of post title
        if (Posts.Where(p => p.Title == post.Title).Any())
        {
            result.ValidationErrors.Add(
                    new System.Data.Entity.Validation.DbValidationError(
                        nameof(Title),
                        "Post title must be unique."));
        }
    }

    if (result.ValidationErrors.Count > 0)
    {
        return result;
    }
    else
    {
        return base.ValidateEntity(entityEntry, items);
    }
}
```

## <a name="explicitly-triggering-validation"></a><span data-ttu-id="3e470-164">Attivazione esplicita della convalida</span><span class="sxs-lookup"><span data-stu-id="3e470-164">Explicitly triggering validation</span></span>

<span data-ttu-id="3e470-165">Una chiamata a `SaveChanges` attiva tutte le convalide descritte in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="3e470-165">A call to `SaveChanges` triggers all of the validations covered in this article.</span></span> <span data-ttu-id="3e470-166">Ma non è necessario basarsi su `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="3e470-166">But you don't need to rely on `SaveChanges`.</span></span> <span data-ttu-id="3e470-167">È possibile che si preferisca convalidare un'altra posizione nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="3e470-167">You may prefer to validate elsewhere in your application.</span></span>

<span data-ttu-id="3e470-168">`DbContext.GetValidationErrors` attiverà tutte le convalide, quelle definite dalle annotazioni o l'API Fluent, la convalida creata in `IValidatableObject` (ad esempio, `Blog.Validate` ) e le convalide eseguite nel `DbContext.ValidateEntity` metodo.</span><span class="sxs-lookup"><span data-stu-id="3e470-168">`DbContext.GetValidationErrors` will trigger all of the validations, those defined by annotations or the Fluent API, the validation created in `IValidatableObject` (for example, `Blog.Validate`), and the validations performed in the `DbContext.ValidateEntity` method.</span></span>

<span data-ttu-id="3e470-169">Il codice seguente chiamerà `GetValidationErrors` sull'istanza corrente di un oggetto `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="3e470-169">The following code will call `GetValidationErrors` on the current instance of a `DbContext`.</span></span> <span data-ttu-id="3e470-170">`ValidationErrors` sono raggruppati in base al tipo di entità in `DbEntityValidationResult` .</span><span class="sxs-lookup"><span data-stu-id="3e470-170">`ValidationErrors` are grouped by entity type into `DbEntityValidationResult`.</span></span> <span data-ttu-id="3e470-171">Il codice scorre prima di tutto l'oggetto `DbEntityValidationResult` restituito dal metodo e quindi attraverso ogni `DbValidationError` interno.</span><span class="sxs-lookup"><span data-stu-id="3e470-171">The code iterates first through the `DbEntityValidationResult`s returned by the method and then through each `DbValidationError` inside.</span></span>

``` csharp
foreach (var validationResult in db.GetValidationErrors())
{
    foreach (var error in validationResult.ValidationErrors)
    {
        Debug.WriteLine(
            "Entity Property: {0}, Error {1}",
            error.PropertyName,
            error.ErrorMessage);
    }
}
```

## <a name="other-considerations-when-using-validation"></a><span data-ttu-id="3e470-172">Altre considerazioni relative all'uso della convalida</span><span class="sxs-lookup"><span data-stu-id="3e470-172">Other considerations when using validation</span></span>

<span data-ttu-id="3e470-173">Di seguito sono riportati alcuni altri punti da considerare quando si usa la convalida Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="3e470-173">Here are a few other points to consider when using Entity Framework validation:</span></span>

- <span data-ttu-id="3e470-174">Il caricamento lazy è disabilitato durante la convalida</span><span class="sxs-lookup"><span data-stu-id="3e470-174">Lazy loading is disabled during validation</span></span>
- <span data-ttu-id="3e470-175">EF convaliderà le annotazioni dei dati in proprietà non mappate (proprietà di cui non è stato eseguito il mapping a una colonna nel database)</span><span class="sxs-lookup"><span data-stu-id="3e470-175">EF will validate data annotations on non-mapped properties (properties that are not mapped to a column in the database)</span></span>
- <span data-ttu-id="3e470-176">La convalida viene eseguita dopo che le modifiche vengono rilevate durante `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="3e470-176">Validation is performed after changes are detected during `SaveChanges`.</span></span> <span data-ttu-id="3e470-177">Se si apportano modifiche durante la convalida, è responsabilità dell'utente inviare una notifica a change tracker</span><span class="sxs-lookup"><span data-stu-id="3e470-177">If you make changes during validation it is your responsibility to notify the change tracker</span></span>
- <span data-ttu-id="3e470-178">`DbUnexpectedValidationException` viene generata se si verificano errori durante la convalida</span><span class="sxs-lookup"><span data-stu-id="3e470-178">`DbUnexpectedValidationException` is thrown if errors occur during validation</span></span>
- <span data-ttu-id="3e470-179">I facet che Entity Framework include nel modello (lunghezza massima, obbligatoria e così via) provocheranno la convalida, anche se non sono presenti annotazioni di dati nelle classi e/o per la creazione del modello è stata usata la finestra di progettazione EF.</span><span class="sxs-lookup"><span data-stu-id="3e470-179">Facets that Entity Framework includes in the model (maximum length, required, etc.) will cause validation, even if there are no data annotations in your classes and/or you used the EF Designer to create your model</span></span>
- <span data-ttu-id="3e470-180">Regole di precedenza:</span><span class="sxs-lookup"><span data-stu-id="3e470-180">Precedence rules:</span></span>
  - <span data-ttu-id="3e470-181">Le chiamate API Fluent eseguono l'override delle annotazioni dei dati corrispondenti</span><span class="sxs-lookup"><span data-stu-id="3e470-181">Fluent API calls override the corresponding data annotations</span></span>
- <span data-ttu-id="3e470-182">Ordine di esecuzione:</span><span class="sxs-lookup"><span data-stu-id="3e470-182">Execution order:</span></span>
  - <span data-ttu-id="3e470-183">La convalida delle proprietà si verifica prima della convalida del tipo</span><span class="sxs-lookup"><span data-stu-id="3e470-183">Property validation occurs before type validation</span></span>
  - <span data-ttu-id="3e470-184">La convalida del tipo si verifica solo se la convalida della proprietà ha esito positivo</span><span class="sxs-lookup"><span data-stu-id="3e470-184">Type validation only occurs if property validation succeeds</span></span>
- <span data-ttu-id="3e470-185">Se una proprietà è complessa, la relativa convalida includerà anche:</span><span class="sxs-lookup"><span data-stu-id="3e470-185">If a property is complex, its validation will also include:</span></span>
  - <span data-ttu-id="3e470-186">Convalida a livello di proprietà per le proprietà del tipo complesso</span><span class="sxs-lookup"><span data-stu-id="3e470-186">Property-level validation on the complex type properties</span></span>
  - <span data-ttu-id="3e470-187">Convalida a livello di tipo per il tipo complesso, inclusa `IValidatableObject` la convalida sul tipo complesso</span><span class="sxs-lookup"><span data-stu-id="3e470-187">Type level validation on the complex type, including `IValidatableObject` validation on the complex type</span></span>

## <a name="summary"></a><span data-ttu-id="3e470-188">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="3e470-188">Summary</span></span>

<span data-ttu-id="3e470-189">L'API di convalida in Entity Framework viene riprodotta molto bene con la convalida lato client in MVC, ma non è necessario basarsi sulla convalida lato client.</span><span class="sxs-lookup"><span data-stu-id="3e470-189">The validation API in Entity Framework plays very nicely with client side validation in MVC but you don't have to rely on client-side validation.</span></span> <span data-ttu-id="3e470-190">Entity Framework si occuperà della convalida sul lato server per le annotazioni o le configurazioni applicate con l'API Code First Fluent.</span><span class="sxs-lookup"><span data-stu-id="3e470-190">Entity Framework will take care of the validation on the server side for DataAnnotations or configurations you've applied with the code first Fluent API.</span></span>

<span data-ttu-id="3e470-191">È stato inoltre illustrato un numero di punti di estensibilità per personalizzare il comportamento se si usa l' `IValidatableObject` interfaccia o si tocca il `DbContext.ValidateEntity` metodo.</span><span class="sxs-lookup"><span data-stu-id="3e470-191">You also saw a number of extensibility points for customizing the behavior whether you use the `IValidatableObject` interface or tap into the `DbContext.ValidateEntity` method.</span></span> <span data-ttu-id="3e470-192">Questi ultimi due metodi di convalida sono disponibili tramite `DbContext` , indipendentemente dal fatto che si utilizzi il flusso di lavoro Code First, Model First o database First per descrivere il modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="3e470-192">And these last two means of validation are available through the `DbContext`, whether you use the Code First, Model First or Database First workflow to describe your conceptual model.</span></span>
