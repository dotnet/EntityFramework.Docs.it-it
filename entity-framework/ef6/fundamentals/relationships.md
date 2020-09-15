---
title: Relazioni, proprietà di navigazione e chiavi esterne-EF6
description: Relazioni, proprietà di navigazione e chiavi esterne in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 65caacfbd860b5b896695c208b55b9c66d11255d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072252"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Relazioni, proprietà di navigazione e chiavi esterne

Questo articolo fornisce una panoramica del modo in cui Entity Framework gestisce le relazioni tra le entità. Vengono inoltre fornite alcune indicazioni su come eseguire il mapping e modificare le relazioni.

## <a name="relationships-in-ef"></a>Relazioni in EF

Nei database relazionali, le relazioni (dette anche associazioni) tra le tabelle vengono definite tramite chiavi esterne. Per chiave esterna si intende una colonna o combinazione di colonne utilizzata per stabilire e applicare un collegamento tra i dati di due tabelle. Esistono in genere tre tipi di relazioni: uno-a-uno, uno-a-molti e molti-a-molti. In una relazione uno-a-molti, la chiave esterna viene definita nella tabella che rappresenta le numerose entità finali della relazione. La relazione molti-a-molti implica la definizione di una terza tabella (denominata tabella di giunzione o join), la cui chiave primaria è costituita dalle chiavi esterne di entrambe le tabelle correlate. In una relazione uno-a-uno, la chiave primaria funge anche da chiave esterna e non esiste una colonna di chiave esterna separata per entrambe le tabelle.

Nella figura seguente sono illustrate due tabelle che fanno parte di una relazione uno-a-molti. La tabella **Course** è la tabella dipendente perché contiene la colonna **DepartmentID** che lo collega alla tabella **Department** .

![Tabelle del reparto e del corso](~/ef6/media/database2.png)

In Entity Framework, un'entità può essere correlata ad altre entità tramite un'associazione o una relazione. Ogni relazione contiene due entità finali che descrivono il tipo di entità e la molteplicità del tipo (uno, zero-o-uno o molti) per le due entità della relazione. La relazione può essere regolata da un vincolo referenziale che descrive quale entità finale nella relazione riveste il ruolo principale e quale quello di dipendente.

Le proprietà di navigazione consentono di spostarsi in un'associazione tra due tipi di entità. Ogni oggetto può disporre di una proprietà di navigazione per ogni relazione di cui fa parte. Le proprietà di navigazione consentono di esplorare e gestire le relazioni in entrambe le direzioni, restituendo un oggetto di riferimento (se la molteplicità è uno o zero-o-uno) o una raccolta (se la molteplicità è molti). È anche possibile scegliere di disporre di una navigazione unidirezionale, nel qual caso si definisce la proprietà di navigazione solo su uno dei tipi che partecipano alla relazione e non su entrambi.

È consigliabile includere nel modello le proprietà che vengono mappate alle chiavi esterne del database. Con le proprietà di chiave esterna incluse, è possibile creare o modificare una relazione cambiando il valore della chiave esterna in un oggetto dipendente. Questo tipo di associazione viene definito associazione di chiavi esterne. L'utilizzo di chiavi esterne è ancora più essenziale quando si utilizzano le entità disconnesse. Si noti che quando si lavora con 1 a 1 o 1 a 0. 1 relazioni, non esiste una colonna di chiave esterna separata, la proprietà della chiave primaria funge da chiave esterna ed è sempre inclusa nel modello.

Quando le colonne di chiavi esterne non sono incluse nel modello, le informazioni di associazione vengono gestite come oggetto indipendente. Le relazioni vengono rilevate tramite riferimenti a oggetti anziché proprietà di chiave esterna. Questo tipo di associazione viene definito *associazione indipendente*. Il modo più comune per modificare un' *associazione indipendente* consiste nel modificare le proprietà di navigazione generate per ogni entità che partecipa all'associazione.

È possibile scegliere di utilizzare uno o entrambi i tipi di associazioni nel modello. Tuttavia, se si dispone di una relazione molti-a-molti pura connessa da una tabella di join che contiene solo chiavi esterne, EF utilizzerà un'associazione indipendente per gestire una relazione molti-a-molti.   

Nell'immagine seguente viene illustrato un modello concettuale creato con l'Entity Framework Designer. Il modello contiene due entità che fanno parte di una relazione uno-a-molti. Entrambe le entità hanno proprietà di navigazione. **Course** è l'entità dipendente ed è stata definita la proprietà di chiave esterna **DepartmentID** .

![Tabelle Department e Course con proprietà di navigazione](~/ef6/media/relationshipefdesigner.png)

Il frammento di codice seguente illustra lo stesso modello creato con Code First.

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

## <a name="configuring-or-mapping-relationships"></a>Configurazione o mapping di relazioni

Il resto di questa pagina illustra come accedere e modificare i dati usando le relazioni. Per informazioni sull'impostazione delle relazioni nel modello, vedere le pagine seguenti.

-   Per configurare le relazioni in Code First, vedere [annotazioni dei dati](xref:ef6/modeling/code-first/data-annotations) e [API Fluent-relazioni](xref:ef6/modeling/code-first/fluent/relationships).
-   Per configurare le relazioni usando il Entity Framework Designer, vedere [relazioni con la finestra di progettazione EF](xref:ef6/modeling/designer/relationships).

## <a name="creating-and-modifying-relationships"></a>Creazione e modifica di relazioni

In un' *associazione di chiavi esterne*, quando si modifica la relazione, lo stato di un oggetto dipendente con uno `EntityState.Unchanged` stato viene modificato in `EntityState.Modified` . In una relazione indipendente, la modifica della relazione non aggiorna lo stato dell'oggetto dipendente.

Negli esempi seguenti viene illustrato come utilizzare le proprietà di chiave esterna e le proprietà di navigazione per associare gli oggetti correlati. Con le associazioni di chiavi esterne è possibile utilizzare uno dei metodi per modificare, creare o modificare le relazioni. Con associazioni indipendenti, non è possibile utilizzare la proprietà di chiave esterna.

- Assegnando un nuovo valore a una proprietà di chiave esterna, come nell'esempio seguente.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Il codice seguente rimuove una relazione impostando la chiave esterna su **null**. Si noti che la proprietà della chiave esterna deve ammettere i valori null.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Se il riferimento è nello stato Added (in questo esempio, l'oggetto Course), la proprietà di navigazione Reference non verrà sincronizzata con i valori di chiave di un nuovo oggetto finché non viene chiamato SaveChanges. La sincronizzazione non si verifica in quanto il contesto dell'oggetto non contiene chiavi permanenti per gli oggetti aggiunti fino quando questi non vengono salvati. Se è necessario che i nuovi oggetti siano completamente sincronizzati non appena si imposta la relazione, usare uno dei metodi seguenti. *

- Assegnando un nuovo oggetto a una proprietà di navigazione. Il codice seguente crea una relazione tra un corso e un oggetto `department` . Se gli oggetti sono collegati al contesto, `course` viene aggiunto anche alla `department.Courses` raccolta e la proprietà di chiave esterna corrispondente nell' `course` oggetto viene impostata sul valore della proprietà chiave del reparto.  
  ``` csharp
  course.Department = department;
  ```

- Per eliminare la relazione, impostare la proprietà di navigazione su `null` . Se si lavora con Entity Framework basata su .NET 4,0, è necessario caricare l'entità finale correlata prima di impostarla su null. Esempio:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  A partire da Entity Framework 5,0, che è basato su .NET 4,5, è possibile impostare la relazione su null senza caricare l'entità finale correlata. È inoltre possibile impostare il valore corrente su null utilizzando il metodo seguente.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Eliminando o aggiungendo un oggetto in una raccolta di entità. Ad esempio, è possibile aggiungere un oggetto di tipo `Course` alla `department.Courses` raccolta. Questa operazione crea una relazione tra un determinato **corso** e un particolare `department` . Se gli oggetti sono collegati al contesto, il riferimento al reparto e la proprietà di chiave esterna nell'oggetto **Course** verranno impostati sull'oggetto appropriato `department` .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Utilizzando il `ChangeRelationshipState` metodo per modificare lo stato della relazione specificata tra due oggetti entità. Questo metodo viene in genere utilizzato quando si utilizzano applicazioni a più livelli e un' *associazione indipendente* (non può essere utilizzato con un'associazione di chiavi esterne). Inoltre, per usare questo metodo, è necessario eseguire l'elenco a discesa `ObjectContext` , come illustrato nell'esempio riportato di seguito.  
Nell'esempio seguente esiste una relazione molti-a-molti tra docenti e corsi. Chiamando il `ChangeRelationshipState` metodo e passando il `EntityState.Added` parametro, è possibile `SchoolContext` verificare che sia stata aggiunta una relazione tra i due oggetti:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Si noti che se si sta aggiornando (non solo aggiungendo) una relazione, è necessario eliminare la relazione precedente dopo aver aggiunto quello nuovo:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Sincronizzazione delle modifiche tra le chiavi esterne e le proprietà di navigazione

Quando si modifica la relazione degli oggetti collegati al contesto utilizzando uno dei metodi descritti in precedenza, Entity Framework necessario che le chiavi esterne, i riferimenti e le raccolte siano sincronizzati. Entity Framework gestisce automaticamente questa sincronizzazione, nota anche come correzione della relazione, per le entità POCO con proxy. Per ulteriori informazioni, vedere [utilizzo di proxy](xref:ef6/fundamentals/proxies).

Se si utilizzano entità POCO senza proxy, è necessario assicurarsi che venga chiamato il metodo **DetectChanges** per sincronizzare gli oggetti correlati nel contesto. Si noti che le API seguenti attivano automaticamente una chiamata **DetectChanges** .

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
-   Esecuzione di una query LINQ su un `DbSet`

## <a name="loading-related-objects"></a>Caricamento di oggetti correlati

In Entity Framework si utilizzano comunemente le proprietà di navigazione per caricare entità correlate all'entità restituita dall'associazione definita. Per ulteriori informazioni, vedere [caricamento di oggetti correlati](xref:ef6/querying/related-data).

> [!NOTE]
> In un'associazione di chiavi esterne, quando si carica un'entità finale correlata di un oggetto dipendente, l'oggetto correlato sarà caricato in base al valore della chiave esterna dell'oggetto dipendente attualmente in memoria:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

In un'associazione indipendente viene eseguita una query sull'entità finale correlata di un oggetto dipendente in base al valore della chiave esterna che è attualmente nel database. Tuttavia, se la relazione è stata modificata e la proprietà Reference nell'oggetto dipendente punta a un oggetto Principal diverso caricato nel contesto dell'oggetto, Entity Framework tenterà di creare una relazione come definita nel client.

## <a name="managing-concurrency"></a>Gestione della concorrenza

Nelle associazioni di chiave esterna e indipendente, i controlli della concorrenza sono basati sulle chiavi di entità e su altre proprietà di entità definite nel modello. Quando si usa la finestra di progettazione di Entity Framework per creare un modello, impostare l' `ConcurrencyMode` attributo su **fixed** per specificare che la proprietà deve essere controllata per la concorrenza. Quando si usa Code First per definire un modello, usare l' `ConcurrencyCheck` annotazione sulle proprietà di cui si vuole verificare la concorrenza. Quando si lavora con Code First è anche possibile usare l' `TimeStamp` annotazione per specificare che la proprietà deve essere controllata per la concorrenza. È possibile avere una sola proprietà timestamp in una determinata classe. Code First esegue il mapping di questa proprietà a un campo che non ammette i valori null nel database.

Si consiglia di utilizzare sempre l'associazione di chiavi esterne quando si utilizzano entità che partecipano al controllo della concorrenza e alla risoluzione.

Per ulteriori informazioni, vedere [gestione dei conflitti di concorrenza](xref:ef6/saving/concurrency).

## <a name="working-with-overlapping-keys"></a>Utilizzo di chiavi sovrapposte

Le chiavi sovrapposte sono chiavi composte in cui alcune proprietà nella chiave fanno parte anche di un'altra chiave nell'entità. Non è possibile disporre di una chiave sovrapposta in un'associazione indipendente. Per modificare un'associazione di chiavi esterne che include chiavi sovrapposte, si consiglia di modificare i valori della chiave esterna anziché utilizzare i riferimenti a un oggetto.
