---
title: Convenzioni Code First personalizzate-EF6
description: Convenzioni di Code First personalizzate in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 69e4b0111394e83195f5c0a81624c7b9e45bda52
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617265"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="8fe65-103">Convenzioni di Code First personalizzate</span><span class="sxs-lookup"><span data-stu-id="8fe65-103">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="8fe65-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="8fe65-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="8fe65-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="8fe65-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="8fe65-106">Quando si usa Code First il modello viene calcolato dalle classi usando un set di convenzioni.</span><span class="sxs-lookup"><span data-stu-id="8fe65-106">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="8fe65-107">Le [convenzioni Code First](xref:ef6/modeling/code-first/conventions/built-in) predefinite determinano elementi come la proprietà che diventa la chiave primaria di un'entità, il nome della tabella a cui viene eseguito il mapping di un'entità e la precisione e la scala di una colonna decimale per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="8fe65-107">The default [Code First Conventions](xref:ef6/modeling/code-first/conventions/built-in) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="8fe65-108">A volte queste convenzioni predefinite non sono ideali per il modello ed è necessario aggirarle configurando molte singole entità usando le annotazioni dei dati o l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="8fe65-108">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="8fe65-109">Le convenzioni Code First personalizzate consentono di definire convenzioni personalizzate che forniscono impostazioni predefinite di configurazione per il modello.</span><span class="sxs-lookup"><span data-stu-id="8fe65-109">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="8fe65-110">In questa procedura dettagliata vengono esaminati i diversi tipi di convenzioni personalizzate e viene illustrato come crearli.</span><span class="sxs-lookup"><span data-stu-id="8fe65-110">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="8fe65-111">Convenzioni basate su modelli</span><span class="sxs-lookup"><span data-stu-id="8fe65-111">Model-Based Conventions</span></span>

<span data-ttu-id="8fe65-112">Questa pagina descrive l'API DbModelBuilder per le convenzioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="8fe65-112">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="8fe65-113">Questa API dovrebbe essere sufficiente per la creazione della maggior parte delle convenzioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="8fe65-113">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="8fe65-114">Tuttavia, esiste anche la possibilità di creare convenzioni basate su modelli, che modificano il modello finale dopo la creazione, per gestire scenari avanzati.</span><span class="sxs-lookup"><span data-stu-id="8fe65-114">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="8fe65-115">Per altre informazioni, vedere [convenzioni basate su modelli](xref:ef6/modeling/code-first/conventions/model).</span><span class="sxs-lookup"><span data-stu-id="8fe65-115">For more information, see [Model-Based Conventions](xref:ef6/modeling/code-first/conventions/model).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="8fe65-116">Modello</span><span class="sxs-lookup"><span data-stu-id="8fe65-116">Our Model</span></span>

<span data-ttu-id="8fe65-117">Si inizierà con la definizione di un modello semplice che è possibile usare con le convenzioni.</span><span class="sxs-lookup"><span data-stu-id="8fe65-117">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="8fe65-118">Aggiungere le classi seguenti al progetto.</span><span class="sxs-lookup"><span data-stu-id="8fe65-118">Add the following classes to your project.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="8fe65-119">Introduzione alle convenzioni personalizzate</span><span class="sxs-lookup"><span data-stu-id="8fe65-119">Introducing Custom Conventions</span></span>

<span data-ttu-id="8fe65-120">Verrà ora scritta una convenzione che configura qualsiasi proprietà denominata Key come chiave primaria per il tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="8fe65-120">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="8fe65-121">Le convenzioni sono abilitate nel generatore di modelli, a cui è possibile accedere eseguendo l'override di OnModelCreating nel contesto.</span><span class="sxs-lookup"><span data-stu-id="8fe65-121">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="8fe65-122">Aggiornare la classe ProductContext come segue:</span><span class="sxs-lookup"><span data-stu-id="8fe65-122">Update the ProductContext class as follows:</span></span>

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

<span data-ttu-id="8fe65-123">A questo punto, qualsiasi proprietà nel modello denominato Key verrà configurata come chiave primaria di qualsiasi entità di cui fa parte.</span><span class="sxs-lookup"><span data-stu-id="8fe65-123">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="8fe65-124">È anche possibile rendere più specifiche le convenzioni filtrando il tipo di proprietà che verrà configurata:</span><span class="sxs-lookup"><span data-stu-id="8fe65-124">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="8fe65-125">Verranno configurate tutte le proprietà chiamate chiave come chiave primaria dell'entità, ma solo se sono di tipo Integer.</span><span class="sxs-lookup"><span data-stu-id="8fe65-125">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="8fe65-126">Una funzionalità interessante del metodo IsKey è che si tratta di un additivo.</span><span class="sxs-lookup"><span data-stu-id="8fe65-126">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="8fe65-127">Ciò significa che se si chiama IsKey su più proprietà e tutte diventano parte di una chiave composta.</span><span class="sxs-lookup"><span data-stu-id="8fe65-127">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="8fe65-128">Un avvertimento è che, quando si specificano più proprietà per una chiave, è necessario specificare anche un ordine per tali proprietà.</span><span class="sxs-lookup"><span data-stu-id="8fe65-128">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="8fe65-129">Questa operazione può essere eseguita chiamando il metodo HasColumnOrder come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="8fe65-129">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="8fe65-130">Questo codice consente di configurare i tipi del modello in modo che abbiano una chiave composta costituita dalla colonna chiave int e dalla colonna nome stringa.</span><span class="sxs-lookup"><span data-stu-id="8fe65-130">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="8fe65-131">Se si Visualizza il modello nella finestra di progettazione, l'aspetto sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8fe65-131">If we view the model in the designer it would look like this:</span></span>

![Chiave composta](~/ef6/media/compositekey.png)

<span data-ttu-id="8fe65-133">Un altro esempio di convenzioni di proprietà è la configurazione di tutte le proprietà DateTime nel modello per eseguire il mapping al tipo datetime2 in SQL Server anziché in DateTime.</span><span class="sxs-lookup"><span data-stu-id="8fe65-133">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="8fe65-134">È possibile ottenere questo risultato con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="8fe65-134">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="8fe65-135">Classi convenzione</span><span class="sxs-lookup"><span data-stu-id="8fe65-135">Convention Classes</span></span>

<span data-ttu-id="8fe65-136">Un altro modo per definire le convenzioni consiste nell'usare una classe Convention per incapsulare la convenzione.</span><span class="sxs-lookup"><span data-stu-id="8fe65-136">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="8fe65-137">Quando si usa una classe Convention, viene creato un tipo che eredita dalla classe Convention nello spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions.</span><span class="sxs-lookup"><span data-stu-id="8fe65-137">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="8fe65-138">È possibile creare una classe di convenzione con la convenzione datetime2 mostrata in precedenza effettuando le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8fe65-138">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

<span data-ttu-id="8fe65-139">Per indicare a EF di usare questa convenzione, è necessario aggiungerla alla raccolta Conventions in OnModelCreating, che se è stata completata con la procedura dettagliata sarà simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="8fe65-139">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="8fe65-140">Come si può notare, si aggiunge un'istanza della convenzione alla raccolta Conventions.</span><span class="sxs-lookup"><span data-stu-id="8fe65-140">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="8fe65-141">L'ereditarietà dalla convenzione fornisce un modo pratico per raggruppare e condividere le convenzioni tra team o progetti.</span><span class="sxs-lookup"><span data-stu-id="8fe65-141">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="8fe65-142">È possibile, ad esempio, avere una libreria di classi con un set di convenzioni comune usato da tutti i progetti delle organizzazioni.</span><span class="sxs-lookup"><span data-stu-id="8fe65-142">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="8fe65-143">Attributi personalizzati</span><span class="sxs-lookup"><span data-stu-id="8fe65-143">Custom Attributes</span></span>

<span data-ttu-id="8fe65-144">Un altro ottimo utilizzo delle convenzioni è quello di consentire l'utilizzo di nuovi attributi durante la configurazione di un modello.</span><span class="sxs-lookup"><span data-stu-id="8fe65-144">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="8fe65-145">Per illustrare questo problema, creare un attributo che è possibile usare per contrassegnare le proprietà di stringa come non Unicode.</span><span class="sxs-lookup"><span data-stu-id="8fe65-145">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="8fe65-146">A questo punto, creare una convenzione per applicare questo attributo al modello:</span><span class="sxs-lookup"><span data-stu-id="8fe65-146">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="8fe65-147">Con questa convenzione è possibile aggiungere l'attributo non Unicode a una qualsiasi delle proprietà della stringa, il che significa che la colonna nel database verrà archiviata come varchar anziché nvarchar.</span><span class="sxs-lookup"><span data-stu-id="8fe65-147">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="8fe65-148">Una cosa da notare in merito a questa convenzione è che se si inserisce l'attributo non Unicode su un valore diverso da una proprietà stringa, verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8fe65-148">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="8fe65-149">Questa operazione viene eseguita perché non è possibile configurare l'estensione Unicode su un tipo diverso da una stringa.</span><span class="sxs-lookup"><span data-stu-id="8fe65-149">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="8fe65-150">In tal caso, è possibile rendere più specifica la convenzione, in modo da escludere qualsiasi elemento che non sia una stringa.</span><span class="sxs-lookup"><span data-stu-id="8fe65-150">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="8fe65-151">Sebbene la convenzione precedente funzioni per la definizione di attributi personalizzati, è disponibile un'altra API che può essere molto più semplice da utilizzare, specialmente quando si desidera utilizzare le proprietà della classe Attribute.</span><span class="sxs-lookup"><span data-stu-id="8fe65-151">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="8fe65-152">Per questo esempio verrà aggiornato l'attributo e il valore verrà modificato in un attributo deunicode, quindi sarà simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8fe65-152">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

<span data-ttu-id="8fe65-153">Al termine di questa operazione, è possibile impostare un valore bool sull'attributo per indicare alla convenzione se una proprietà deve essere Unicode o meno.</span><span class="sxs-lookup"><span data-stu-id="8fe65-153">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="8fe65-154">È possibile eseguire questa operazione nella convenzione già eseguita accedendo al ClrProperty della classe di configurazione come segue:</span><span class="sxs-lookup"><span data-stu-id="8fe65-154">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="8fe65-155">Questa operazione è abbastanza semplice, ma esiste un modo più conciso per ottenere questo problema usando il metodo having dell'API Conventions.</span><span class="sxs-lookup"><span data-stu-id="8fe65-155">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="8fe65-156">Il metodo having ha un parametro di tipo Func &lt; PropertyInfo, T &gt; che accetta l'oggetto PropertyInfo uguale al metodo Where, ma si prevede che restituisca un oggetto.</span><span class="sxs-lookup"><span data-stu-id="8fe65-156">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="8fe65-157">Se l'oggetto restituito è null, la proprietà non verrà configurata, il che significa che è possibile filtrare le proprietà con il valore come WHERE, ma è diverso in quanto acquisisce anche l'oggetto restituito e lo passa al metodo Configure.</span><span class="sxs-lookup"><span data-stu-id="8fe65-157">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="8fe65-158">Il funzionamento è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="8fe65-158">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="8fe65-159">Gli attributi personalizzati non sono l'unico motivo per usare il metodo having, ma è utile ovunque sia necessario ragionare su un elemento su cui si sta filtrando quando si configurano tipi o proprietà.</span><span class="sxs-lookup"><span data-stu-id="8fe65-159">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="8fe65-160">Configurazione di tipi</span><span class="sxs-lookup"><span data-stu-id="8fe65-160">Configuring Types</span></span>

<span data-ttu-id="8fe65-161">Fino a questo punto, tutte le convenzioni sono state usate per le proprietà, ma esiste un'altra area dell'API convenzioni per la configurazione dei tipi nel modello.</span><span class="sxs-lookup"><span data-stu-id="8fe65-161">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="8fe65-162">L'esperienza è simile alle convenzioni che abbiamo visto finora, ma le opzioni all'interno di configure si troveranno all'entità anziché al livello della proprietà.</span><span class="sxs-lookup"><span data-stu-id="8fe65-162">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="8fe65-163">Uno degli elementi che possono essere particolarmente utili per le convenzioni del livello di tipo è la modifica della convenzione di denominazione della tabella, per eseguire il mapping a uno schema esistente diverso da quello predefinito di EF oppure per creare un nuovo database con una convenzione di denominazione diversa.</span><span class="sxs-lookup"><span data-stu-id="8fe65-163">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="8fe65-164">A tale scopo, è necessario innanzitutto un metodo in grado di accettare TypeInfo per un tipo nel modello e restituire il nome della tabella per quel tipo:</span><span class="sxs-lookup"><span data-stu-id="8fe65-164">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="8fe65-165">Questo metodo accetta un tipo e restituisce una stringa che usa lettere minuscole con caratteri di sottolineatura anziché CamelCase.</span><span class="sxs-lookup"><span data-stu-id="8fe65-165">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="8fe65-166">Nel modello questo significa che la classe ProductCategory verrà mappata a una tabella denominata Product \_ Category anziché ProductCategories.</span><span class="sxs-lookup"><span data-stu-id="8fe65-166">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="8fe65-167">Una volta ottenuto il metodo, è possibile chiamarlo in una convenzione come la seguente:</span><span class="sxs-lookup"><span data-stu-id="8fe65-167">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="8fe65-168">Questa convenzione Configura ogni tipo nel modello per eseguire il mapping al nome della tabella restituito dal metodo gettablenamename.</span><span class="sxs-lookup"><span data-stu-id="8fe65-168">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="8fe65-169">Questa convenzione equivale a chiamare il metodo ToTable per ogni entità nel modello usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="8fe65-169">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="8fe65-170">Una cosa da tenere presente è che quando si chiama ToTable EF prenderà la stringa fornita come nome di tabella esatto, senza alcuna parte della pluralità che verrebbe eseguita normalmente durante la determinazione dei nomi delle tabelle.</span><span class="sxs-lookup"><span data-stu-id="8fe65-170">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="8fe65-171">Questo è il motivo per cui il nome della tabella della convenzione è Product \_ Category anziché categorie di prodotti \_ .</span><span class="sxs-lookup"><span data-stu-id="8fe65-171">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="8fe65-172">Possiamo risolverlo nella convenzione effettuando una chiamata al servizio di pluralismo.</span><span class="sxs-lookup"><span data-stu-id="8fe65-172">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="8fe65-173">Nel codice seguente verrà usata la funzionalità di [risoluzione delle dipendenze](xref:ef6/fundamentals/configuring/dependency-resolution) aggiunta in EF6 per recuperare il servizio di pluralismo che EF avrebbe usato e plurali il nome della tabella.</span><span class="sxs-lookup"><span data-stu-id="8fe65-173">In the following code we will use the [Dependency Resolution](xref:ef6/fundamentals/configuring/dependency-resolution) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> <span data-ttu-id="8fe65-174">La versione generica di GetService è un metodo di estensione nello spazio dei nomi System. Data. Entity. Infrastructure. DependencyResolution. è necessario aggiungere un'istruzione using al contesto per poterla usare.</span><span class="sxs-lookup"><span data-stu-id="8fe65-174">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="8fe65-175">ToTable ed ereditarietà</span><span class="sxs-lookup"><span data-stu-id="8fe65-175">ToTable and Inheritance</span></span>

<span data-ttu-id="8fe65-176">Un altro aspetto importante di ToTable è che se si esegue in modo esplicito il mapping di un tipo a una determinata tabella, è possibile modificare la strategia di mapping che verrà usata da EF.</span><span class="sxs-lookup"><span data-stu-id="8fe65-176">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="8fe65-177">Se si chiama ToTable per ogni tipo in una gerarchia di ereditarietà, passando il nome del tipo come nome della tabella, come descritto in precedenza, si modificherà la strategia di mapping tabella per gerarchia (TPH) predefinita a tabella per tipo (TPT).</span><span class="sxs-lookup"><span data-stu-id="8fe65-177">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="8fe65-178">Il modo migliore per descrivere questa operazione è un esempio concreto:</span><span class="sxs-lookup"><span data-stu-id="8fe65-178">The best way to describe this is whith a concrete example:</span></span>

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

<span data-ttu-id="8fe65-179">Per impostazione predefinita, sia Employee che Manager vengono mappati alla stessa tabella (Employees) del database.</span><span class="sxs-lookup"><span data-stu-id="8fe65-179">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="8fe65-180">La tabella conterrà sia i dipendenti che i responsabili con una colonna discriminatore che indica il tipo di istanza archiviato in ogni riga.</span><span class="sxs-lookup"><span data-stu-id="8fe65-180">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="8fe65-181">Si tratta di un mapping TPH poiché esiste una singola tabella per la gerarchia.</span><span class="sxs-lookup"><span data-stu-id="8fe65-181">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="8fe65-182">Tuttavia, se si chiama ToTable su entrambi i tipi, verrà eseguito il mapping di ogni tipo alla relativa tabella, nota anche come TPT, perché ogni tipo ha una propria tabella.</span><span class="sxs-lookup"><span data-stu-id="8fe65-182">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="8fe65-183">Il codice precedente eseguirà il mapping a una struttura di tabella simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="8fe65-183">The code above will map to a table structure that looks like the following:</span></span>

![Esempio di TPT](~/ef6/media/tptexample.jpg)

<span data-ttu-id="8fe65-185">È possibile evitare questo problema e mantenere il mapping predefinito di TPH, in due modi:</span><span class="sxs-lookup"><span data-stu-id="8fe65-185">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="8fe65-186">Chiamare ToTable con lo stesso nome di tabella per ogni tipo nella gerarchia.</span><span class="sxs-lookup"><span data-stu-id="8fe65-186">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="8fe65-187">Chiamare ToTable solo sulla classe di base della gerarchia, in questo esempio si tratta di Employee.</span><span class="sxs-lookup"><span data-stu-id="8fe65-187">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="8fe65-188">Ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="8fe65-188">Execution Order</span></span>

<span data-ttu-id="8fe65-189">Le convenzioni operano in un ultimo modo WINS, come l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="8fe65-189">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="8fe65-190">Ciò significa che se si scrivono due convenzioni che configurano la stessa opzione della stessa proprietà, l'ultima esecuzione di WINS.</span><span class="sxs-lookup"><span data-stu-id="8fe65-190">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="8fe65-191">Ad esempio, nel codice sotto la lunghezza massima di tutte le stringhe è impostato su 500, ma vengono quindi configurate tutte le proprietà denominate nel modello per avere una lunghezza massima di 250.</span><span class="sxs-lookup"><span data-stu-id="8fe65-191">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="8fe65-192">Poiché la convenzione per impostare la lunghezza massima su 250 è successiva a quella che imposta tutte le stringhe su 500, tutte le proprietà denominate nel modello avranno un valore MaxLength di 250 mentre qualsiasi altra stringa, ad esempio Description, sarà 500.</span><span class="sxs-lookup"><span data-stu-id="8fe65-192">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="8fe65-193">L'uso di convenzioni in questo modo significa che è possibile fornire una convenzione generale per i tipi o le proprietà nel modello e quindi Sostituisci per subset diversi.</span><span class="sxs-lookup"><span data-stu-id="8fe65-193">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="8fe65-194">Le annotazioni di dati e API Fluent possono essere utilizzate anche per eseguire l'override di una convenzione in casi specifici.</span><span class="sxs-lookup"><span data-stu-id="8fe65-194">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="8fe65-195">Nell'esempio precedente, se è stata usata l'API Fluent per impostare la lunghezza massima di una proprietà, è possibile inserirla prima o dopo la convenzione, perché l'API Fluent più specifica vincerà la convenzione di configurazione più generale.</span><span class="sxs-lookup"><span data-stu-id="8fe65-195">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="8fe65-196">Convenzioni predefinite</span><span class="sxs-lookup"><span data-stu-id="8fe65-196">Built-in Conventions</span></span>

<span data-ttu-id="8fe65-197">Poiché le convenzioni personalizzate possono essere influenzate dalle convenzioni Code First predefinite, può essere utile aggiungere convenzioni da eseguire prima o dopo un'altra convenzione.</span><span class="sxs-lookup"><span data-stu-id="8fe65-197">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="8fe65-198">A tale scopo, è possibile usare i metodi AddBefore e AddAfter della raccolta Conventions nel DbContext derivato.</span><span class="sxs-lookup"><span data-stu-id="8fe65-198">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="8fe65-199">Il codice seguente aggiunge la classe della convenzione creata in precedenza, in modo che venga eseguita prima della convenzione di individuazione chiave incorporata.</span><span class="sxs-lookup"><span data-stu-id="8fe65-199">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="8fe65-200">Questo sarà il più usato quando si aggiungono le convenzioni che devono essere eseguite prima o dopo le convenzioni predefinite. un elenco delle convenzioni predefinite è disponibile qui: [spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="8fe65-200">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="8fe65-201">È inoltre possibile rimuovere le convenzioni che non si desidera applicare al modello.</span><span class="sxs-lookup"><span data-stu-id="8fe65-201">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="8fe65-202">Per rimuovere una convenzione, usare il metodo Remove.</span><span class="sxs-lookup"><span data-stu-id="8fe65-202">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="8fe65-203">Di seguito è riportato un esempio di rimozione del PluralizingTableNameConvention.</span><span class="sxs-lookup"><span data-stu-id="8fe65-203">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
