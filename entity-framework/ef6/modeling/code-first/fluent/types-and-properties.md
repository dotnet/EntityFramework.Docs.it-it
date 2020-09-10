---
title: API Fluent-configurazione e mapping di proprietà e tipi-EF6
description: API Fluent-configurazione e mapping di proprietà e tipi in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: d49da416b2df5828fce5618cc196e22dfbc2bd34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618052"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="1817d-103">API Fluent-configurazione e mapping di proprietà e tipi</span><span class="sxs-lookup"><span data-stu-id="1817d-103">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="1817d-104">Quando si lavora con Entity Framework Code First il comportamento predefinito consiste nel eseguire il mapping delle classi POCO alle tabelle usando un set di convenzioni di cui è stato eseguito il bake in EF.</span><span class="sxs-lookup"><span data-stu-id="1817d-104">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="1817d-105">In alcuni casi, tuttavia, non è possibile o non si desidera seguire tali convenzioni ed è necessario eseguire il mapping delle entità a un valore diverso da quello che le convenzioni dettano.</span><span class="sxs-lookup"><span data-stu-id="1817d-105">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="1817d-106">Esistono due modi principali per configurare Entity Framework in modo da usare un valore diverso dalle convenzioni, ovvero le [annotazioni](xref:ef6/modeling/code-first/data-annotations) o l'API di EFS Fluent.</span><span class="sxs-lookup"><span data-stu-id="1817d-106">There are two main ways you can configure EF to use something other than conventions, namely [annotations](xref:ef6/modeling/code-first/data-annotations) or EFs fluent API.</span></span> <span data-ttu-id="1817d-107">Le annotazioni coprono solo un subset della funzionalità dell'API Fluent, quindi esistono scenari di mapping che non possono essere consentiti utilizzando le annotazioni.</span><span class="sxs-lookup"><span data-stu-id="1817d-107">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="1817d-108">Questo articolo è stato progettato per illustrare come usare l'API Fluent per configurare le proprietà.</span><span class="sxs-lookup"><span data-stu-id="1817d-108">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="1817d-109">L'accesso all'API Fluent Code First viene eseguito più di frequente eseguendo l'override del metodo [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) sul [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)derivato.</span><span class="sxs-lookup"><span data-stu-id="1817d-109">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="1817d-110">Gli esempi seguenti sono progettati per illustrare come eseguire varie attività con l'API Fluent e consentono di copiare il codice e personalizzarlo in base al modello, se si desidera visualizzare il modello che può essere utilizzato con così com'è, viene fornito alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="1817d-110">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="1817d-111">Impostazioni a livello di modello</span><span class="sxs-lookup"><span data-stu-id="1817d-111">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="1817d-112">Schema predefinito (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="1817d-112">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="1817d-113">A partire da EF6, è possibile usare il metodo HasDefaultSchema in DbModelBuilder per specificare lo schema del database da usare per tutte le tabelle, le stored procedure e così via. Questa impostazione predefinita verrà sostituita per tutti gli oggetti per i quali si configura in modo esplicito uno schema diverso.</span><span class="sxs-lookup"><span data-stu-id="1817d-113">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="1817d-114">Convenzioni personalizzate (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="1817d-114">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="1817d-115">A partire da EF6 è possibile creare convenzioni personalizzate per integrare quelle incluse in Code First.</span><span class="sxs-lookup"><span data-stu-id="1817d-115">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="1817d-116">Per ulteriori informazioni, vedere [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="1817d-116">For more details, see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="1817d-117">Mapping di proprietà</span><span class="sxs-lookup"><span data-stu-id="1817d-117">Property Mapping</span></span>  

<span data-ttu-id="1817d-118">Il metodo [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) viene utilizzato per configurare gli attributi per ogni proprietà appartenente a un tipo di entità o complesso.</span><span class="sxs-lookup"><span data-stu-id="1817d-118">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="1817d-119">Il metodo Property viene usato per ottenere un oggetto di configurazione per una determinata proprietà.</span><span class="sxs-lookup"><span data-stu-id="1817d-119">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="1817d-120">Le opzioni nell'oggetto di configurazione sono specifiche del tipo da configurare. L'estensione Unicode è disponibile solo per le proprietà di stringa, ad esempio.</span><span class="sxs-lookup"><span data-stu-id="1817d-120">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="1817d-121">Configurazione di una chiave primaria</span><span class="sxs-lookup"><span data-stu-id="1817d-121">Configuring a Primary Key</span></span>  

<span data-ttu-id="1817d-122">La convenzione Entity Framework per le chiavi primarie è la seguente:</span><span class="sxs-lookup"><span data-stu-id="1817d-122">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="1817d-123">La classe definisce una proprietà il cui nome è "ID" o "ID"</span><span class="sxs-lookup"><span data-stu-id="1817d-123">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="1817d-124">o un nome di classe seguito da "ID" o "ID"</span><span class="sxs-lookup"><span data-stu-id="1817d-124">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="1817d-125">Per impostare in modo esplicito una proprietà su una chiave primaria, è possibile usare il metodo HasKey.</span><span class="sxs-lookup"><span data-stu-id="1817d-125">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="1817d-126">Nell'esempio seguente viene usato il metodo HasKey per configurare la chiave primaria InstructorID sul tipo OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="1817d-126">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="1817d-127">Configurazione di una chiave primaria composta</span><span class="sxs-lookup"><span data-stu-id="1817d-127">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="1817d-128">Nell'esempio seguente vengono configurate le proprietà DepartmentID e Name in modo che siano la chiave primaria composta del tipo di reparto.</span><span class="sxs-lookup"><span data-stu-id="1817d-128">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="1817d-129">Spegnimento dell'identità per le chiavi primarie numeriche</span><span class="sxs-lookup"><span data-stu-id="1817d-129">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="1817d-130">Nell'esempio seguente la proprietà DepartmentID viene impostata su System. ComponentModel. DataAnnotations. DatabaseGeneratedOption. None per indicare che il valore non verrà generato dal database.</span><span class="sxs-lookup"><span data-stu-id="1817d-130">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="1817d-131">Specifica della lunghezza massima di una proprietà</span><span class="sxs-lookup"><span data-stu-id="1817d-131">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="1817d-132">Nell'esempio seguente la proprietà Name non può contenere più di 50 caratteri.</span><span class="sxs-lookup"><span data-stu-id="1817d-132">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="1817d-133">Se il valore viene reso più lungo di 50 caratteri, si otterrà un'eccezione [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) .</span><span class="sxs-lookup"><span data-stu-id="1817d-133">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="1817d-134">Se Code First crea un database da questo modello, verrà impostata anche la lunghezza massima della colonna nome su 50 caratteri.</span><span class="sxs-lookup"><span data-stu-id="1817d-134">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="1817d-135">Configurazione della proprietà in modo che sia obbligatoria</span><span class="sxs-lookup"><span data-stu-id="1817d-135">Configuring the Property to be Required</span></span>  

<span data-ttu-id="1817d-136">Nell'esempio seguente, la proprietà Name è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="1817d-136">In the following example, the Name property is required.</span></span> <span data-ttu-id="1817d-137">Se non si specifica il nome, si otterrà un'eccezione DbEntityValidationException.</span><span class="sxs-lookup"><span data-stu-id="1817d-137">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="1817d-138">Se Code First crea un database da questo modello, la colonna utilizzata per archiviare questa proprietà sarà in genere non nullable.</span><span class="sxs-lookup"><span data-stu-id="1817d-138">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="1817d-139">In alcuni casi potrebbe non essere possibile che la colonna nel database sia non nullable anche se la proprietà è obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="1817d-139">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="1817d-140">Ad esempio, quando si usano i dati della strategia di ereditarietà TPH per più tipi, viene archiviato in una singola tabella.</span><span class="sxs-lookup"><span data-stu-id="1817d-140">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="1817d-141">Se un tipo derivato include una proprietà obbligatoria, la colonna non può essere resa non nullable perché non tutti i tipi nella gerarchia avranno questa proprietà.</span><span class="sxs-lookup"><span data-stu-id="1817d-141">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="1817d-142">Configurazione di un indice in una o più proprietà</span><span class="sxs-lookup"><span data-stu-id="1817d-142">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="1817d-143">**Ef 6.1 e versioni successive** : l'attributo index è stato introdotto in Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="1817d-143">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="1817d-144">Se si usa una versione precedente, le informazioni contenute in questa sezione non sono valide.</span><span class="sxs-lookup"><span data-stu-id="1817d-144">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="1817d-145">La creazione di indici non è supportata in modo nativo dall'API Fluent, ma è possibile usare il supporto per **IndexAttribute** tramite l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="1817d-145">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="1817d-146">Gli attributi degli indici vengono elaborati includendo un'annotazione del modello nel modello che viene quindi trasformata in un indice del database in un secondo momento nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="1817d-146">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="1817d-147">È possibile aggiungere manualmente le stesse annotazioni usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="1817d-147">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="1817d-148">Il modo più semplice per eseguire questa operazione consiste nel creare un'istanza di **IndexAttribute** contenente tutte le impostazioni per il nuovo indice.</span><span class="sxs-lookup"><span data-stu-id="1817d-148">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="1817d-149">È quindi possibile creare un'istanza di **IndexAnnotation** che è un tipo specifico di EF che converte le impostazioni **IndexAttribute** in un'annotazione del modello che può essere archiviata nel modello EF.</span><span class="sxs-lookup"><span data-stu-id="1817d-149">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="1817d-150">Questi possono quindi essere passati al metodo **HasColumnAnnotation** sull'API Fluent, specificando l' **Indice** del nome per l'annotazione.</span><span class="sxs-lookup"><span data-stu-id="1817d-150">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="1817d-151">Per un elenco completo delle impostazioni disponibili in **IndexAttribute**, vedere la sezione relativa all' *Indice* delle [annotazioni dei dati Code First](xref:ef6/modeling/code-first/data-annotations).</span><span class="sxs-lookup"><span data-stu-id="1817d-151">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](xref:ef6/modeling/code-first/data-annotations).</span></span> <span data-ttu-id="1817d-152">Sono incluse la personalizzazione del nome dell'indice, la creazione di indici univoci e la creazione di indici a più colonne.</span><span class="sxs-lookup"><span data-stu-id="1817d-152">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="1817d-153">È possibile specificare più annotazioni di indice in una singola proprietà passando una matrice di **IndexAttribute** al costruttore di **IndexAnnotation**.</span><span class="sxs-lookup"><span data-stu-id="1817d-153">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="1817d-154">Specifica di non eseguire il mapping di una proprietà CLR a una colonna nel database</span><span class="sxs-lookup"><span data-stu-id="1817d-154">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="1817d-155">Nell'esempio seguente viene illustrato come specificare che una proprietà in un tipo CLR non è mappata a una colonna nel database.</span><span class="sxs-lookup"><span data-stu-id="1817d-155">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="1817d-156">Mapping di una proprietà CLR a una colonna specifica nel database</span><span class="sxs-lookup"><span data-stu-id="1817d-156">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="1817d-157">Nell'esempio seguente viene eseguito il mapping della proprietà CLR Name alla colonna di database DepartmentName.</span><span class="sxs-lookup"><span data-stu-id="1817d-157">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="1817d-158">Ridenominazione di una chiave esterna che non è definita nel modello</span><span class="sxs-lookup"><span data-stu-id="1817d-158">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="1817d-159">Se si sceglie di non definire una chiave esterna in un tipo CLR, ma si desidera specificare il nome che deve avere nel database, eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1817d-159">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="1817d-160">Configurazione se una proprietà stringa supporta il contenuto Unicode</span><span class="sxs-lookup"><span data-stu-id="1817d-160">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="1817d-161">Per impostazione predefinita, le stringhe sono Unicode (nvarchar in SQL Server).</span><span class="sxs-lookup"><span data-stu-id="1817d-161">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="1817d-162">È possibile utilizzare il metodo IsValid per specificare che una stringa deve essere di tipo varchar.</span><span class="sxs-lookup"><span data-stu-id="1817d-162">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="1817d-163">Configurazione del tipo di dati di una colonna di database</span><span class="sxs-lookup"><span data-stu-id="1817d-163">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="1817d-164">Il metodo [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) consente il mapping a rappresentazioni diverse dello stesso tipo di base.</span><span class="sxs-lookup"><span data-stu-id="1817d-164">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="1817d-165">L'utilizzo di questo metodo non consente di eseguire alcuna conversione dei dati in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1817d-165">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="1817d-166">Si noti che è il modo migliore per impostare le colonne su varchar, perché è indipendente dal database.</span><span class="sxs-lookup"><span data-stu-id="1817d-166">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="1817d-167">Configurazione di proprietà su un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="1817d-167">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="1817d-168">Esistono due modi per configurare le proprietà scalari in un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="1817d-168">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="1817d-169">È possibile chiamare la proprietà su ComplexTypeConfiguration.</span><span class="sxs-lookup"><span data-stu-id="1817d-169">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="1817d-170">È anche possibile usare la notazione del punto per accedere a una proprietà di un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="1817d-170">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="1817d-171">Configurazione di una proprietà da utilizzare come token di concorrenza ottimistica</span><span class="sxs-lookup"><span data-stu-id="1817d-171">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="1817d-172">Per specificare che una proprietà in un'entità rappresenta un token di concorrenza, è possibile usare l'attributo ConcurrencyCheck o il metodo IsConcurrencyToken.</span><span class="sxs-lookup"><span data-stu-id="1817d-172">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="1817d-173">È anche possibile usare il metodo IsRowVersion per configurare la proprietà in modo che sia una versione di riga nel database.</span><span class="sxs-lookup"><span data-stu-id="1817d-173">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="1817d-174">Impostando la proprietà su una versione di riga, questa viene configurata automaticamente in modo che sia un token di concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="1817d-174">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="1817d-175">Mapping dei tipi</span><span class="sxs-lookup"><span data-stu-id="1817d-175">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="1817d-176">Specifica che una classe è un tipo complesso</span><span class="sxs-lookup"><span data-stu-id="1817d-176">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="1817d-177">Per convenzione, un tipo che non ha una chiave primaria specificata viene considerato come un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="1817d-177">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="1817d-178">Esistono scenari in cui Code First non rileva un tipo complesso, ad esempio se si dispone di una proprietà denominata ID, ma non si intende che sia una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1817d-178">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="1817d-179">In questi casi, si utilizzerà l'API Fluent per specificare in modo esplicito che un tipo è un tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="1817d-179">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="1817d-180">Specifica di non eseguire il mapping di un tipo di entità CLR a una tabella nel database</span><span class="sxs-lookup"><span data-stu-id="1817d-180">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="1817d-181">Nell'esempio seguente viene illustrato come escludere un tipo CLR dal mapping a una tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="1817d-181">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="1817d-182">Mapping di un tipo di entità a una tabella specifica nel database</span><span class="sxs-lookup"><span data-stu-id="1817d-182">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="1817d-183">Tutte le proprietà del reparto verranno mappate alle colonne in una tabella denominata t_ Department.</span><span class="sxs-lookup"><span data-stu-id="1817d-183">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="1817d-184">È inoltre possibile specificare il nome dello schema come segue:</span><span class="sxs-lookup"><span data-stu-id="1817d-184">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="1817d-185">Mapping dell'ereditarietà tabella per gerarchia (TPH)</span><span class="sxs-lookup"><span data-stu-id="1817d-185">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="1817d-186">Nello scenario di mapping di TPH, viene eseguito il mapping di tutti i tipi in una gerarchia di ereditarietà a una singola tabella.</span><span class="sxs-lookup"><span data-stu-id="1817d-186">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="1817d-187">Una colonna discriminatore viene utilizzata per identificare il tipo di ogni riga.</span><span class="sxs-lookup"><span data-stu-id="1817d-187">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="1817d-188">Quando si crea il modello con Code First, TPH è la strategia predefinita per i tipi che fanno parte della gerarchia di ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="1817d-188">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="1817d-189">Per impostazione predefinita, la colonna discriminatore viene aggiunta alla tabella con il nome "discriminatore" e il nome del tipo CLR di ogni tipo nella gerarchia viene usato per i valori del discriminatore.</span><span class="sxs-lookup"><span data-stu-id="1817d-189">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="1817d-190">È possibile modificare il comportamento predefinito usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="1817d-190">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="1817d-191">Mapping dell'ereditarietà tabella per tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="1817d-191">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="1817d-192">Nello scenario di mapping di TPT, viene eseguito il mapping di tutti i tipi a singole tabelle.</span><span class="sxs-lookup"><span data-stu-id="1817d-192">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="1817d-193">Le proprietà che appartengono esclusivamente a un tipo di base o derivato sono archiviate in una tabella che viene mappata a quel tipo.</span><span class="sxs-lookup"><span data-stu-id="1817d-193">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="1817d-194">Le tabelle con mapping ai tipi derivati archiviano anche una chiave esterna che unisce la tabella derivata alla tabella di base.</span><span class="sxs-lookup"><span data-stu-id="1817d-194">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="1817d-195">Mapping dell'ereditarietà della classe tabella per calcestruzzo (TPC)</span><span class="sxs-lookup"><span data-stu-id="1817d-195">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="1817d-196">Nello scenario di mapping TPC, viene eseguito il mapping di tutti i tipi non astratti della gerarchia a singole tabelle.</span><span class="sxs-lookup"><span data-stu-id="1817d-196">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="1817d-197">Le tabelle per le quali viene eseguito il mapping alle classi derivate non hanno alcuna relazione con la tabella che esegue il mapping alla classe di base nel database.</span><span class="sxs-lookup"><span data-stu-id="1817d-197">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="1817d-198">Per tutte le proprietà di una classe, incluse le proprietà ereditate, viene eseguito il mapping alle colonne della tabella corrispondente.</span><span class="sxs-lookup"><span data-stu-id="1817d-198">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="1817d-199">Chiamare il metodo MapInheritedProperties per configurare tutti i tipi derivati.</span><span class="sxs-lookup"><span data-stu-id="1817d-199">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="1817d-200">MapInheritedProperties esegue il mapping di tutte le proprietà ereditate dalla classe base a nuove colonne della tabella per la classe derivata.</span><span class="sxs-lookup"><span data-stu-id="1817d-200">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="1817d-201">Si noti che poiché le tabelle che fanno parte della gerarchia di ereditarietà TPC non condividono una chiave primaria, verranno utilizzate chiavi di entità duplicate durante l'inserimento in tabelle di cui è stato eseguito il mapping a sottoclassi se sono presenti valori generati dal database con lo stesso valore di inizializzazione Identity.</span><span class="sxs-lookup"><span data-stu-id="1817d-201">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="1817d-202">Per risolvere questo problema, è possibile specificare un valore di inizializzazione iniziale diverso per ogni tabella o disattivare l'identità sulla proprietà della chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="1817d-202">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="1817d-203">Identity è il valore predefinito per le proprietà chiave Integer quando si lavora con Code First.</span><span class="sxs-lookup"><span data-stu-id="1817d-203">Identity is the default value for integer key properties when working with Code First.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="1817d-204">Mapping delle proprietà di un tipo di entità a più tabelle nel database (suddivisione di entità)</span><span class="sxs-lookup"><span data-stu-id="1817d-204">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="1817d-205">La suddivisione delle entità consente di distribuire le proprietà di un tipo di entità in più tabelle.</span><span class="sxs-lookup"><span data-stu-id="1817d-205">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="1817d-206">Nell'esempio seguente l'entità Department è suddivisa in due tabelle: Department e DepartmentDetails.</span><span class="sxs-lookup"><span data-stu-id="1817d-206">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="1817d-207">La suddivisione delle entità utilizza più chiamate al metodo map per eseguire il mapping di un subset di proprietà a una tabella specifica.</span><span class="sxs-lookup"><span data-stu-id="1817d-207">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="1817d-208">Mapping di più tipi di entità a una tabella del database (suddivisione di tabelle)</span><span class="sxs-lookup"><span data-stu-id="1817d-208">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="1817d-209">Nell'esempio seguente viene eseguito il mapping di due tipi di entità che condividono una chiave primaria a una tabella.</span><span class="sxs-lookup"><span data-stu-id="1817d-209">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="1817d-210">Mapping di un tipo di entità alle stored procedure Insert/Update/Delete (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="1817d-210">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="1817d-211">A partire da EF6 è possibile eseguire il mapping di un'entità per l'utilizzo di stored procedure per l'inserimento di aggiornamenti e di eliminazione.</span><span class="sxs-lookup"><span data-stu-id="1817d-211">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="1817d-212">Per ulteriori informazioni, vedere [Code First stored procedure INSERT/UPDATE/DELETE](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span><span class="sxs-lookup"><span data-stu-id="1817d-212">For more details see, [Code First Insert/Update/Delete Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="1817d-213">Modello usato negli esempi</span><span class="sxs-lookup"><span data-stu-id="1817d-213">Model Used in Samples</span></span>  

<span data-ttu-id="1817d-214">Per gli esempi in questa pagina viene utilizzato il modello di Code First seguente.</span><span class="sxs-lookup"><span data-stu-id="1817d-214">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
