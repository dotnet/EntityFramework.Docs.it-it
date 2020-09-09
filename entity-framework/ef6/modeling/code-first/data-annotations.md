---
title: Annotazioni dei dati Code First-EF6
description: Annotazioni dei dati Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: 37b2872b5fb40cf0dc346554b2cbb9e3fffd6bdc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617357"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="62b26-103">Annotazioni dei dati per Code First</span><span class="sxs-lookup"><span data-stu-id="62b26-103">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="62b26-104">**EF 4.1 solo e versioni successive** : le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 4,1.</span><span class="sxs-lookup"><span data-stu-id="62b26-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="62b26-105">Se si usa una versione precedente, alcune o tutte queste informazioni non sono valide.</span><span class="sxs-lookup"><span data-stu-id="62b26-105">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="62b26-106">Il contenuto di questa pagina è stato adattato da un articolo scritto in origine da Julie Lerman ( \<http://thedatafarm.com> ).</span><span class="sxs-lookup"><span data-stu-id="62b26-106">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="62b26-107">Entity Framework Code First consente di usare le proprie classi di dominio per rappresentare il modello su cui è basato EF per eseguire query, rilevamento delle modifiche e funzioni di aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="62b26-107">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="62b26-108">Code First utilizza un modello di programmazione denominato "Convenzione sulla configurazione".</span><span class="sxs-lookup"><span data-stu-id="62b26-108">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="62b26-109">Code First presuppone che le classi seguano le convenzioni di Entity Framework e, in tal caso, verrà automaticamente illustrato come eseguire il processo.</span><span class="sxs-lookup"><span data-stu-id="62b26-109">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="62b26-110">Tuttavia, se le classi non seguono le convenzioni, è possibile aggiungere configurazioni alle classi per fornire a EF le informazioni necessarie.</span><span class="sxs-lookup"><span data-stu-id="62b26-110">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="62b26-111">Code First offre due modi per aggiungere queste configurazioni alle classi.</span><span class="sxs-lookup"><span data-stu-id="62b26-111">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="62b26-112">Uno usa semplici attributi denominati DataAnnotations e il secondo usa l'API Fluent di Code First, che consente di descrivere le configurazioni in modo imperativo nel codice.</span><span class="sxs-lookup"><span data-stu-id="62b26-112">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="62b26-113">Questo articolo è incentrato sull'uso di DataAnnotations (nello spazio dei nomi System. ComponentModel. DataAnnotations) per configurare le classi, evidenziando le configurazioni più comunemente necessarie.</span><span class="sxs-lookup"><span data-stu-id="62b26-113">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="62b26-114">Le annotazioni vengono inoltre riconosciute da diverse applicazioni .NET, ad esempio ASP.NET MVC, che consente a queste applicazioni di sfruttare le stesse annotazioni per le convalide lato client.</span><span class="sxs-lookup"><span data-stu-id="62b26-114">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="62b26-115">Il modello</span><span class="sxs-lookup"><span data-stu-id="62b26-115">The model</span></span>

<span data-ttu-id="62b26-116">Illustrerò Code First DataAnnotations con una semplice coppia di classi: Blog e post.</span><span class="sxs-lookup"><span data-stu-id="62b26-116">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
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

<span data-ttu-id="62b26-117">Così come sono, le classi di Blog e post seguono comodamente la convenzione Code First e non richiedono alcuna modifica per consentire la compatibilità di EF.</span><span class="sxs-lookup"><span data-stu-id="62b26-117">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="62b26-118">Tuttavia, è anche possibile usare le annotazioni per fornire altre informazioni a EF sulle classi e sul database a cui vengono mappate.</span><span class="sxs-lookup"><span data-stu-id="62b26-118">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="62b26-119">Chiave</span><span class="sxs-lookup"><span data-stu-id="62b26-119">Key</span></span>

<span data-ttu-id="62b26-120">Entity Framework si basa su ogni entità con un valore di chiave utilizzato per il rilevamento delle entità.</span><span class="sxs-lookup"><span data-stu-id="62b26-120">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="62b26-121">Una convenzione di Code First è proprietà chiave implicita. Code First cercherà una proprietà denominata "ID" o una combinazione di nome di classe e "ID", ad esempio "BlogId".</span><span class="sxs-lookup"><span data-stu-id="62b26-121">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="62b26-122">Questa proprietà eseguirà il mapping a una colonna chiave primaria nel database.</span><span class="sxs-lookup"><span data-stu-id="62b26-122">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="62b26-123">Il Blog e le classi post seguono entrambi questa convenzione.</span><span class="sxs-lookup"><span data-stu-id="62b26-123">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="62b26-124">Cosa accade se non lo facevano?</span><span class="sxs-lookup"><span data-stu-id="62b26-124">What if they didn’t?</span></span> <span data-ttu-id="62b26-125">Che cosa succede se il Blog usava invece il nome *PrimaryTrackingKey* o anche *foo*?</span><span class="sxs-lookup"><span data-stu-id="62b26-125">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="62b26-126">Se Code First non trova una proprietà che corrisponde a questa convenzione, verrà generata un'eccezione a causa del requisito Entity Framework che è necessario disporre di una proprietà chiave.</span><span class="sxs-lookup"><span data-stu-id="62b26-126">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="62b26-127">È possibile utilizzare l'annotazione chiave per specificare quale proprietà deve essere utilizzata come EntityKey.</span><span class="sxs-lookup"><span data-stu-id="62b26-127">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="62b26-128">Se si usa la funzionalità di generazione del database Code First, nella tabella di Blog sarà presente una colonna chiave primaria denominata PrimaryTrackingKey, definita anche come Identity per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="62b26-128">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![Tabella Blog con chiave primaria](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="62b26-130">Chiavi composte</span><span class="sxs-lookup"><span data-stu-id="62b26-130">Composite keys</span></span>

<span data-ttu-id="62b26-131">Entity Framework supporta chiavi composite-chiavi primarie costituite da più di una proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-131">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="62b26-132">Ad esempio, è possibile avere una classe Passport la cui chiave primaria è una combinazione di PassportNumber e IssuingCountry.</span><span class="sxs-lookup"><span data-stu-id="62b26-132">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="62b26-133">Il tentativo di usare la classe precedente nel modello EF comporterebbe un errore `InvalidOperationException` :</span><span class="sxs-lookup"><span data-stu-id="62b26-133">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="62b26-134">*Impossibile determinare l'ordinamento della chiave primaria composita per il tipo ' Passport '. Usare il metodo ColumnAttribute o HasKey per specificare un ordine per le chiavi primarie composte.*</span><span class="sxs-lookup"><span data-stu-id="62b26-134">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="62b26-135">Per usare le chiavi composite, Entity Framework necessario definire un ordine per le proprietà chiave.</span><span class="sxs-lookup"><span data-stu-id="62b26-135">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="62b26-136">Per eseguire questa operazione, è possibile utilizzare l'annotazione di colonna per specificare un ordine.</span><span class="sxs-lookup"><span data-stu-id="62b26-136">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="62b26-137">Il valore dell'ordine è relativo (anziché basato sull'indice), pertanto è possibile utilizzare qualsiasi valore.</span><span class="sxs-lookup"><span data-stu-id="62b26-137">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="62b26-138">Ad esempio, 100 e 200 sarebbero accettabili al posto di 1 e 2.</span><span class="sxs-lookup"><span data-stu-id="62b26-138">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="62b26-139">Se sono presenti entità con chiavi esterne composite, è necessario specificare lo stesso ordine di colonna usato per le proprietà della chiave primaria corrispondente.</span><span class="sxs-lookup"><span data-stu-id="62b26-139">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="62b26-140">Solo l'ordinamento relativo all'interno delle proprietà della chiave esterna deve essere lo stesso, non è necessario che i valori esatti assegnati a **Order** corrispondano.</span><span class="sxs-lookup"><span data-stu-id="62b26-140">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="62b26-141">Nella classe seguente, ad esempio, è possibile utilizzare 3 e 4 al posto di 1 e 2.</span><span class="sxs-lookup"><span data-stu-id="62b26-141">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="62b26-142">Necessario</span><span class="sxs-lookup"><span data-stu-id="62b26-142">Required</span></span>

<span data-ttu-id="62b26-143">L' `Required` annotazione indica a EF che è richiesta una particolare proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-143">The `Required` annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="62b26-144">L'aggiunta di required alla proprietà title forza EF (e MVC) a garantire che la proprietà disponga di dati.</span><span class="sxs-lookup"><span data-stu-id="62b26-144">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="62b26-145">Senza ulteriori modifiche al codice o al markup nell'applicazione, un'applicazione MVC eseguirà la convalida lato client, anche creando dinamicamente un messaggio utilizzando i nomi delle proprietà e delle annotazioni.</span><span class="sxs-lookup"><span data-stu-id="62b26-145">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![Errore obbligatorio creazione pagina con titolo](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="62b26-147">L'attributo obbligatorio influirà anche sul database generato rendendo non nullable la proprietà mappata.</span><span class="sxs-lookup"><span data-stu-id="62b26-147">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="62b26-148">Si noti che il campo title è stato modificato in "not null".</span><span class="sxs-lookup"><span data-stu-id="62b26-148">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="62b26-149">In alcuni casi potrebbe non essere possibile che la colonna nel database sia non nullable anche se la proprietà è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="62b26-149">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="62b26-150">Ad esempio, quando si usano i dati della strategia di ereditarietà TPH per più tipi, viene archiviato in una singola tabella.</span><span class="sxs-lookup"><span data-stu-id="62b26-150">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="62b26-151">Se un tipo derivato include una proprietà obbligatoria, la colonna non può essere resa non nullable perché non tutti i tipi nella gerarchia avranno questa proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-151">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Tabella Blog](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="62b26-153">MaxLength e MinLength</span><span class="sxs-lookup"><span data-stu-id="62b26-153">MaxLength and MinLength</span></span>

<span data-ttu-id="62b26-154">Gli `MaxLength` `MinLength` attributi e consentono di specificare convalide di proprietà aggiuntive, esattamente come è stato fatto con `Required` .</span><span class="sxs-lookup"><span data-stu-id="62b26-154">The `MaxLength` and `MinLength` attributes allow you to specify additional property validations, just as you did with `Required`.</span></span>

<span data-ttu-id="62b26-155">Ecco il BloggerName con i requisiti di lunghezza.</span><span class="sxs-lookup"><span data-stu-id="62b26-155">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="62b26-156">Nell'esempio viene inoltre illustrato come combinare gli attributi.</span><span class="sxs-lookup"><span data-stu-id="62b26-156">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="62b26-157">L'annotazione MaxLength avrà un effetto sul database impostando la lunghezza della proprietà su 10.</span><span class="sxs-lookup"><span data-stu-id="62b26-157">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Tabella Blog che mostra la lunghezza massima nella colonna BloggerName](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="62b26-159">L'annotazione lato client MVC e l'annotazione lato server EF 4,1 rispettano questa convalida, generando nuovamente in modo dinamico un messaggio di errore: "il campo BloggerName deve essere un tipo stringa o matrice con una lunghezza massima di" 10 ". Il messaggio è leggermente lungo.</span><span class="sxs-lookup"><span data-stu-id="62b26-159">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="62b26-160">Molte annotazioni consentono di specificare un messaggio di errore con l'attributo ErrorMessage.</span><span class="sxs-lookup"><span data-stu-id="62b26-160">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="62b26-161">È anche possibile specificare ErrorMessage nell'annotazione richiesta.</span><span class="sxs-lookup"><span data-stu-id="62b26-161">You can also specify ErrorMessage in the Required annotation.</span></span>

![Crea pagina con messaggio di errore personalizzato](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="62b26-163">NotMapped</span><span class="sxs-lookup"><span data-stu-id="62b26-163">NotMapped</span></span>

<span data-ttu-id="62b26-164">La convenzione Code First impone che ogni proprietà di un tipo di dati supportato venga rappresentata nel database.</span><span class="sxs-lookup"><span data-stu-id="62b26-164">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="62b26-165">Tuttavia, questo non è sempre il caso delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="62b26-165">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="62b26-166">È ad esempio possibile avere una proprietà nella classe Blog che crea un codice basato sui campi title e BloggerName.</span><span class="sxs-lookup"><span data-stu-id="62b26-166">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="62b26-167">Tale proprietà può essere creata dinamicamente e non deve essere archiviata.</span><span class="sxs-lookup"><span data-stu-id="62b26-167">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="62b26-168">È possibile contrassegnare tutte le proprietà che non eseguono il mapping al database con l'annotazione NotMapped, ad esempio questa proprietà BlogCode.</span><span class="sxs-lookup"><span data-stu-id="62b26-168">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="62b26-169">ComplexType</span><span class="sxs-lookup"><span data-stu-id="62b26-169">ComplexType</span></span>

<span data-ttu-id="62b26-170">Non è insolito descrivere le entità di dominio in un set di classi e quindi eseguire il layer di tali classi per descrivere un'entità completa.</span><span class="sxs-lookup"><span data-stu-id="62b26-170">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="62b26-171">Ad esempio, è possibile aggiungere una classe denominata BlogDetails al modello.</span><span class="sxs-lookup"><span data-stu-id="62b26-171">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="62b26-172">Si noti che non `BlogDetails` ha alcun tipo di proprietà chiave.</span><span class="sxs-lookup"><span data-stu-id="62b26-172">Notice that `BlogDetails` does not have any type of key property.</span></span> <span data-ttu-id="62b26-173">Nella progettazione basata su dominio, `BlogDetails` viene definito oggetto valore.</span><span class="sxs-lookup"><span data-stu-id="62b26-173">In domain driven design, `BlogDetails` is referred to as a value object.</span></span> <span data-ttu-id="62b26-174">Entity Framework fa riferimento a oggetti valore come tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="62b26-174">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="62b26-175">I tipi complessi non possono essere rilevati autonomamente.</span><span class="sxs-lookup"><span data-stu-id="62b26-175">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="62b26-176">Tuttavia, come proprietà nella `Blog` classe, `BlogDetails` verrà rilevata come parte di un `Blog` oggetto.</span><span class="sxs-lookup"><span data-stu-id="62b26-176">However as a property in the `Blog` class, `BlogDetails` will be tracked as part of a `Blog` object.</span></span> <span data-ttu-id="62b26-177">Per poter riconoscere il codice prima, è necessario contrassegnare la `BlogDetails` classe come `ComplexType` .</span><span class="sxs-lookup"><span data-stu-id="62b26-177">In order for code first to recognize this, you must mark the `BlogDetails` class as a `ComplexType`.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="62b26-178">A questo punto è possibile aggiungere una proprietà nella `Blog` classe per rappresentare `BlogDetails` per tale Blog.</span><span class="sxs-lookup"><span data-stu-id="62b26-178">Now you can add a property in the `Blog` class to represent the `BlogDetails` for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="62b26-179">Nel database, la tabella conterrà `Blog` tutte le proprietà del Blog, incluse le proprietà contenute nella relativa `BlogDetail` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-179">In the database, the `Blog` table will contain all of the properties of the blog including the properties contained in its `BlogDetail` property.</span></span> <span data-ttu-id="62b26-180">Per impostazione predefinita, ciascuna di esse è preceduta dal nome del tipo complesso "BlogDetail".</span><span class="sxs-lookup"><span data-stu-id="62b26-180">By default, each one is preceded with the name of the complex type, "BlogDetail".</span></span>

![Tabella Blog con tipo complesso](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="62b26-182">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="62b26-182">ConcurrencyCheck</span></span>

<span data-ttu-id="62b26-183">L' `ConcurrencyCheck` annotazione consente di contrassegnare una o più proprietà da utilizzare per il controllo della concorrenza nel database quando un utente modifica o Elimina un'entità.</span><span class="sxs-lookup"><span data-stu-id="62b26-183">The `ConcurrencyCheck` annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="62b26-184">Se si è lavorato con la finestra di progettazione di Entity Framework, questo si allinea impostando la proprietà `ConcurrencyMode` su `Fixed` .</span><span class="sxs-lookup"><span data-stu-id="62b26-184">If you've been working with the EF Designer, this aligns with setting a property's `ConcurrencyMode` to `Fixed`.</span></span>

<span data-ttu-id="62b26-185">Verrà ora illustrato `ConcurrencyCheck` il funzionamento aggiungendolo alla `BloggerName` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-185">Let’s see how `ConcurrencyCheck` works by adding it to the `BloggerName` property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="62b26-186">Quando `SaveChanges` viene chiamato il metodo, a causa dell' `ConcurrencyCheck` annotazione nel `BloggerName` campo, il valore originale di tale proprietà verrà usato nell'aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="62b26-186">When `SaveChanges` is called, because of the `ConcurrencyCheck` annotation on the `BloggerName` field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="62b26-187">Il comando tenterà di individuare la riga corretta filtrando non solo sul valore della chiave, ma anche sul valore originale di `BloggerName` .</span><span class="sxs-lookup"><span data-stu-id="62b26-187">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of `BloggerName`.</span></span><span data-ttu-id="62b26-188">Di seguito sono riportate le parti critiche del comando UPDATE inviate al database, in cui è possibile notare che il comando aggiornerà la riga che ha un `PrimaryTrackingKey` valore pari a 1 e a `BloggerName` di "Julie", che rappresenta il valore originale quando il Blog è stato recuperato dal database.</span><span class="sxs-lookup"><span data-stu-id="62b26-188">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a `PrimaryTrackingKey` is 1 and a `BloggerName` of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="62b26-189">Se nel frattempo è stato modificato il nome del blogger per quel blog, l'aggiornamento avrà esito negativo e si otterrà una **DbUpdateConcurrencyException** che è necessario gestire.</span><span class="sxs-lookup"><span data-stu-id="62b26-189">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a **DbUpdateConcurrencyException** that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="62b26-190">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="62b26-190">TimeStamp</span></span>

<span data-ttu-id="62b26-191">È più comune usare i campi rowversion o timestamp per il controllo della concorrenza.</span><span class="sxs-lookup"><span data-stu-id="62b26-191">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="62b26-192">Invece di usare l' `ConcurrencyCheck` annotazione, è possibile usare l'annotazione più specifica, purché `TimeStamp` il tipo della proprietà sia una matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="62b26-192">But rather than using the `ConcurrencyCheck` annotation, you can use the more specific `TimeStamp` annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="62b26-193">Code First considererà `Timestamp` le proprietà come le `ConcurrencyCheck` proprietà, ma assicurerà anche che il campo del database generato da Code First non ammette i valori null.</span><span class="sxs-lookup"><span data-stu-id="62b26-193">Code first will treat `Timestamp` properties the same as `ConcurrencyCheck` properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="62b26-194">È possibile avere una sola proprietà timestamp in una determinata classe.</span><span class="sxs-lookup"><span data-stu-id="62b26-194">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="62b26-195">Aggiunta della proprietà seguente alla classe Blog:</span><span class="sxs-lookup"><span data-stu-id="62b26-195">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="62b26-196">genera prima di tutto una colonna timestamp che non ammette i valori null nella tabella di database.</span><span class="sxs-lookup"><span data-stu-id="62b26-196">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![Tabella Blogs con colonna timestamp](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="62b26-198">Tabella e colonna</span><span class="sxs-lookup"><span data-stu-id="62b26-198">Table and Column</span></span>

<span data-ttu-id="62b26-199">Se si lascia Code First creare il database, può essere necessario modificare il nome delle tabelle e delle colonne che sta creando.</span><span class="sxs-lookup"><span data-stu-id="62b26-199">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="62b26-200">È inoltre possibile utilizzare Code First con un database esistente.</span><span class="sxs-lookup"><span data-stu-id="62b26-200">You can also use Code First with an existing database.</span></span> <span data-ttu-id="62b26-201">Non è sempre il caso che i nomi delle classi e delle proprietà nel dominio corrispondano ai nomi delle tabelle e delle colonne nel database.</span><span class="sxs-lookup"><span data-stu-id="62b26-201">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="62b26-202">La classe è denominata `Blog` e per convenzione, il Code First presuppone che questo venga mappato a una tabella denominata `Blogs` .</span><span class="sxs-lookup"><span data-stu-id="62b26-202">My class is named `Blog` and by convention, code first presumes this will map to a table named `Blogs`.</span></span> <span data-ttu-id="62b26-203">In caso contrario, è possibile specificare il nome della tabella con l' `Table` attributo.</span><span class="sxs-lookup"><span data-stu-id="62b26-203">If that's not the case you can specify the name of the table with the `Table` attribute.</span></span> <span data-ttu-id="62b26-204">Qui, ad esempio, l'annotazione specifica che il nome della tabella è **InternalBlogs**.</span><span class="sxs-lookup"><span data-stu-id="62b26-204">Here for example, the annotation is specifying that the table name is **InternalBlogs**.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="62b26-205">L' `Column` annotazione è più abile nella definizione degli attributi di una colonna mappata.</span><span class="sxs-lookup"><span data-stu-id="62b26-205">The `Column` annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="62b26-206">È possibile specificare un nome, un tipo di dati o anche l'ordine in cui una colonna viene visualizzata nella tabella.</span><span class="sxs-lookup"><span data-stu-id="62b26-206">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="62b26-207">Di seguito è riportato un esempio dell' `Column` attributo.</span><span class="sxs-lookup"><span data-stu-id="62b26-207">Here is an example of the `Column` attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="62b26-208">Non confondere `TypeName` l'attributo della colonna con DataType DataAnnotation.</span><span class="sxs-lookup"><span data-stu-id="62b26-208">Don’t confuse Column’s `TypeName` attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="62b26-209">DataType è un'annotazione utilizzata per l'interfaccia utente e viene ignorata dal Code First.</span><span class="sxs-lookup"><span data-stu-id="62b26-209">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="62b26-210">Di seguito è riportata la tabella dopo che è stata rigenerata.</span><span class="sxs-lookup"><span data-stu-id="62b26-210">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="62b26-211">Il nome della tabella è stato modificato in **InternalBlogs** e la `Description` colonna del tipo complesso è ora `BlogDescription` .</span><span class="sxs-lookup"><span data-stu-id="62b26-211">The table name has changed to **InternalBlogs** and `Description` column from the complex type is now `BlogDescription`.</span></span> <span data-ttu-id="62b26-212">Poiché il nome è stato specificato nell'annotazione, Code First non utilizzerà la convenzione di avvio del nome della colonna con il nome del tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="62b26-212">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Tabella e colonna di Blog rinominati](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="62b26-214">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="62b26-214">DatabaseGenerated</span></span>

<span data-ttu-id="62b26-215">Una funzionalità di database importante è la possibilità di disporre di proprietà calcolate.</span><span class="sxs-lookup"><span data-stu-id="62b26-215">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="62b26-216">Se si esegue il mapping delle classi Code First a tabelle che contengono colonne calcolate, non si desidera che Entity Framework tenti di aggiornare tali colonne.</span><span class="sxs-lookup"><span data-stu-id="62b26-216">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="62b26-217">Ma si vuole che EF restituisca tali valori dal database dopo l'inserimento o l'aggiornamento dei dati.</span><span class="sxs-lookup"><span data-stu-id="62b26-217">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="62b26-218">È possibile usare l' `DatabaseGenerated` annotazione per contrassegnare le proprietà della classe insieme all' `Computed` enum.</span><span class="sxs-lookup"><span data-stu-id="62b26-218">You can use the `DatabaseGenerated` annotation to flag those properties in your class along with the `Computed` enum.</span></span> <span data-ttu-id="62b26-219">Altre enumerazioni sono `None` e `Identity` .</span><span class="sxs-lookup"><span data-stu-id="62b26-219">Other enums are `None` and `Identity`.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="62b26-220">È possibile utilizzare il database generato su colonne di tipo byte o timestamp al momento della generazione del database da parte del codice; in caso contrario, è consigliabile utilizzare questa opzione solo quando si fa riferimento a database esistenti perché Code First non sarà in grado di determinare la formula per la colonna calcolata.</span><span class="sxs-lookup"><span data-stu-id="62b26-220">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="62b26-221">Per impostazione predefinita, una proprietà chiave che corrisponde a un numero intero diventerà una chiave di identità nel database.</span><span class="sxs-lookup"><span data-stu-id="62b26-221">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="62b26-222">Corrisponderebbe `DatabaseGenerated` a impostare su `DatabaseGeneratedOption.Identity` .</span><span class="sxs-lookup"><span data-stu-id="62b26-222">That would be the same as setting `DatabaseGenerated` to `DatabaseGeneratedOption.Identity`.</span></span> <span data-ttu-id="62b26-223">Se non si desidera che sia una chiave di identità, è possibile impostare il valore su `DatabaseGeneratedOption.None` .</span><span class="sxs-lookup"><span data-stu-id="62b26-223">If you do not want it to be an identity key, you can set the value to `DatabaseGeneratedOption.None`.</span></span>

 

## <a name="index"></a><span data-ttu-id="62b26-224">Indice</span><span class="sxs-lookup"><span data-stu-id="62b26-224">Index</span></span>

> [!NOTE]
> <span data-ttu-id="62b26-225">**Ef 6.1 e versioni successive** : l' `Index` attributo è stato introdotto in Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="62b26-225">**EF6.1 Onwards Only** - The `Index` attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="62b26-226">Se si usa una versione precedente, le informazioni contenute in questa sezione non sono valide.</span><span class="sxs-lookup"><span data-stu-id="62b26-226">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="62b26-227">È possibile creare un indice in una o più colonne usando **IndexAttribute**.</span><span class="sxs-lookup"><span data-stu-id="62b26-227">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="62b26-228">Se si aggiunge l'attributo a una o più proprietà, Entity Framework creerà l'indice corrispondente nel database durante la creazione del database oppure esegue l'impalcatura delle chiamate **CreateIndex** corrispondenti se si utilizza Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="62b26-228">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="62b26-229">Il codice seguente, ad esempio, comporterà la creazione di un indice nella `Rating` colonna della `Posts` tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="62b26-229">For example, the following code will result in an index being created on the `Rating` column of the `Posts` table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="62b26-230">Per impostazione predefinita, l'indice verrà denominato \*\*IX \_ &lt; Property Name &gt; \*\* (IX \_ rating nell'esempio precedente).</span><span class="sxs-lookup"><span data-stu-id="62b26-230">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="62b26-231">È anche possibile specificare un nome per l'indice.</span><span class="sxs-lookup"><span data-stu-id="62b26-231">You can also specify a name for the index though.</span></span> <span data-ttu-id="62b26-232">Nell'esempio seguente viene specificato che l'indice deve essere denominato `PostRatingIndex` .</span><span class="sxs-lookup"><span data-stu-id="62b26-232">The following example specifies that the index should be named `PostRatingIndex`.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="62b26-233">Per impostazione predefinita, gli indici non sono univoci, ma è possibile usare il `IsUnique` parametro denominato per specificare che un indice deve essere univoco.</span><span class="sxs-lookup"><span data-stu-id="62b26-233">By default, indexes are non-unique, but you can use the `IsUnique` named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="62b26-234">Nell'esempio seguente viene introdotto un indice univoco per il `User` nome di accesso di un oggetto.</span><span class="sxs-lookup"><span data-stu-id="62b26-234">The following example introduces a unique index on a `User`'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="62b26-235">Indici a più colonne</span><span class="sxs-lookup"><span data-stu-id="62b26-235">Multiple-Column Indexes</span></span>

<span data-ttu-id="62b26-236">Gli indici che si estendono su più colonne vengono specificati utilizzando lo stesso nome in più annotazioni di indice per una tabella specificata.</span><span class="sxs-lookup"><span data-stu-id="62b26-236">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="62b26-237">Quando si creano indici a più colonne, è necessario specificare un ordine per le colonne nell'indice.</span><span class="sxs-lookup"><span data-stu-id="62b26-237">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="62b26-238">Il codice seguente, ad esempio, consente di creare un indice a più colonne in `Rating` e `BlogId` denominato **IX \_ BlogIdAndRating**.</span><span class="sxs-lookup"><span data-stu-id="62b26-238">For example, the following code creates a multi-column index on `Rating` and `BlogId` called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="62b26-239">`BlogId` è la prima colonna nell'indice e `Rating` è il secondo.</span><span class="sxs-lookup"><span data-stu-id="62b26-239">`BlogId` is the first column in the index and `Rating` is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="62b26-240">Attributi relazione: InverseProperty e ForeignKey</span><span class="sxs-lookup"><span data-stu-id="62b26-240">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="62b26-241">Questa pagina fornisce informazioni sulla configurazione delle relazioni nel modello di Code First usando le annotazioni dei dati.</span><span class="sxs-lookup"><span data-stu-id="62b26-241">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="62b26-242">Per informazioni generali sulle relazioni in EF e su come accedere e modificare i dati usando le relazioni, vedere [relazioni & proprietà di navigazione](xref:ef6/fundamentals/relationships). \*</span><span class="sxs-lookup"><span data-stu-id="62b26-242">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).\*</span></span>

<span data-ttu-id="62b26-243">La convenzione Code First si occuperà delle relazioni più comuni nel modello, ma in alcuni casi è necessario aiuto.</span><span class="sxs-lookup"><span data-stu-id="62b26-243">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="62b26-244">La modifica del nome della proprietà chiave nella `Blog` classe ha creato un problema con la relativa relazione con `Post` .</span><span class="sxs-lookup"><span data-stu-id="62b26-244">Changing the name of the key property in the `Blog` class created a problem with its relationship to `Post`.</span></span> 

<span data-ttu-id="62b26-245">Quando si genera il database, il codice Visualizza prima la `BlogId` proprietà nella classe post e la riconosce, in base alla convenzione che corrisponde al nome di una classe più l' **ID**, come chiave esterna della `Blog` classe.</span><span class="sxs-lookup"><span data-stu-id="62b26-245">When generating the database, code first sees the `BlogId` property in the Post class and recognizes it, by the convention that it matches a class name plus **Id**, as a foreign key to the `Blog` class.</span></span> <span data-ttu-id="62b26-246">Ma non è presente alcuna `BlogId` proprietà nella classe Blog.</span><span class="sxs-lookup"><span data-stu-id="62b26-246">But there is no `BlogId` property in the blog class.</span></span> <span data-ttu-id="62b26-247">La soluzione consiste nel creare una proprietà di navigazione in `Post` e utilizzare l'oggetto `ForeignKey` DataAnnotation per aiutare il codice a comprendere come compilare la relazione tra le due classi (utilizzando la `Post.BlogId` proprietà), nonché come specificare vincoli nel database.</span><span class="sxs-lookup"><span data-stu-id="62b26-247">The solution for this is to create a navigation property in the `Post` and use the `ForeignKey` DataAnnotation to help code first understand how to build the relationship between the two classes (using the `Post.BlogId` property) as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="62b26-248">Il vincolo nel database Mostra una relazione tra `InternalBlogs.PrimaryTrackingKey` e `Posts.BlogId` .</span><span class="sxs-lookup"><span data-stu-id="62b26-248">The constraint in the database shows a relationship between `InternalBlogs.PrimaryTrackingKey` and `Posts.BlogId`.</span></span> 

![relazione tra InternalBlogs. PrimaryTrackingKey e Posts. BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="62b26-250">`InverseProperty`Viene utilizzato quando si dispone di più relazioni tra le classi.</span><span class="sxs-lookup"><span data-stu-id="62b26-250">The `InverseProperty` is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="62b26-251">Nella `Post` classe può essere utile tenere traccia dell'autore di un post di Blog, nonché di chi lo ha modificato.</span><span class="sxs-lookup"><span data-stu-id="62b26-251">In the `Post` class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="62b26-252">Di seguito sono riportate due nuove proprietà di navigazione per la classe post.</span><span class="sxs-lookup"><span data-stu-id="62b26-252">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="62b26-253">È anche necessario aggiungere la `Person` classe a cui fanno riferimento queste proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-253">You’ll also need to add in the `Person` class referenced by these properties.</span></span> <span data-ttu-id="62b26-254">Alla `Person` classe sono associate proprietà di navigazione `Post` , una per tutti i post scritti dalla persona e l'altra per tutti i post aggiornati da tale persona.</span><span class="sxs-lookup"><span data-stu-id="62b26-254">The `Person` class has navigation properties back to the `Post`, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="62b26-255">Code First non è in grado di associare le proprietà nelle due classi in modo autonomo.</span><span class="sxs-lookup"><span data-stu-id="62b26-255">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="62b26-256">La tabella di database per `Posts` deve avere una chiave esterna per la `CreatedBy` persona e una per la `UpdatedBy` persona, ma Code First creerà quattro proprietà di chiave esterna: \*\* \_ ID persona**, **Person \_ ID1**, \*\* \_ ID CreatedBy** e \*\* \_ ID UpdatedBy\*\*.</span><span class="sxs-lookup"><span data-stu-id="62b26-256">The database table for `Posts` should have one foreign key for the `CreatedBy` person and one for the `UpdatedBy` person but code first will create four foreign key properties: **Person\_Id**, **Person\_Id1**, **CreatedBy\_Id** and **UpdatedBy\_Id**.</span></span>

![Tabella post con chiavi esterne aggiuntive](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="62b26-258">Per risolvere questi problemi, è possibile utilizzare l' `InverseProperty` annotazione per specificare l'allineamento delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="62b26-258">To fix these problems, you can use the `InverseProperty` annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="62b26-259">Poiché la `PostsWritten` Proprietà in person sa che si riferisce al `Post` tipo, la relazione verrà compilata con `Post.CreatedBy` .</span><span class="sxs-lookup"><span data-stu-id="62b26-259">Because the `PostsWritten` property in Person knows that this refers to the `Post` type, it will build the relationship to `Post.CreatedBy`.</span></span> <span data-ttu-id="62b26-260">Analogamente, `PostsUpdated` verrà connesso a `Post.UpdatedBy` .</span><span class="sxs-lookup"><span data-stu-id="62b26-260">Similarly, `PostsUpdated` will be connected to `Post.UpdatedBy`.</span></span> <span data-ttu-id="62b26-261">E Code First non creerà le chiavi esterne aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="62b26-261">And code first will not create the extra foreign keys.</span></span>

![Tabella post senza chiavi esterne aggiuntive](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="62b26-263">Riepilogo</span><span class="sxs-lookup"><span data-stu-id="62b26-263">Summary</span></span>

<span data-ttu-id="62b26-264">Le annotazioni DataAnnotations non solo consentono di descrivere la convalida lato client e server nelle classi code first, ma consentono anche di migliorare e persino correggere i presupposti che Code First farà sulle classi in base alle convenzioni.</span><span class="sxs-lookup"><span data-stu-id="62b26-264">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="62b26-265">Con le annotazioni DataAnnotations non solo è possibile gestire la generazione dello schema del database, ma è anche possibile eseguire il mapping delle classi Code First a un database preesistente.</span><span class="sxs-lookup"><span data-stu-id="62b26-265">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="62b26-266">Sebbene siano molto flessibili, tenere presente che le annotazioni di DataAnnotations forniscono solo le modifiche di configurazione più comuni che è possibile apportare alle classi code first.</span><span class="sxs-lookup"><span data-stu-id="62b26-266">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="62b26-267">Per configurare le classi per alcuni dei casi limite, è necessario esaminare il meccanismo di configurazione alternativo, l'API Fluent di Code First.</span><span class="sxs-lookup"><span data-stu-id="62b26-267">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
