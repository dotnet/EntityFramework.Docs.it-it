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
# <a name="code-first-conventions"></a>Convenzioni di Code First
Code First consente di descrivere un modello usando le classi C# o Visual Basic .NET. La forma di base del modello viene rilevata utilizzando le convenzioni. Le convenzioni sono set di regole usate per configurare automaticamente un modello concettuale basato su definizioni di classe quando si lavora con Code First. Le convenzioni sono definite nello spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions.  

È possibile configurare ulteriormente il modello usando le annotazioni dei dati o l'API Fluent. La precedenza viene assegnata alla configurazione tramite l'API Fluent seguita dalle annotazioni dei dati e quindi dalle convenzioni. Per altre informazioni, vedere [annotazioni di dati](xref:ef6/modeling/code-first/data-annotations), [relazioni tra API Fluent](xref:ef6/modeling/code-first/fluent/relationships), [tipi di API Fluent & proprietà](xref:ef6/modeling/code-first/fluent/types-and-properties) e [API Fluent con VB.NET](xref:ef6/modeling/code-first/fluent/vb).  

Nella [documentazione dell'API](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)è disponibile un elenco dettagliato delle convenzioni Code First. In questo argomento viene fornita una panoramica delle convenzioni utilizzate da Code First.  

## <a name="type-discovery"></a>Individuazione del tipo  

Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio). Oltre a definire le classi, è anche necessario consentire a **DbContext** di individuare i tipi che si desidera includere nel modello. A tale scopo, si definisce una classe del contesto che deriva da **DbContext** ed espone le proprietà **DbSet** per i tipi che si desidera far parte del modello. Code First includerà questi tipi e anche i tipi a cui si fa riferimento, anche se i tipi a cui si fa riferimento sono definiti in un assembly diverso.  

Se i tipi fanno parte di una gerarchia di ereditarietà, è sufficiente definire una proprietà **DbSet** per la classe di base e i tipi derivati verranno inclusi automaticamente se si trovano nello stesso assembly della classe di base.  

Nell'esempio seguente è presente una sola proprietà **DbSet** definita nella classe **SchoolEntities** (**Departments**). Code First usa questa proprietà per individuare ed eseguire il pull di tutti i tipi a cui si fa riferimento.  

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

Se si vuole escludere un tipo dal modello, usare l'attributo **NotMapped** o l'API **DbModelBuilder. Ignora** Fluent.  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>Convenzione di chiave primaria  

Code First deduce che una proprietà è una chiave primaria se una proprietà di una classe è denominata "ID" (senza distinzione tra maiuscole e minuscole) o il nome della classe seguito da "ID". Se il tipo della proprietà della chiave primaria è numeric o GUID, sarà configurato come colonna Identity.  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>Convenzione delle relazioni  

In Entity Framework, le proprietà di navigazione consentono di spostarsi in una relazione tra due tipi di entità. Ogni oggetto può disporre di una proprietà di navigazione per ogni relazione di cui fa parte. Le proprietà di navigazione consentono di esplorare e gestire le relazioni in entrambe le direzioni, restituendo un oggetto di riferimento (se la molteplicità è uno o zero-o-uno) o una raccolta (se la molteplicità è molti). Code First deduce le relazioni in base alle proprietà di navigazione definite nei tipi.  

Oltre alle proprietà di navigazione, è consigliabile includere proprietà di chiave esterna per i tipi che rappresentano oggetti dipendenti. Qualsiasi proprietà con lo stesso tipo di dati della proprietà della chiave primaria principale e con un nome che segue uno dei formati seguenti rappresenta una chiave esterna per la relazione:' \<navigation property name\> \<principal primary key property name\> ',' \<principal class name\> \<primary key property name\> ' o ' \<principal primary key property name\> '. Se vengono rilevate più corrispondenze, viene specificata la precedenza nell'ordine indicato in precedenza. Il rilevamento della chiave esterna non distingue tra maiuscole e minuscole. Quando viene rilevata una proprietà di chiave esterna, Code First deduce la molteplicità della relazione in base al supporto di valori null della chiave esterna. Se la proprietà ammette i valori null, la relazione viene registrata come facoltativa. in caso contrario, la relazione viene registrata come obbligatoria.  

Se una chiave esterna nell'entità dipendente non ammette i valori null, Code First imposta CASCADE DELETE per la relazione. Se una chiave esterna nell'entità dipendente ammette i valori null, Code First non imposta CASCADE DELETE sulla relazione e quando l'entità viene eliminata, la chiave esterna viene impostata su null. È possibile eseguire l'override del comportamento di molteplicità e eliminazione a catena rilevato dalla convenzione usando l'API Fluent.  

Nell'esempio seguente vengono usate le proprietà di navigazione e una chiave esterna per definire la relazione tra le classi Department e Course.  

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
> Se sono presenti più relazioni tra gli stessi tipi (ad esempio, si supponga di definire le classi **Person** e **book** , in cui la classe **Person** contiene le proprietà di navigazione **ReviewedBooks** e **AuthoredBooks** e la classe **book** contiene le proprietà di navigazione **autore** e **revisore** ) è necessario configurare manualmente le relazioni usando le annotazioni dei dati o l'API Fluent. Per altre informazioni, vedere [annotazioni dei dati-relazioni](xref:ef6/modeling/code-first/data-annotations) e [relazioni tra API Fluent](xref:ef6/modeling/code-first/fluent/relationships).  

## <a name="complex-types-convention"></a>Convenzione sui tipi complessi  

Quando Code First individua una definizione di classe in cui non è possibile dedurre una chiave primaria e nessuna chiave primaria viene registrata tramite le annotazioni dei dati o l'API Fluent, il tipo viene registrato automaticamente come tipo complesso. Il rilevamento di tipi complessi richiede anche che il tipo non disponga di proprietà che fanno riferimento ai tipi di entità e non vi viene fatto riferimento da una proprietà della raccolta in un altro tipo. Date le seguenti definizioni di classe Code First dedurre che **i dettagli** sono un tipo complesso perché non ha una chiave primaria.  

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

## <a name="connection-string-convention"></a>Convenzione della stringa di connessione  

Per informazioni sulle convenzioni utilizzate da DbContext per individuare la connessione da utilizzare [, vedere connessioni e modelli](xref:ef6/fundamentals/configuring/connection-strings).  

## <a name="removing-conventions"></a>Rimozione delle convenzioni  

È possibile rimuovere qualsiasi convenzione definita nello spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions. Nell'esempio seguente viene rimosso **PluralizingTableNameConvention**.  

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

## <a name="custom-conventions"></a>Convenzioni personalizzate  

Le convenzioni personalizzate sono supportate in EF6 e versioni successive. Per ulteriori informazioni, vedere [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).
