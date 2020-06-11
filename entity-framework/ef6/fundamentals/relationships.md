---
title: Relazioni, proprietà di navigazione e chiavi esterne-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 5807f7aeeb68328821cf45ac4f8c28efa17ed399
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664195"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="dffaa-102">Relazioni, proprietà di navigazione e chiavi esterne</span><span class="sxs-lookup"><span data-stu-id="dffaa-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="dffaa-103">Questo articolo fornisce una panoramica del modo in cui Entity Framework gestisce le relazioni tra le entità.</span><span class="sxs-lookup"><span data-stu-id="dffaa-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="dffaa-104">Vengono inoltre fornite alcune indicazioni su come eseguire il mapping e modificare le relazioni.</span><span class="sxs-lookup"><span data-stu-id="dffaa-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="dffaa-105">Relazioni in EF</span><span class="sxs-lookup"><span data-stu-id="dffaa-105">Relationships in EF</span></span>

<span data-ttu-id="dffaa-106">Nei database relazionali, le relazioni (dette anche associazioni) tra le tabelle vengono definite tramite chiavi esterne.</span><span class="sxs-lookup"><span data-stu-id="dffaa-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="dffaa-107">Per chiave esterna si intende una colonna o combinazione di colonne utilizzata per stabilire e applicare un collegamento tra i dati di due tabelle.</span><span class="sxs-lookup"><span data-stu-id="dffaa-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="dffaa-108">Esistono in genere tre tipi di relazioni: uno-a-uno, uno-a-molti e molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="dffaa-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="dffaa-109">In una relazione uno-a-molti, la chiave esterna viene definita nella tabella che rappresenta le numerose entità finali della relazione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="dffaa-110">La relazione molti-a-molti implica la definizione di una terza tabella (denominata tabella di giunzione o join), la cui chiave primaria è costituita dalle chiavi esterne di entrambe le tabelle correlate.</span><span class="sxs-lookup"><span data-stu-id="dffaa-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="dffaa-111">In una relazione uno-a-uno, la chiave primaria funge anche da chiave esterna e non esiste una colonna di chiave esterna separata per entrambe le tabelle.</span><span class="sxs-lookup"><span data-stu-id="dffaa-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="dffaa-112">Nella figura seguente sono illustrate due tabelle che fanno parte di una relazione uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="dffaa-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="dffaa-113">La tabella **Course** è la tabella dipendente perché contiene la colonna **DepartmentID** che lo collega alla tabella **Department** .</span><span class="sxs-lookup"><span data-stu-id="dffaa-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Tabelle del reparto e del corso](~/ef6/media/database2.png)

<span data-ttu-id="dffaa-115">In Entity Framework, un'entità può essere correlata ad altre entità tramite un'associazione o una relazione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="dffaa-116">Ogni relazione contiene due entità finali che descrivono il tipo di entità e la molteplicità del tipo (uno, zero-o-uno o molti) per le due entità della relazione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="dffaa-117">La relazione può essere regolata da un vincolo referenziale che descrive quale entità finale nella relazione riveste il ruolo principale e quale quello di dipendente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="dffaa-118">Le proprietà di navigazione consentono di spostarsi in un'associazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="dffaa-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="dffaa-119">Ogni oggetto può disporre di una proprietà di navigazione per ogni relazione di cui fa parte.</span><span class="sxs-lookup"><span data-stu-id="dffaa-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="dffaa-120">Le proprietà di navigazione consentono di esplorare e gestire le relazioni in entrambe le direzioni, restituendo un oggetto di riferimento (se la molteplicità è uno o zero-o-uno) o una raccolta (se la molteplicità è molti).</span><span class="sxs-lookup"><span data-stu-id="dffaa-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="dffaa-121">È anche possibile scegliere di disporre di una navigazione unidirezionale, nel qual caso si definisce la proprietà di navigazione solo su uno dei tipi che partecipano alla relazione e non su entrambi.</span><span class="sxs-lookup"><span data-stu-id="dffaa-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="dffaa-122">È consigliabile includere nel modello le proprietà che vengono mappate alle chiavi esterne del database.</span><span class="sxs-lookup"><span data-stu-id="dffaa-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="dffaa-123">Con le proprietà di chiave esterna incluse, è possibile creare o modificare una relazione cambiando il valore della chiave esterna in un oggetto dipendente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="dffaa-124">Questo tipo di associazione viene definito associazione di chiavi esterne.</span><span class="sxs-lookup"><span data-stu-id="dffaa-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="dffaa-125">L'utilizzo di chiavi esterne è ancora più essenziale quando si utilizzano le entità disconnesse.</span><span class="sxs-lookup"><span data-stu-id="dffaa-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="dffaa-126">Si noti che quando si lavora con 1 a 1 o 1 a 0. 1 relazioni, non esiste una colonna di chiave esterna separata, la proprietà della chiave primaria funge da chiave esterna ed è sempre inclusa nel modello.</span><span class="sxs-lookup"><span data-stu-id="dffaa-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="dffaa-127">Quando le colonne di chiavi esterne non sono incluse nel modello, le informazioni di associazione vengono gestite come oggetto indipendente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="dffaa-128">Le relazioni vengono rilevate tramite riferimenti a oggetti anziché proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="dffaa-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="dffaa-129">Questo tipo di associazione viene definito *associazione indipendente*.</span><span class="sxs-lookup"><span data-stu-id="dffaa-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="dffaa-130">Il modo più comune per modificare un' *associazione indipendente* consiste nel modificare le proprietà di navigazione generate per ogni entità che partecipa all'associazione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="dffaa-131">È possibile scegliere di utilizzare uno o entrambi i tipi di associazioni nel modello.</span><span class="sxs-lookup"><span data-stu-id="dffaa-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="dffaa-132">Tuttavia, se si dispone di una relazione molti-a-molti pura connessa da una tabella di join che contiene solo chiavi esterne, EF utilizzerà un'associazione indipendente per gestire una relazione molti-a-molti.</span><span class="sxs-lookup"><span data-stu-id="dffaa-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="dffaa-133">Nell'immagine seguente viene illustrato un modello concettuale creato con l'Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="dffaa-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="dffaa-134">Il modello contiene due entità che fanno parte di una relazione uno-a-molti.</span><span class="sxs-lookup"><span data-stu-id="dffaa-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="dffaa-135">Entrambe le entità hanno proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-135">Both entities have navigation properties.</span></span> <span data-ttu-id="dffaa-136">**Course** è l'entità dipendente ed è stata definita la proprietà di chiave esterna **DepartmentID** .</span><span class="sxs-lookup"><span data-stu-id="dffaa-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![Tabelle Department e Course con proprietà di navigazione](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="dffaa-138">Il frammento di codice seguente illustra lo stesso modello creato con Code First.</span><span class="sxs-lookup"><span data-stu-id="dffaa-138">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="dffaa-139">Configurazione o mapping di relazioni</span><span class="sxs-lookup"><span data-stu-id="dffaa-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="dffaa-140">Il resto di questa pagina illustra come accedere e modificare i dati usando le relazioni.</span><span class="sxs-lookup"><span data-stu-id="dffaa-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="dffaa-141">Per informazioni sull'impostazione delle relazioni nel modello, vedere le pagine seguenti.</span><span class="sxs-lookup"><span data-stu-id="dffaa-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="dffaa-142">Per configurare le relazioni in Code First, vedere [annotazioni dei dati](~/ef6/modeling/code-first/data-annotations.md) e [API Fluent-relazioni](~/ef6/modeling/code-first/fluent/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="dffaa-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="dffaa-143">Per configurare le relazioni usando il Entity Framework Designer, vedere [relazioni con la finestra di progettazione EF](~/ef6/modeling/designer/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="dffaa-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="dffaa-144">Creazione e modifica di relazioni</span><span class="sxs-lookup"><span data-stu-id="dffaa-144">Creating and modifying relationships</span></span>

<span data-ttu-id="dffaa-145">In un' *associazione di chiavi esterne*, quando si modifica la relazione, lo stato di un oggetto dipendente con uno `EntityState.Unchanged` stato viene modificato in `EntityState.Modified` .</span><span class="sxs-lookup"><span data-stu-id="dffaa-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="dffaa-146">In una relazione indipendente, la modifica della relazione non aggiorna lo stato dell'oggetto dipendente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="dffaa-147">Negli esempi seguenti viene illustrato come utilizzare le proprietà di chiave esterna e le proprietà di navigazione per associare gli oggetti correlati.</span><span class="sxs-lookup"><span data-stu-id="dffaa-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="dffaa-148">Con le associazioni di chiavi esterne è possibile utilizzare uno dei metodi per modificare, creare o modificare le relazioni.</span><span class="sxs-lookup"><span data-stu-id="dffaa-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="dffaa-149">Con associazioni indipendenti, non è possibile utilizzare la proprietà di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="dffaa-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="dffaa-150">Assegnando un nuovo valore a una proprietà di chiave esterna, come nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="dffaa-151">Il codice seguente rimuove una relazione impostando la chiave esterna su **null**.</span><span class="sxs-lookup"><span data-stu-id="dffaa-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="dffaa-152">Si noti che la proprietà della chiave esterna deve ammettere i valori null.</span><span class="sxs-lookup"><span data-stu-id="dffaa-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="dffaa-153">Se il riferimento è nello stato Added (in questo esempio, l'oggetto Course), la proprietà di navigazione Reference non verrà sincronizzata con i valori di chiave di un nuovo oggetto finché non viene chiamato SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="dffaa-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="dffaa-154">La sincronizzazione non si verifica in quanto il contesto dell'oggetto non contiene chiavi permanenti per gli oggetti aggiunti fino quando questi non vengono salvati.</span><span class="sxs-lookup"><span data-stu-id="dffaa-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="dffaa-155">Se è necessario che i nuovi oggetti siano completamente sincronizzati non appena si imposta la relazione, usare uno dei metodi seguenti. \*</span><span class="sxs-lookup"><span data-stu-id="dffaa-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="dffaa-156">Assegnando un nuovo oggetto a una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="dffaa-157">Il codice seguente crea una relazione tra un corso e un oggetto `department` .</span><span class="sxs-lookup"><span data-stu-id="dffaa-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="dffaa-158">Se gli oggetti sono collegati al contesto, `course` viene aggiunto anche alla `department.Courses` raccolta e la proprietà di chiave esterna corrispondente nell' `course` oggetto viene impostata sul valore della proprietà chiave del reparto.</span><span class="sxs-lookup"><span data-stu-id="dffaa-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="dffaa-159">Per eliminare la relazione, impostare la proprietà di navigazione su `null` .</span><span class="sxs-lookup"><span data-stu-id="dffaa-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="dffaa-160">Se si lavora con Entity Framework basata su .NET 4,0, è necessario caricare l'entità finale correlata prima di impostarla su null.</span><span class="sxs-lookup"><span data-stu-id="dffaa-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="dffaa-161">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="dffaa-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="dffaa-162">A partire da Entity Framework 5,0, che è basato su .NET 4,5, è possibile impostare la relazione su null senza caricare l'entità finale correlata.</span><span class="sxs-lookup"><span data-stu-id="dffaa-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="dffaa-163">È inoltre possibile impostare il valore corrente su null utilizzando il metodo seguente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="dffaa-164">Eliminando o aggiungendo un oggetto in una raccolta di entità.</span><span class="sxs-lookup"><span data-stu-id="dffaa-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="dffaa-165">Ad esempio, è possibile aggiungere un oggetto di tipo `Course` alla `department.Courses` raccolta.</span><span class="sxs-lookup"><span data-stu-id="dffaa-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="dffaa-166">Questa operazione crea una relazione tra un determinato **corso** e un particolare `department` .</span><span class="sxs-lookup"><span data-stu-id="dffaa-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="dffaa-167">Se gli oggetti sono collegati al contesto, il riferimento al reparto e la proprietà di chiave esterna nell'oggetto **Course** verranno impostati sull'oggetto appropriato `department` .</span><span class="sxs-lookup"><span data-stu-id="dffaa-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="dffaa-168">Utilizzando il `ChangeRelationshipState` metodo per modificare lo stato della relazione specificata tra due oggetti entità.</span><span class="sxs-lookup"><span data-stu-id="dffaa-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="dffaa-169">Questo metodo viene in genere utilizzato quando si utilizzano applicazioni a più livelli e un' *associazione indipendente* (non può essere utilizzato con un'associazione di chiavi esterne).</span><span class="sxs-lookup"><span data-stu-id="dffaa-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="dffaa-170">Inoltre, per usare questo metodo, è necessario eseguire l'elenco a discesa `ObjectContext` , come illustrato nell'esempio riportato di seguito.</span><span class="sxs-lookup"><span data-stu-id="dffaa-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="dffaa-171">Nell'esempio seguente esiste una relazione molti-a-molti tra docenti e corsi.</span><span class="sxs-lookup"><span data-stu-id="dffaa-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="dffaa-172">Chiamando il `ChangeRelationshipState` metodo e passando il `EntityState.Added` parametro, è possibile `SchoolContext` verificare che sia stata aggiunta una relazione tra i due oggetti:</span><span class="sxs-lookup"><span data-stu-id="dffaa-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="dffaa-173">Si noti che se si sta aggiornando (non solo aggiungendo) una relazione, è necessario eliminare la relazione precedente dopo aver aggiunto quello nuovo:</span><span class="sxs-lookup"><span data-stu-id="dffaa-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="dffaa-174">Sincronizzazione delle modifiche tra le chiavi esterne e le proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="dffaa-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="dffaa-175">Quando si modifica la relazione degli oggetti collegati al contesto utilizzando uno dei metodi descritti in precedenza, Entity Framework necessario che le chiavi esterne, i riferimenti e le raccolte siano sincronizzati. Entity Framework gestisce automaticamente questa sincronizzazione, nota anche come correzione della relazione, per le entità POCO con proxy.</span><span class="sxs-lookup"><span data-stu-id="dffaa-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="dffaa-176">Per ulteriori informazioni, vedere [utilizzo di proxy](~/ef6/fundamentals/proxies.md).</span><span class="sxs-lookup"><span data-stu-id="dffaa-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="dffaa-177">Se si utilizzano entità POCO senza proxy, è necessario assicurarsi che venga chiamato il metodo **DetectChanges** per sincronizzare gli oggetti correlati nel contesto.</span><span class="sxs-lookup"><span data-stu-id="dffaa-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="dffaa-178">Si noti che le API seguenti attivano automaticamente una chiamata **DetectChanges** .</span><span class="sxs-lookup"><span data-stu-id="dffaa-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="dffaa-179">Esecuzione di una query LINQ su un`DbSet`</span><span class="sxs-lookup"><span data-stu-id="dffaa-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="dffaa-180">Caricamento di oggetti correlati</span><span class="sxs-lookup"><span data-stu-id="dffaa-180">Loading related objects</span></span>

<span data-ttu-id="dffaa-181">In Entity Framework si utilizzano comunemente le proprietà di navigazione per caricare entità correlate all'entità restituita dall'associazione definita.</span><span class="sxs-lookup"><span data-stu-id="dffaa-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="dffaa-182">Per ulteriori informazioni, vedere [caricamento di oggetti correlati](~/ef6/querying/related-data.md).</span><span class="sxs-lookup"><span data-stu-id="dffaa-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="dffaa-183">In un'associazione di chiavi esterne, quando si carica un'entità finale correlata di un oggetto dipendente, l'oggetto correlato sarà caricato in base al valore della chiave esterna dell'oggetto dipendente attualmente in memoria:</span><span class="sxs-lookup"><span data-stu-id="dffaa-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="dffaa-184">In un'associazione indipendente viene eseguita una query sull'entità finale correlata di un oggetto dipendente in base al valore della chiave esterna che è attualmente nel database.</span><span class="sxs-lookup"><span data-stu-id="dffaa-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="dffaa-185">Tuttavia, se la relazione è stata modificata e la proprietà Reference nell'oggetto dipendente punta a un oggetto Principal diverso caricato nel contesto dell'oggetto, Entity Framework tenterà di creare una relazione come definita nel client.</span><span class="sxs-lookup"><span data-stu-id="dffaa-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="dffaa-186">Gestione della concorrenza</span><span class="sxs-lookup"><span data-stu-id="dffaa-186">Managing concurrency</span></span>

<span data-ttu-id="dffaa-187">Nelle associazioni di chiave esterna e indipendente, i controlli della concorrenza sono basati sulle chiavi di entità e su altre proprietà di entità definite nel modello.</span><span class="sxs-lookup"><span data-stu-id="dffaa-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="dffaa-188">Quando si usa la finestra di progettazione di Entity Framework per creare un modello, impostare l' `ConcurrencyMode` attributo su **fixed** per specificare che la proprietà deve essere controllata per la concorrenza.</span><span class="sxs-lookup"><span data-stu-id="dffaa-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="dffaa-189">Quando si usa Code First per definire un modello, usare l' `ConcurrencyCheck` annotazione sulle proprietà di cui si vuole verificare la concorrenza.</span><span class="sxs-lookup"><span data-stu-id="dffaa-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="dffaa-190">Quando si lavora con Code First è anche possibile usare l' `TimeStamp` annotazione per specificare che la proprietà deve essere controllata per la concorrenza.</span><span class="sxs-lookup"><span data-stu-id="dffaa-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="dffaa-191">È possibile avere una sola proprietà timestamp in una determinata classe.</span><span class="sxs-lookup"><span data-stu-id="dffaa-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="dffaa-192">Code First esegue il mapping di questa proprietà a un campo che non ammette i valori null nel database.</span><span class="sxs-lookup"><span data-stu-id="dffaa-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="dffaa-193">Si consiglia di utilizzare sempre l'associazione di chiavi esterne quando si utilizzano entità che partecipano al controllo della concorrenza e alla risoluzione.</span><span class="sxs-lookup"><span data-stu-id="dffaa-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="dffaa-194">Per ulteriori informazioni, vedere [gestione dei conflitti di concorrenza](~/ef6/saving/concurrency.md).</span><span class="sxs-lookup"><span data-stu-id="dffaa-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="dffaa-195">Utilizzo di chiavi sovrapposte</span><span class="sxs-lookup"><span data-stu-id="dffaa-195">Working with overlapping Keys</span></span>

<span data-ttu-id="dffaa-196">Le chiavi sovrapposte sono chiavi composte in cui alcune proprietà nella chiave fanno parte anche di un'altra chiave nell'entità.</span><span class="sxs-lookup"><span data-stu-id="dffaa-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="dffaa-197">Non è possibile disporre di una chiave sovrapposta in un'associazione indipendente.</span><span class="sxs-lookup"><span data-stu-id="dffaa-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="dffaa-198">Per modificare un'associazione di chiavi esterne che include chiavi sovrapposte, si consiglia di modificare i valori della chiave esterna anziché utilizzare i riferimenti a un oggetto.</span><span class="sxs-lookup"><span data-stu-id="dffaa-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
