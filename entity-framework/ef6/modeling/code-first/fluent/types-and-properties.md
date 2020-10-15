---
title: API Fluent-configurazione e mapping di proprietà e tipi-EF6
description: API Fluent-configurazione e mapping di proprietà e tipi in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: 821672bcb797314c96189443ace7f875a79c8582
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065147"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>API Fluent-configurazione e mapping di proprietà e tipi
Quando si lavora con Entity Framework Code First il comportamento predefinito consiste nel eseguire il mapping delle classi POCO alle tabelle usando un set di convenzioni di cui è stato eseguito il bake in EF. In alcuni casi, tuttavia, non è possibile o non si desidera seguire tali convenzioni ed è necessario eseguire il mapping delle entità a un valore diverso da quello che le convenzioni dettano.  

Esistono due modi principali per configurare Entity Framework in modo da usare un valore diverso dalle convenzioni, ovvero le [annotazioni](xref:ef6/modeling/code-first/data-annotations) o l'API di EFS Fluent. Le annotazioni coprono solo un subset della funzionalità dell'API Fluent, quindi esistono scenari di mapping che non possono essere consentiti utilizzando le annotazioni. Questo articolo è stato progettato per illustrare come usare l'API Fluent per configurare le proprietà.  

L'accesso all'API Fluent Code First viene eseguito più di frequente eseguendo l'override del metodo [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) sul [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)derivato. Gli esempi seguenti sono progettati per illustrare come eseguire varie attività con l'API Fluent e consentono di copiare il codice e personalizzarlo in base al modello, se si desidera visualizzare il modello che può essere utilizzato con così com'è, viene fornito alla fine di questo articolo.  

## <a name="model-wide-settings"></a>Impostazioni Model-Wide  

### <a name="default-schema-ef6-onwards"></a>Schema predefinito (EF6 e versioni successive)  

A partire da EF6, è possibile usare il metodo HasDefaultSchema in DbModelBuilder per specificare lo schema del database da usare per tutte le tabelle, le stored procedure e così via. Questa impostazione predefinita verrà sostituita per tutti gli oggetti per i quali si configura in modo esplicito uno schema diverso.  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>Convenzioni personalizzate (EF6 e versioni successive)  

A partire da EF6 è possibile creare convenzioni personalizzate per integrare quelle incluse in Code First. Per ulteriori informazioni, vedere [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).  

## <a name="property-mapping"></a>Mapping di proprietà  

Il metodo [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) viene utilizzato per configurare gli attributi per ogni proprietà appartenente a un tipo di entità o complesso. Il metodo Property viene usato per ottenere un oggetto di configurazione per una determinata proprietà. Le opzioni nell'oggetto di configurazione sono specifiche del tipo da configurare. L'estensione Unicode è disponibile solo per le proprietà di stringa, ad esempio.  

### <a name="configuring-a-primary-key"></a>Configurazione di una chiave primaria  

La convenzione Entity Framework per le chiavi primarie è la seguente:  

1. La classe definisce una proprietà il cui nome è "ID" o "ID"  
2. o un nome di classe seguito da "ID" o "ID"  

Per impostare in modo esplicito una proprietà su una chiave primaria, è possibile usare il metodo HasKey. Nell'esempio seguente viene usato il metodo HasKey per configurare la chiave primaria InstructorID sul tipo OfficeAssignment.  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>Configurazione di una chiave primaria composta  

Nell'esempio seguente vengono configurate le proprietà DepartmentID e Name in modo che siano la chiave primaria composta del tipo di reparto.  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>Spegnimento dell'identità per le chiavi primarie numeriche  

Nell'esempio seguente la proprietà DepartmentID viene impostata su System. ComponentModel. DataAnnotations. DatabaseGeneratedOption. None per indicare che il valore non verrà generato dal database.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>Specifica della lunghezza massima di una proprietà  

Nell'esempio seguente la proprietà Name non può contenere più di 50 caratteri. Se il valore viene reso più lungo di 50 caratteri, si otterrà un'eccezione [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) . Se Code First crea un database da questo modello, verrà impostata anche la lunghezza massima della colonna nome su 50 caratteri.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>Configurazione della proprietà in modo che sia obbligatoria  

Nell'esempio seguente, la proprietà Name è obbligatoria. Se non si specifica il nome, si otterrà un'eccezione DbEntityValidationException. Se Code First crea un database da questo modello, la colonna utilizzata per archiviare questa proprietà sarà in genere non nullable.  

> [!NOTE]
> In alcuni casi potrebbe non essere possibile che la colonna nel database sia non nullable anche se la proprietà è obbligatoria. Ad esempio, quando si usano i dati della strategia di ereditarietà TPH per più tipi, viene archiviato in una singola tabella. Se un tipo derivato include una proprietà obbligatoria, la colonna non può essere resa non nullable perché non tutti i tipi nella gerarchia avranno questa proprietà.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>Configurazione di un indice in una o più proprietà  

> [!NOTE]
> **Ef 6.1 e versioni successive** : l'attributo index è stato introdotto in Entity Framework 6,1. Se si usa una versione precedente, le informazioni contenute in questa sezione non sono valide.  

La creazione di indici non è supportata in modo nativo dall'API Fluent, ma è possibile usare il supporto per **IndexAttribute** tramite l'API Fluent. Gli attributi degli indici vengono elaborati includendo un'annotazione del modello nel modello che viene quindi trasformata in un indice del database in un secondo momento nella pipeline. È possibile aggiungere manualmente le stesse annotazioni usando l'API Fluent.  

Il modo più semplice per eseguire questa operazione consiste nel creare un'istanza di **IndexAttribute** contenente tutte le impostazioni per il nuovo indice. È quindi possibile creare un'istanza di **IndexAnnotation** che è un tipo specifico di EF che converte le impostazioni **IndexAttribute** in un'annotazione del modello che può essere archiviata nel modello EF. Questi possono quindi essere passati al metodo **HasColumnAnnotation** sull'API Fluent, specificando l' **Indice** del nome per l'annotazione.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

Per un elenco completo delle impostazioni disponibili in **IndexAttribute**, vedere la sezione relativa all' *Indice* delle [annotazioni dei dati Code First](xref:ef6/modeling/code-first/data-annotations). Sono incluse la personalizzazione del nome dell'indice, la creazione di indici univoci e la creazione di indici a più colonne.  

È possibile specificare più annotazioni di indice in una singola proprietà passando una matrice di **IndexAttribute** al costruttore di **IndexAnnotation**.  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>Specifica di non eseguire il mapping di una proprietà CLR a una colonna nel database  

Nell'esempio seguente viene illustrato come specificare che una proprietà in un tipo CLR non è mappata a una colonna nel database.  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>Mapping di una proprietà CLR a una colonna specifica nel database  

Nell'esempio seguente viene eseguito il mapping della proprietà CLR Name alla colonna di database DepartmentName.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Ridenominazione di una chiave esterna che non è definita nel modello  

Se si sceglie di non definire una chiave esterna in un tipo CLR, ma si desidera specificare il nome che deve avere nel database, eseguire le operazioni seguenti:  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>Configurazione se una proprietà stringa supporta il contenuto Unicode  

Per impostazione predefinita, le stringhe sono Unicode (nvarchar in SQL Server). È possibile utilizzare il metodo IsValid per specificare che una stringa deve essere di tipo varchar.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>Configurazione del tipo di dati di una colonna di database  

Il metodo [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) consente il mapping a rappresentazioni diverse dello stesso tipo di base. L'utilizzo di questo metodo non consente di eseguire alcuna conversione dei dati in fase di esecuzione. Si noti che è il modo migliore per impostare le colonne su varchar, perché è indipendente dal database.  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>Configurazione di proprietà su un tipo complesso  

Esistono due modi per configurare le proprietà scalari in un tipo complesso.  

È possibile chiamare la proprietà su ComplexTypeConfiguration.  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

È anche possibile usare la notazione del punto per accedere a una proprietà di un tipo complesso.  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>Configurazione di una proprietà da utilizzare come token di concorrenza ottimistica  

Per specificare che una proprietà in un'entità rappresenta un token di concorrenza, è possibile usare l'attributo ConcurrencyCheck o il metodo IsConcurrencyToken.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

È anche possibile usare il metodo IsRowVersion per configurare la proprietà in modo che sia una versione di riga nel database. Impostando la proprietà su una versione di riga, questa viene configurata automaticamente in modo che sia un token di concorrenza ottimistica.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>Mapping dei tipi  

### <a name="specifying-that-a-class-is-a-complex-type"></a>Specifica che una classe è un tipo complesso  

Per convenzione, un tipo che non ha una chiave primaria specificata viene considerato come un tipo complesso. Esistono scenari in cui Code First non rileva un tipo complesso, ad esempio se si dispone di una proprietà denominata ID, ma non si intende che sia una chiave primaria. In questi casi, si utilizzerà l'API Fluent per specificare in modo esplicito che un tipo è un tipo complesso.  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>Specifica di non eseguire il mapping di un tipo di entità CLR a una tabella nel database  

Nell'esempio seguente viene illustrato come escludere un tipo CLR dal mapping a una tabella nel database.  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>Mapping di un tipo di entità a una tabella specifica nel database  

Tutte le proprietà del reparto verranno mappate alle colonne in una tabella denominata t_ Department.  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

È inoltre possibile specificare il nome dello schema come segue:  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>Mapping dell'ereditarietà tabella per gerarchia (TPH)  

Nello scenario di mapping di TPH, viene eseguito il mapping di tutti i tipi in una gerarchia di ereditarietà a una singola tabella. Una colonna discriminatore viene utilizzata per identificare il tipo di ogni riga. Quando si crea il modello con Code First, TPH è la strategia predefinita per i tipi che fanno parte della gerarchia di ereditarietà. Per impostazione predefinita, la colonna discriminatore viene aggiunta alla tabella con il nome "discriminatore" e il nome del tipo CLR di ogni tipo nella gerarchia viene usato per i valori del discriminatore. È possibile modificare il comportamento predefinito usando l'API Fluent.  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>Mapping dell'ereditarietà tabella per tipo (TPT)  

Nello scenario di mapping di TPT, viene eseguito il mapping di tutti i tipi a singole tabelle. Le proprietà che appartengono esclusivamente a un tipo di base o derivato sono archiviate in una tabella che viene mappata a quel tipo. Le tabelle con mapping ai tipi derivati archiviano anche una chiave esterna che unisce la tabella derivata alla tabella di base.  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>Mapping dell'ereditarietà della classe tabella per calcestruzzo (TPC)  

Nello scenario di mapping TPC, viene eseguito il mapping di tutti i tipi non astratti della gerarchia a singole tabelle. Le tabelle per le quali viene eseguito il mapping alle classi derivate non hanno alcuna relazione con la tabella che esegue il mapping alla classe di base nel database. Per tutte le proprietà di una classe, incluse le proprietà ereditate, viene eseguito il mapping alle colonne della tabella corrispondente.  

Chiamare il metodo MapInheritedProperties per configurare tutti i tipi derivati. MapInheritedProperties esegue il mapping di tutte le proprietà ereditate dalla classe base a nuove colonne della tabella per la classe derivata.  

> [!NOTE]
> Si noti che poiché le tabelle che fanno parte della gerarchia di ereditarietà TPC non condividono una chiave primaria, verranno utilizzate chiavi di entità duplicate durante l'inserimento in tabelle di cui è stato eseguito il mapping a sottoclassi se sono presenti valori generati dal database con lo stesso valore di inizializzazione Identity. Per risolvere questo problema, è possibile specificare un valore di inizializzazione iniziale diverso per ogni tabella o disattivare l'identità sulla proprietà della chiave primaria. Identity è il valore predefinito per le proprietà chiave Integer quando si lavora con Code First.  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>Mapping delle proprietà di un tipo di entità a più tabelle nel database (suddivisione di entità)  

La suddivisione delle entità consente di distribuire le proprietà di un tipo di entità in più tabelle. Nell'esempio seguente l'entità Department è suddivisa in due tabelle: Department e DepartmentDetails. La suddivisione delle entità utilizza più chiamate al metodo map per eseguire il mapping di un subset di proprietà a una tabella specifica.  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>Mapping di più tipi di entità a una tabella del database (suddivisione di tabelle)  

Nell'esempio seguente viene eseguito il mapping di due tipi di entità che condividono una chiave primaria a una tabella.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>Mapping di un tipo di entità alle stored procedure Insert/Update/Delete (EF6 e versioni successive)  

A partire da EF6 è possibile eseguire il mapping di un'entità per l'utilizzo di stored procedure per l'inserimento di aggiornamenti e di eliminazione. Per ulteriori informazioni, vedere [Code First stored procedure INSERT/UPDATE/DELETE](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).  

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
