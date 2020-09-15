---
title: API Fluent-relazioni-EF6
description: Relazioni API Fluent in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 73e910279540fc93845aeebdfd79af9b95c60fae
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073925"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="0ce0b-103">API Fluent-relazioni</span><span class="sxs-lookup"><span data-stu-id="0ce0b-103">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="0ce0b-104">Questa pagina fornisce informazioni sulla configurazione delle relazioni nel modello di Code First usando l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-104">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="0ce0b-105">Per informazioni generali sulle relazioni in EF e su come accedere e modificare i dati usando le relazioni, vedere [relazioni & proprietà di navigazione](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="0ce0b-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>  

<span data-ttu-id="0ce0b-106">Quando si lavora con Code First, si definisce il modello definendo le classi CLR del dominio.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-106">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="0ce0b-107">Per impostazione predefinita, Entity Framework utilizza le convenzioni di Code First per eseguire il mapping delle classi allo schema del database.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-107">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="0ce0b-108">Se si usano le convenzioni di denominazione Code First, nella maggior parte dei casi è possibile basarsi su Code First per configurare le relazioni tra le tabelle in base alle chiavi esterne e alle proprietà di navigazione definite nelle classi.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-108">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="0ce0b-109">Se non si seguono le convenzioni durante la definizione delle classi o se si desidera modificare la modalità di funzionamento delle convenzioni, è possibile utilizzare le annotazioni di dati o API Fluent per configurare le classi in modo Code First possibile eseguire il mapping delle relazioni tra le tabelle.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-109">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="0ce0b-110">Introduzione</span><span class="sxs-lookup"><span data-stu-id="0ce0b-110">Introduction</span></span>  

<span data-ttu-id="0ce0b-111">Quando si configura una relazione con l'API Fluent, si inizia con l'istanza di EntityTypeConfiguration e quindi si usa il metodo HasRequired, HasOptional o HasMany per specificare il tipo di relazione a cui partecipa questa entità.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-111">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="0ce0b-112">I metodi HasRequired e HasOptional accettano un'espressione lambda che rappresenta una proprietà di navigazione di riferimento.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-112">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="0ce0b-113">Il Metodo HasMany accetta un'espressione lambda che rappresenta una proprietà di navigazione della raccolta.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-113">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="0ce0b-114">È quindi possibile configurare una proprietà di navigazione inversa usando i metodi WithRequired, WithOptional e WithMany.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-114">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="0ce0b-115">Questi metodi hanno overload che non accettano argomenti e possono essere usati per specificare la cardinalità con le navigazioni unidirezionali.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-115">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="0ce0b-116">È quindi possibile configurare le proprietà di chiave esterna usando il metodo HasForeignKey.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-116">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="0ce0b-117">Questo metodo accetta un'espressione lambda che rappresenta la proprietà da utilizzare come chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-117">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="0ce0b-118">Configurazione di una relazione obbligatoria-a-facoltativa (uno-a-zero-o-uno)</span><span class="sxs-lookup"><span data-stu-id="0ce0b-118">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="0ce0b-119">Nell'esempio seguente viene configurata una relazione uno-a-zero-o-uno.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-119">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="0ce0b-120">OfficeAssignment dispone della proprietà InstructorID che è una chiave primaria e una chiave esterna, perché il nome della proprietà non segue la convenzione per la configurazione della chiave primaria, viene usato il metodo HasKey.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-120">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="0ce0b-121">Configurazione di una relazione in cui entrambe le entità finali sono obbligatorie (uno-a-uno)</span><span class="sxs-lookup"><span data-stu-id="0ce0b-121">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="0ce0b-122">Nella maggior parte dei casi Entity Framework possibile dedurre il tipo di dipendente e che rappresenta l'entità in una relazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-122">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="0ce0b-123">Tuttavia, quando entrambe le entità finali della relazione sono obbligatorie o entrambi i lati sono facoltativi Entity Framework non possono identificare il dipendente e l'entità.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-123">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="0ce0b-124">Quando entrambe le entità finali della relazione sono obbligatorie, utilizzare WithRequiredPrincipal o WithRequiredDependent dopo il metodo HasRequired.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-124">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="0ce0b-125">Quando entrambe le entità finali della relazione sono facoltative, utilizzare WithOptionalPrincipal o WithOptionalDependent dopo il metodo HasOptional.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-125">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="0ce0b-126">Configurazione di una relazione molti-a-molti</span><span class="sxs-lookup"><span data-stu-id="0ce0b-126">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="0ce0b-127">Il codice seguente configura una relazione molti-a-molti tra i tipi Course e Instructor.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-127">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="0ce0b-128">Nell'esempio seguente vengono utilizzate le convenzioni Code First predefinite per creare una tabella di join.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-128">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="0ce0b-129">Di conseguenza la tabella CourseInstructor viene creata con Course_CourseID e Instructor_InstructorID colonne.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-129">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="0ce0b-130">Se si desidera specificare il nome della tabella di join e i nomi delle colonne nella tabella, è necessario eseguire una configurazione aggiuntiva tramite il metodo map.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-130">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="0ce0b-131">Il codice seguente genera la tabella CourseInstructor con le colonne CourseID e InstructorID.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-131">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="0ce0b-132">Configurazione di una relazione con una proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="0ce0b-132">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="0ce0b-133">Una relazione unidirezionale (detta anche unidirezionale) si verifica quando una proprietà di navigazione viene definita su una sola delle relazioni che termina e non su entrambe.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-133">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="0ce0b-134">Per convenzione, Code First interpreta sempre una relazione unidirezionale come uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-134">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="0ce0b-135">Se ad esempio si vuole una relazione uno-a-uno tra Instructor e OfficeAssignment, in cui è presente una proprietà di navigazione solo sul tipo di insegnante, è necessario usare l'API Fluent per configurare questa relazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-135">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="0ce0b-136">Abilitazione dell'eliminazione a catena</span><span class="sxs-lookup"><span data-stu-id="0ce0b-136">Enabling Cascade Delete</span></span>  

<span data-ttu-id="0ce0b-137">È possibile configurare l'eliminazione a catena in una relazione tramite il metodo WillCascadeOnDelete.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-137">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="0ce0b-138">Se una chiave esterna nell'entità dipendente non ammette i valori null, Code First imposta CASCADE DELETE per la relazione.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-138">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="0ce0b-139">Se una chiave esterna nell'entità dipendente ammette i valori null, Code First non imposta CASCADE DELETE sulla relazione e quando l'entità viene eliminata, la chiave esterna viene impostata su null.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-139">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="0ce0b-140">È possibile rimuovere le convenzioni di eliminazione a catena utilizzando:</span><span class="sxs-lookup"><span data-stu-id="0ce0b-140">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="0ce0b-141">modelBuilder. Conventions. Remove \<OneToManyCascadeDeleteConvention\> ()</span><span class="sxs-lookup"><span data-stu-id="0ce0b-141">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="0ce0b-142">modelBuilder. Conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()</span><span class="sxs-lookup"><span data-stu-id="0ce0b-142">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="0ce0b-143">Il codice seguente configura la relazione in modo che sia richiesta e quindi Disabilita l'eliminazione a catena.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-143">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="0ce0b-144">Configurazione di una chiave esterna composta</span><span class="sxs-lookup"><span data-stu-id="0ce0b-144">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="0ce0b-145">Se la chiave primaria del tipo di reparto è costituita dalle proprietà DepartmentID e Name, configurare la chiave primaria per il reparto e la chiave esterna per i tipi di corso come indicato di seguito:</span><span class="sxs-lookup"><span data-stu-id="0ce0b-145">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="0ce0b-146">Ridenominazione di una chiave esterna che non è definita nel modello</span><span class="sxs-lookup"><span data-stu-id="0ce0b-146">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="0ce0b-147">Se si sceglie di non definire una chiave esterna per il tipo CLR, ma si desidera specificare il nome che deve avere nel database, eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="0ce0b-147">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="0ce0b-148">Configurazione di un nome di chiave esterna che non segue la convenzione di Code First</span><span class="sxs-lookup"><span data-stu-id="0ce0b-148">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="0ce0b-149">Se la proprietà della chiave esterna della classe Course è stata chiamata SomeDepartmentID anziché DepartmentID, è necessario eseguire le operazioni seguenti per specificare che SomeDepartmentID deve essere la chiave esterna:</span><span class="sxs-lookup"><span data-stu-id="0ce0b-149">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="0ce0b-150">Modello usato negli esempi</span><span class="sxs-lookup"><span data-stu-id="0ce0b-150">Model Used in Samples</span></span>  

<span data-ttu-id="0ce0b-151">Per gli esempi in questa pagina viene utilizzato il modello di Code First seguente.</span><span class="sxs-lookup"><span data-stu-id="0ce0b-151">The following Code First model is used for the samples on this page.</span></span>  

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
