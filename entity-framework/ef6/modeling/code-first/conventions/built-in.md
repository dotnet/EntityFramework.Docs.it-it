---
title: Convenzioni Code First-EF6
description: Convenzioni di Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: 71e865d674d6bef7aaf65d95abe1b1add96d890f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072604"
---
# <a name="code-first-conventions"></a><span data-ttu-id="2288e-103">Convenzioni di Code First</span><span class="sxs-lookup"><span data-stu-id="2288e-103">Code First Conventions</span></span>
<span data-ttu-id="2288e-104">Code First consente di descrivere un modello usando le classi C# o Visual Basic .NET.</span><span class="sxs-lookup"><span data-stu-id="2288e-104">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="2288e-105">La forma di base del modello viene rilevata utilizzando le convenzioni.</span><span class="sxs-lookup"><span data-stu-id="2288e-105">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="2288e-106">Le convenzioni sono set di regole usate per configurare automaticamente un modello concettuale basato su definizioni di classe quando si lavora con Code First.</span><span class="sxs-lookup"><span data-stu-id="2288e-106">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="2288e-107">Le convenzioni sono definite nello spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions.</span><span class="sxs-lookup"><span data-stu-id="2288e-107">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="2288e-108">È possibile configurare ulteriormente il modello usando le annotazioni dei dati o l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="2288e-108">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="2288e-109">La precedenza viene assegnata alla configurazione tramite l'API Fluent seguita dalle annotazioni dei dati e quindi dalle convenzioni.</span><span class="sxs-lookup"><span data-stu-id="2288e-109">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="2288e-110">Per altre informazioni, vedere [annotazioni di dati](xref:ef6/modeling/code-first/data-annotations), [relazioni tra API Fluent](xref:ef6/modeling/code-first/fluent/relationships), [tipi di API Fluent & proprietà](xref:ef6/modeling/code-first/fluent/types-and-properties) e [API Fluent con VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span><span class="sxs-lookup"><span data-stu-id="2288e-110">For more information see [Data Annotations](xref:ef6/modeling/code-first/data-annotations), [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API - Types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span></span>  

<span data-ttu-id="2288e-111">Nella [documentazione dell'API](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)è disponibile un elenco dettagliato delle convenzioni Code First.</span><span class="sxs-lookup"><span data-stu-id="2288e-111">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="2288e-112">In questo argomento viene fornita una panoramica delle convenzioni utilizzate da Code First.</span><span class="sxs-lookup"><span data-stu-id="2288e-112">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="2288e-113">Individuazione del tipo</span><span class="sxs-lookup"><span data-stu-id="2288e-113">Type Discovery</span></span>  

<span data-ttu-id="2288e-114">Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio).</span><span class="sxs-lookup"><span data-stu-id="2288e-114">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="2288e-115">Oltre a definire le classi, è anche necessario consentire a **DbContext** di individuare i tipi che si desidera includere nel modello.</span><span class="sxs-lookup"><span data-stu-id="2288e-115">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="2288e-116">A tale scopo, si definisce una classe del contesto che deriva da **DbContext** ed espone le proprietà **DbSet** per i tipi che si desidera far parte del modello.</span><span class="sxs-lookup"><span data-stu-id="2288e-116">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="2288e-117">Code First includerà questi tipi e anche i tipi a cui si fa riferimento, anche se i tipi a cui si fa riferimento sono definiti in un assembly diverso.</span><span class="sxs-lookup"><span data-stu-id="2288e-117">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="2288e-118">Se i tipi fanno parte di una gerarchia di ereditarietà, è sufficiente definire una proprietà **DbSet** per la classe di base e i tipi derivati verranno inclusi automaticamente se si trovano nello stesso assembly della classe di base.</span><span class="sxs-lookup"><span data-stu-id="2288e-118">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="2288e-119">Nell'esempio seguente è presente una sola proprietà **DbSet** definita nella classe **SchoolEntities** (**Departments**).</span><span class="sxs-lookup"><span data-stu-id="2288e-119">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="2288e-120">Code First usa questa proprietà per individuare ed eseguire il pull di tutti i tipi a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="2288e-120">Code First uses this property to discover and pull in any referenced types.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

<span data-ttu-id="2288e-121">Se si vuole escludere un tipo dal modello, usare l'attributo **NotMapped** o l'API **DbModelBuilder. Ignora** Fluent.</span><span class="sxs-lookup"><span data-stu-id="2288e-121">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="2288e-122">Convenzione di chiave primaria</span><span class="sxs-lookup"><span data-stu-id="2288e-122">Primary Key Convention</span></span>  

<span data-ttu-id="2288e-123">Code First deduce che una proprietà è una chiave primaria se una proprietà di una classe è denominata "ID" (senza distinzione tra maiuscole e minuscole) o il nome della classe seguito da "ID".</span><span class="sxs-lookup"><span data-stu-id="2288e-123">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="2288e-124">Se il tipo della proprietà della chiave primaria è numeric o GUID, sarà configurato come colonna Identity.</span><span class="sxs-lookup"><span data-stu-id="2288e-124">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="2288e-125">Convenzione delle relazioni</span><span class="sxs-lookup"><span data-stu-id="2288e-125">Relationship Convention</span></span>  

<span data-ttu-id="2288e-126">In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="2288e-126">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="2288e-127">Ogni oggetto può disporre di una proprietà di navigazione per ogni relazione di cui fa parte.</span><span class="sxs-lookup"><span data-stu-id="2288e-127">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="2288e-128">Le proprietà di navigazione consentono di esplorare e gestire le relazioni in entrambe le direzioni, restituendo un oggetto di riferimento (se la molteplicità è uno o zero-o-uno) o una raccolta (se la molteplicità è molti).</span><span class="sxs-lookup"><span data-stu-id="2288e-128">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="2288e-129">Code First deduce le relazioni in base alle proprietà di navigazione definite nei tipi.</span><span class="sxs-lookup"><span data-stu-id="2288e-129">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="2288e-130">Oltre alle proprietà di navigazione, è consigliabile includere proprietà di chiave esterna per i tipi che rappresentano oggetti dipendenti.</span><span class="sxs-lookup"><span data-stu-id="2288e-130">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="2288e-131">Qualsiasi proprietà con lo stesso tipo di dati della proprietà della chiave primaria principale e con un nome che segue uno dei formati seguenti rappresenta una chiave esterna per la relazione:' \<navigation property name\> \<principal primary key property name\> ',' \<principal class name\> \<primary key property name\> ' o ' \<principal primary key property name\> '.</span><span class="sxs-lookup"><span data-stu-id="2288e-131">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="2288e-132">Se vengono rilevate più corrispondenze, viene specificata la precedenza nell'ordine indicato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="2288e-132">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="2288e-133">Il rilevamento della chiave esterna non distingue tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="2288e-133">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="2288e-134">Quando viene rilevata una proprietà di chiave esterna, Code First deduce la molteplicità della relazione in base al supporto di valori null della chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="2288e-134">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="2288e-135">Se la proprietà ammette i valori null, la relazione viene registrata come facoltativa. in caso contrario, la relazione viene registrata come obbligatoria.</span><span class="sxs-lookup"><span data-stu-id="2288e-135">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="2288e-136">Se una chiave esterna nell'entità dipendente non ammette i valori null, Code First imposta CASCADE DELETE per la relazione.</span><span class="sxs-lookup"><span data-stu-id="2288e-136">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="2288e-137">Se una chiave esterna nell'entità dipendente ammette i valori null, Code First non imposta CASCADE DELETE sulla relazione e quando l'entità viene eliminata, la chiave esterna viene impostata su null.</span><span class="sxs-lookup"><span data-stu-id="2288e-137">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="2288e-138">È possibile eseguire l'override del comportamento di molteplicità e eliminazione a catena rilevato dalla convenzione usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="2288e-138">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="2288e-139">Nell'esempio seguente vengono usate le proprietà di navigazione e una chiave esterna per definire la relazione tra le classi Department e Course.</span><span class="sxs-lookup"><span data-stu-id="2288e-139">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> <span data-ttu-id="2288e-140">Se sono presenti più relazioni tra gli stessi tipi (ad esempio, si supponga di definire le classi **Person** e **book** , in cui la classe **Person** contiene le proprietà di navigazione **ReviewedBooks** e **AuthoredBooks** e la classe **book** contiene le proprietà di navigazione **autore** e **revisore** ) è necessario configurare manualmente le relazioni usando le annotazioni dei dati o l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="2288e-140">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="2288e-141">Per altre informazioni, vedere [annotazioni dei dati-relazioni](xref:ef6/modeling/code-first/data-annotations) e [relazioni tra API Fluent](xref:ef6/modeling/code-first/fluent/relationships).</span><span class="sxs-lookup"><span data-stu-id="2288e-141">For more information, see [Data Annotations - Relationships](xref:ef6/modeling/code-first/data-annotations) and [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="2288e-142">Convenzione sui tipi complessi</span><span class="sxs-lookup"><span data-stu-id="2288e-142">Complex Types Convention</span></span>  

<span data-ttu-id="2288e-143">Quando Code First individua una definizione di classe in cui non è possibile dedurre una chiave primaria e nessuna chiave primaria viene registrata tramite le annotazioni dei dati o l'API Fluent, il tipo viene registrato automaticamente come tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="2288e-143">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="2288e-144">Il rilevamento di tipi complessi richiede anche che il tipo non disponga di proprietà che fanno riferimento ai tipi di entità e non vi viene fatto riferimento da una proprietà della raccolta in un altro tipo.</span><span class="sxs-lookup"><span data-stu-id="2288e-144">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="2288e-145">Date le seguenti definizioni di classe Code First dedurre che **i dettagli** sono un tipo complesso perché non ha una chiave primaria.</span><span class="sxs-lookup"><span data-stu-id="2288e-145">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

``` csharp
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
```  

## <a name="connection-string-convention"></a><span data-ttu-id="2288e-146">Convenzione della stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="2288e-146">Connection String Convention</span></span>  

<span data-ttu-id="2288e-147">Per informazioni sulle convenzioni utilizzate da DbContext per individuare la connessione da utilizzare [, vedere connessioni e modelli](xref:ef6/fundamentals/configuring/connection-strings).</span><span class="sxs-lookup"><span data-stu-id="2288e-147">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](xref:ef6/fundamentals/configuring/connection-strings).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="2288e-148">Rimozione delle convenzioni</span><span class="sxs-lookup"><span data-stu-id="2288e-148">Removing Conventions</span></span>  

<span data-ttu-id="2288e-149">È possibile rimuovere qualsiasi convenzione definita nello spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions.</span><span class="sxs-lookup"><span data-stu-id="2288e-149">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="2288e-150">Nell'esempio seguente viene rimosso **PluralizingTableNameConvention**.</span><span class="sxs-lookup"><span data-stu-id="2288e-150">The following example removes **PluralizingTableNameConvention**.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a><span data-ttu-id="2288e-151">Convenzioni personalizzate</span><span class="sxs-lookup"><span data-stu-id="2288e-151">Custom Conventions</span></span>  

<span data-ttu-id="2288e-152">Le convenzioni personalizzate sono supportate in EF6 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="2288e-152">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="2288e-153">Per ulteriori informazioni, vedere [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="2288e-153">For more information see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>
