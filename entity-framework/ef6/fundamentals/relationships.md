---
title: Relazioni, proprietà di navigazione e chiavi esterne - EF6Relationships, navigation properties, and foreign keys - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434326"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relazioni, proprietà di navigazione e chiavi esterne

In questo articolo viene fornita una panoramica del modo in cui Entity Framework gestisce le relazioni tra le entità. Fornisce inoltre alcune indicazioni su come mappare e manipolare le relazioni.

## <a name="relationships-in-ef"></a>Relazioni in EF

Nei database relazionali, le relazioni (chiamate anche associazioni) tra tabelle vengono definite tramite chiavi esterne. Per chiave esterna si intende una colonna o combinazione di colonne utilizzata per stabilire e applicare un collegamento tra i dati di due tabelle. Esistono in genere tre tipi di relazioni: uno-a-uno, uno-a-molti, e molti-a-molti. In una relazione uno-a-molti, la chiave esterna viene definita nella tabella che rappresenta l'entità finale molti della relazione. La relazione molti-a-molti comporta la definizione di una terza tabella (denominata tabella di collegamento o di join), la cui chiave primaria è composta dalle chiavi esterne di entrambe le tabelle correlate. In una relazione uno-a-uno, la chiave primaria funge inoltre da chiave esterna e non esiste una colonna di chiave esterna separata per entrambe le tabelle.

L'immagine seguente mostra due tabelle che fanno parte di una relazione uno-a-molti. La tabella **Course** è la tabella dipendente perché contiene la colonna **DepartmentID** che la collega alla tabella **Department.**

![Tavoli Reparto e Corso](~/ef6/media/database2.png)

In Entity Framework, un'entità può essere correlata ad altre entità tramite un'associazione o una relazione. Ogni relazione contiene due estremità che descrivono il tipo di entità e la molteplicità del tipo (uno, zero o uno o molti) per le due entità in tale relazione. La relazione può essere regolata da un vincolo referenziale che descrive quale entità finale nella relazione riveste il ruolo principale e quale quello di dipendente.

Le proprietà di navigazione consentono di esplorare un'associazione tra due tipi di entità. Ogni oggetto può disporre di una proprietà di navigazione per ogni relazione di cui fa parte. Le proprietà di navigazione consentono di esplorare e gestire le relazioni in entrambe le direzioni, restituendo un oggetto di riferimento (se la molteplicità è uno o zero o uno) o una raccolta (se la molteplicità è molti). È inoltre possibile scegliere di eseguire la navigazione unidirezionale, nel qual caso si definisce la proprietà di navigazione solo su uno dei tipi che partecipano alla relazione e non su entrambi.

È consigliabile includere nel modello le proprietà che eseguono il mapping alle chiavi esterne nel database. Con le proprietà di chiave esterna incluse, è possibile creare o modificare una relazione cambiando il valore della chiave esterna in un oggetto dipendente. Questo tipo di associazione viene definito associazione di chiavi esterne. L'uso di chiavi esterne è ancora più essenziale quando si lavora con entità disconnesse. Si noti che quando si lavora con 1-a-1 o 1-a-0.. 1 relazioni, non esiste una colonna di chiave esterna separata, la proprietà di chiave primaria funge da chiave esterna ed è sempre inclusa nel modello.

Quando le colonne di chiave esterna non sono incluse nel modello, le informazioni sull'associazione vengono gestite come oggetto indipendente. Le relazioni vengono rilevate tramite riferimenti a oggetti anziché proprietà di chiave esterna. Questo tipo di associazione è denominato *associazione indipendente.* Il modo più comune per modificare *un'associazione indipendente* consiste nel modificare le proprietà di navigazione generate per ogni entità che fa parte dell'associazione.

È possibile scegliere di utilizzare uno o entrambi i tipi di associazioni nel modello. Tuttavia, se si dispone di una relazione molti-a-molti pura connessa da una tabella di join che contiene solo chiavi esterne, il server DiF utilizzerà un'associazione indipendente per gestire tale relazione molti-a-molti.   

Nell'immagine seguente viene illustrato un modello concettuale creato con Entity Framework Designer. Il modello contiene due entità che partecipano alla relazione uno-a-molti. Entrambe le entità hanno proprietà di navigazione. **Course** è l'entità dipendente e ha la proprietà di chiave esterna **DepartmentID** definita.

![Tabelle Reparto e Corso con proprietà di navigazione](~/ef6/media/relationshipefdesigner.png)

The following code snippet shows the same model that was created with Code First.

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a>Configurazione o mapping delle relazioni

Nella parte restante di questa pagina viene illustrato come accedere e modificare i dati utilizzando le relazioni. Per informazioni sull'impostazione delle relazioni nel modello, vedere le pagine seguenti.

-   Per configurare le relazioni in Code First, vedere [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).
-   Per configurare le relazioni utilizzando Entity Framework Designer, vedere [Relazioni con Progettazione Entity Framework](~/ef6/modeling/designer/relationships.md).

## <a name="creating-and-modifying-relationships"></a>Creazione e modifica di relazioni

In *un'associazione*di chiave esterna , quando si modifica `EntityState.Unchanged` la `EntityState.Modified`relazione, lo stato di un oggetto dipendente con uno stato diventa . In una relazione indipendente, la modifica della relazione non aggiorna lo stato dell'oggetto dipendente.

Negli esempi seguenti viene illustrato come utilizzare le proprietà di chiave esterna e le proprietà di navigazione per associare gli oggetti correlati. Con le associazioni di chiavi esterne, è possibile utilizzare uno dei due metodi per modificare, creare o modificare le relazioni. Con associazioni indipendenti, non è possibile utilizzare la proprietà di chiave esterna.

- Assegnando un nuovo valore a una proprietà di chiave esterna, come nell'esempio seguente.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Il codice seguente rimuove una relazione impostando la chiave esterna su **null**. Si noti che la proprietà della chiave esterna deve essere nullable.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Se il riferimento si trova nello stato aggiunto (in questo esempio, l'oggetto corso), la proprietà di navigazione del riferimento non verrà sincronizzata con i valori chiave di un nuovo oggetto finché non viene chiamato SaveChanges. La sincronizzazione non si verifica in quanto il contesto dell'oggetto non contiene chiavi permanenti per gli oggetti aggiunti fino quando questi non vengono salvati. Se è necessario che nuovi oggetti siano completamente sincronizzati non appena si imposta la relazione, utilizzare uno dei metodi descritti di seguito.

- Assegnando un nuovo oggetto a una proprietà di navigazione. Il codice seguente crea una relazione `department`tra un corso e un oggetto . Se gli oggetti sono associati al `course` contesto, `department.Courses` l'oggetto viene aggiunto anche `course` alla raccolta e la proprietà della chiave esterna corrispondente sull'oggetto viene impostata sul valore della proprietà key del reparto.  
  ``` csharp
  course.Department = department;
  ```

- Per eliminare la relazione, impostare la proprietà di navigazione su `null`. Se si utilizza Entity Framework basato su .NET 4.0, l'estremità correlata deve essere caricata prima di impostarla su null. Ad esempio:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  A partire da Entity Framework 5.0, basato su .NET 4.5, è possibile impostare la relazione su null senza caricare l'entità finale correlata. È inoltre possibile impostare il valore corrente su null utilizzando il metodo seguente.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Eliminando o aggiungendo un oggetto in una raccolta di entità. Ad esempio, è possibile aggiungere `Course` un `department.Courses` oggetto di tipo alla raccolta. Questa operazione crea una **course** relazione tra `department`un corso particolare e un particolare . Se gli oggetti sono associati al contesto, il riferimento **course** al reparto e la `department`proprietà di chiave esterna nell'oggetto corso verranno impostati sull'oggetto appropriato.  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Utilizzando il `ChangeRelationshipState` metodo per modificare lo stato della relazione specificata tra due oggetti entità. Questo metodo viene utilizzato più comunemente quando si lavora con applicazioni a più livelli e *un'associazione indipendente* (non può essere utilizzato con un'associazione di chiave esterna). Inoltre, per utilizzare questo metodo `ObjectContext`è necessario scendere a , come illustrato nell'esempio seguente.  
Nell'esempio seguente esiste una relazione molti-a-molti tra istruttori e corsi. La `ChangeRelationshipState` chiamata al `EntityState.Added` metodo e `SchoolContext` il passaggio del parametro indica che è stata aggiunta una relazione tra i due oggetti:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Si noti che se si sta aggiornando (non solo aggiungendo) una relazione, è necessario eliminare la relazione precedente dopo aver aggiunto quella nuova:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Sincronizzazione delle modifiche tra le chiavi esterne e le proprietà di navigazione

Quando si modifica la relazione degli oggetti associati al contesto utilizzando uno dei metodi descritti in precedenza, Entity Framework deve mantenere sincronizzate chiavi esterne, riferimenti e raccolte. Entity Framework gestisce automaticamente questa sincronizzazione (nota anche come correzione delle relazioni) per le entità POCO con proxy. Per ulteriori informazioni, consultate [Utilizzo dei proxy.](~/ef6/fundamentals/proxies.md)

Se si utilizzano entità POCO senza proxy, è necessario assicurarsi che il **DetectChanges** metodo viene chiamato per sincronizzare gli oggetti correlati nel contesto. Si noti che le API seguenti attivano automaticamente una chiamata **DetectChanges.Note** that the following APIs automatically trigger a DetectChanges call.

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   Esecuzione di una query LINQ su un`DbSet`

## <a name="loading-related-objects"></a>Caricamento di oggetti correlati

In Entity Framework si usano comunemente le proprietà di navigazione per caricare le entità correlate all'entità restituita dall'associazione definita. Per ulteriori informazioni, vedere [Caricamento di oggetti correlati](~/ef6/querying/related-data.md).

> [!NOTE]
> In un'associazione di chiavi esterne, quando si carica un'entità finale correlata di un oggetto dipendente, l'oggetto correlato sarà caricato in base al valore della chiave esterna dell'oggetto dipendente attualmente in memoria:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

In un'associazione indipendente viene eseguita una query sull'entità finale correlata di un oggetto dipendente in base al valore della chiave esterna che è attualmente nel database. Tuttavia, se la relazione è stata modificata e la proprietà di riferimento nell'oggetto dipendente punta a un oggetto Principal diverso caricato nel contesto dell'oggetto, Entity Framework tenterà di creare una relazione come è definita nel client.

## <a name="managing-concurrency"></a>Gestione della concorrenza

Nelle associazioni di chiave esterna e indipendente, i controlli di concorrenza sono basati sulle chiavi di entità e altre proprietà di entità definite nel modello. Quando si usa la finestra di progettazione di EF per creare un modello, impostare l'attributo `ConcurrencyMode` su **fixed** per specificare che la proprietà deve essere controllata per la concorrenza. Quando si utilizza Code First per `ConcurrencyCheck` definire un modello, usare l'annotazione sulle proprietà che si desidera controllare per la concorrenza. Quando si lavora con Code `TimeStamp` First è anche possibile usare l'annotazione per specificare che la proprietà deve essere controllata per la concorrenza. È possibile avere una sola proprietà timestamp in una determinata classe. Code First esegue il mapping di questa proprietà a un campo non nullable nel database.

È consigliabile usare sempre l'associazione di chiave esterna quando si lavora con entità che partecipano al controllo e alla risoluzione della concorrenza.

Per ulteriori informazioni, vedere [Gestione dei conflitti](~/ef6/saving/concurrency.md)di concorrenza .

## <a name="working-with-overlapping-keys"></a>Utilizzo di tasti sovrapposti

Le chiavi sovrapposte sono chiavi composte in cui alcune proprietà nella chiave fanno parte anche di un'altra chiave nell'entità. Non è possibile disporre di una chiave sovrapposta in un'associazione indipendente. Per modificare un'associazione di chiavi esterne che include chiavi sovrapposte, si consiglia di modificare i valori della chiave esterna anziché utilizzare i riferimenti a un oggetto.
