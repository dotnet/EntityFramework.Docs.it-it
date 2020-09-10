---
title: API Fluent-relazioni-EF6
description: Relazioni API Fluent in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 8cc56f7341df6da7f60f649308ea7042ef23b537
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616998"
---
# <a name="fluent-api---relationships"></a>API Fluent-relazioni
> [!NOTE]
> Questa pagina fornisce informazioni sulla configurazione delle relazioni nel modello di Code First usando l'API Fluent. Per informazioni generali sulle relazioni in EF e su come accedere e modificare i dati usando le relazioni, vedere [relazioni & proprietà di navigazione](xref:ef6/fundamentals/relationships).  

Quando si lavora con Code First, si definisce il modello definendo le classi CLR del dominio. Per impostazione predefinita, Entity Framework utilizza le convenzioni di Code First per eseguire il mapping delle classi allo schema del database. Se si usano le convenzioni di denominazione Code First, nella maggior parte dei casi è possibile basarsi su Code First per configurare le relazioni tra le tabelle in base alle chiavi esterne e alle proprietà di navigazione definite nelle classi. Se non si seguono le convenzioni durante la definizione delle classi o se si desidera modificare la modalità di funzionamento delle convenzioni, è possibile utilizzare le annotazioni di dati o API Fluent per configurare le classi in modo Code First possibile eseguire il mapping delle relazioni tra le tabelle.  

## <a name="introduction"></a>Introduzione  

Quando si configura una relazione con l'API Fluent, si inizia con l'istanza di EntityTypeConfiguration e quindi si usa il metodo HasRequired, HasOptional o HasMany per specificare il tipo di relazione a cui partecipa questa entità. I metodi HasRequired e HasOptional accettano un'espressione lambda che rappresenta una proprietà di navigazione di riferimento. Il Metodo HasMany accetta un'espressione lambda che rappresenta una proprietà di navigazione della raccolta. È quindi possibile configurare una proprietà di navigazione inversa usando i metodi WithRequired, WithOptional e WithMany. Questi metodi hanno overload che non accettano argomenti e possono essere usati per specificare la cardinalità con le navigazioni unidirezionali.  

È quindi possibile configurare le proprietà di chiave esterna usando il metodo HasForeignKey. Questo metodo accetta un'espressione lambda che rappresenta la proprietà da utilizzare come chiave esterna.  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>Configurazione di una relazione obbligatoria-a-facoltativa (uno-a-zero-o-uno)  

Nell'esempio seguente viene configurata una relazione uno-a-zero-o-uno. OfficeAssignment dispone della proprietà InstructorID che è una chiave primaria e una chiave esterna, perché il nome della proprietà non segue la convenzione per la configurazione della chiave primaria, viene usato il metodo HasKey.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>Configurazione di una relazione in cui entrambe le entità finali sono obbligatorie (uno-a-uno)  

Nella maggior parte dei casi Entity Framework possibile dedurre il tipo di dipendente e che rappresenta l'entità in una relazione. Tuttavia, quando entrambe le entità finali della relazione sono obbligatorie o entrambi i lati sono facoltativi Entity Framework non possono identificare il dipendente e l'entità. Quando entrambe le entità finali della relazione sono obbligatorie, utilizzare WithRequiredPrincipal o WithRequiredDependent dopo il metodo HasRequired. Quando entrambe le entità finali della relazione sono facoltative, utilizzare WithOptionalPrincipal o WithOptionalDependent dopo il metodo HasOptional.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>Configurazione di una relazione molti-a-molti  

Il codice seguente configura una relazione molti-a-molti tra i tipi Course e Instructor. Nell'esempio seguente vengono utilizzate le convenzioni Code First predefinite per creare una tabella di join. Di conseguenza la tabella CourseInstructor viene creata con Course_CourseID e Instructor_InstructorID colonne.  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

Se si desidera specificare il nome della tabella di join e i nomi delle colonne nella tabella, è necessario eseguire una configurazione aggiuntiva tramite il metodo map. Il codice seguente genera la tabella CourseInstructor con le colonne CourseID e InstructorID.  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>Configurazione di una relazione con una proprietà di navigazione  

Una relazione unidirezionale (detta anche unidirezionale) si verifica quando una proprietà di navigazione viene definita su una sola delle relazioni che termina e non su entrambe. Per convenzione, Code First interpreta sempre una relazione unidirezionale come uno-a-molti. Se ad esempio si vuole una relazione uno-a-uno tra Instructor e OfficeAssignment, in cui è presente una proprietà di navigazione solo sul tipo di insegnante, è necessario usare l'API Fluent per configurare questa relazione.  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>Abilitazione dell'eliminazione a catena  

È possibile configurare l'eliminazione a catena in una relazione tramite il metodo WillCascadeOnDelete. Se una chiave esterna nell'entità dipendente non ammette i valori null, Code First imposta CASCADE DELETE per la relazione. Se una chiave esterna nell'entità dipendente ammette i valori null, Code First non imposta CASCADE DELETE sulla relazione e quando l'entità viene eliminata, la chiave esterna viene impostata su null.  

È possibile rimuovere le convenzioni di eliminazione a catena utilizzando:  

modelBuilder. Conventions. Remove \<OneToManyCascadeDeleteConvention\> ()  
modelBuilder. Conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()  

Il codice seguente configura la relazione in modo che sia richiesta e quindi Disabilita l'eliminazione a catena.  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>Configurazione di una chiave esterna composta  

Se la chiave primaria del tipo di reparto è costituita dalle proprietà DepartmentID e Name, configurare la chiave primaria per il reparto e la chiave esterna per i tipi di corso come indicato di seguito:  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Ridenominazione di una chiave esterna che non è definita nel modello  

Se si sceglie di non definire una chiave esterna per il tipo CLR, ma si desidera specificare il nome che deve avere nel database, eseguire le operazioni seguenti:  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>Configurazione di un nome di chiave esterna che non segue la convenzione di Code First  

Se la proprietà della chiave esterna della classe Course è stata chiamata SomeDepartmentID anziché DepartmentID, è necessario eseguire le operazioni seguenti per specificare che SomeDepartmentID deve essere la chiave esterna:  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>Modello usato negli esempi  

Per gli esempi in questa pagina viene utilizzato il modello di Code First seguente.  

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
