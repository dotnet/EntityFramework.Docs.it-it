---
title: Uso dei valori delle proprietà-EF6
description: Utilizzo dei valori delle proprietà in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: 30c8e7dbd59f0eb3ec15c0f57f022afd90fd80f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073756"
---
# <a name="working-with-property-values"></a>Utilizzo dei valori delle proprietà
Per la maggior parte Entity Framework si occuperà di tenere traccia dello stato, dei valori originali e dei valori correnti delle proprietà delle istanze dell'entità. Tuttavia, potrebbero verificarsi alcuni casi, ad esempio gli scenari disconnessi, in cui si desidera visualizzare o modificare le informazioni di EF sulle proprietà. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

Entity Framework tiene traccia di due valori per ogni proprietà di un'entità rilevata. Il valore corrente è, come indica il nome, il valore corrente della proprietà nell'entità. Il valore originale è il valore che la proprietà aveva quando è stata eseguita una query sul database o è stato associato al contesto.  

Sono disponibili due meccanismi generali per l'utilizzo dei valori delle proprietà:  

- Il valore di una singola proprietà può essere ottenuto in modo fortemente tipizzato usando il metodo Property.  
- I valori per tutte le proprietà di un'entità possono essere letti in un oggetto elementi dbpropertyvalues. Elementi dbpropertyvalues funge quindi da oggetto simile a un dizionario per consentire la lettura e l'impostazione dei valori delle proprietà. I valori in un oggetto elementi dbpropertyvalues possono essere impostati dai valori di un altro oggetto elementi dbpropertyvalues o dai valori in un altro oggetto, ad esempio un'altra copia dell'entità o un oggetto di trasferimento dati semplice (DTO).  

Le sezioni seguenti illustrano esempi di utilizzo di entrambi i meccanismi descritti in precedenza.  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>Recupero e impostazione del valore corrente o originale di una singola proprietà  

Nell'esempio seguente viene illustrato come è possibile leggere il valore corrente di una proprietà e quindi impostarla su un nuovo valore:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

Usare la proprietà OriginalValue invece della proprietà CurrentValue per leggere o impostare il valore originale.  

Si noti che il valore restituito viene digitato come "Object" quando viene utilizzata una stringa per specificare il nome della proprietà. D'altra parte, il valore restituito è fortemente tipizzato se viene utilizzata un'espressione lambda.  

Impostando il valore della proprietà come questo, la proprietà verrà contrassegnata come modificata solo se il nuovo valore è diverso dal valore precedente.  

Quando un valore di proprietà viene impostato in questo modo, la modifica viene rilevata automaticamente anche se AutoDetectChanges è disattivato.  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>Recupero e impostazione del valore corrente di una proprietà non mappata  

È anche possibile leggere il valore corrente di una proprietà di cui non è stato eseguito il mapping al database. Un esempio di proprietà di cui non è stato eseguito il mapping può essere una proprietà RssLink nel Blog. Questo valore può essere calcolato in base a BlogId e pertanto non deve essere archiviato nel database. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

Il valore corrente può essere impostato anche se la proprietà espone un setter.  

La lettura dei valori delle proprietà di cui non è stato eseguito il mapping risulta utile quando si esegue Entity Framework la convalida delle proprietà senza mapping. Per lo stesso motivo è possibile leggere e impostare i valori correnti per le proprietà delle entità che non sono attualmente rilevate dal contesto. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

Si noti che i valori originali non sono disponibili per le proprietà di cui non è stato eseguito il mapping o per le proprietà di entità che non vengono rilevate dal contesto.  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>Verifica per determinare se una proprietà è contrassegnata come modificata  

Nell'esempio seguente viene illustrato come verificare se una singola proprietà è contrassegnata come modificata:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

I valori delle proprietà modificate vengono inviati come aggiornamenti al database quando viene chiamato SaveChanges.  

##  <a name="marking-a-property-as-modified"></a>Contrassegno di una proprietà come modificata  

Nell'esempio seguente viene illustrato come forzare la contrassegnatura di una singola proprietà come modificata:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

Contrassegnando una proprietà come modificata, viene inviato un aggiornamento al database per la proprietà quando SaveChanges viene chiamato anche se il valore corrente della proprietà è uguale al valore originale.  

Non è attualmente possibile reimpostare una singola proprietà in modo che non venga modificata dopo che è stata contrassegnata come modificata. Si tratta di un elemento che si prevede di supportare in una versione futura.  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>Lettura dei valori correnti, originali e del database per tutte le proprietà di un'entità  

Nell'esempio seguente viene illustrato come leggere i valori correnti, i valori originali e i valori effettivamente presenti nel database per tutte le proprietà di cui è stato eseguito il mapping di un'entità.  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

I valori correnti sono i valori attualmente contenuti nelle proprietà dell'entità. I valori originali sono i valori letti dal database durante la query dell'entità. I valori del database sono i valori attualmente archiviati nel database. Il recupero dei valori del database è utile quando è possibile che i valori nel database siano stati modificati dopo l'esecuzione di query sull'entità, ad esempio quando è stata eseguita una modifica simultanea del database da un altro utente.  

## <a name="setting-current-or-original-values-from-another-object"></a>Impostazione dei valori correnti o originali da un altro oggetto  

I valori correnti o originali di un'entità rilevata possono essere aggiornati copiando i valori da un altro oggetto. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

L'esecuzione del codice precedente comporterà la stampa:  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

Questa tecnica viene talvolta utilizzata quando si aggiorna un'entità con valori ottenuti da una chiamata al servizio o da un client in un'applicazione a più livelli. Si noti che l'oggetto utilizzato non deve essere dello stesso tipo dell'entità purché disponga di proprietà i cui nomi corrispondono a quelli dell'entità. Nell'esempio precedente viene usata un'istanza di BlogDTO per aggiornare i valori originali.  

Si noti che solo le proprietà impostate su valori diversi quando vengono copiati dall'altro oggetto verranno contrassegnate come modificate.  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>Impostazione dei valori correnti o originali da un dizionario  

I valori correnti o originali di un'entità rilevata possono essere aggiornati copiando i valori da un dizionario o da un'altra struttura di dati. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

Usare la proprietà OriginalValues invece della proprietà CurrentValues per impostare i valori originali.  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>Impostazione dei valori correnti o originali da un dizionario usando la proprietà  

Un'alternativa all'uso di CurrentValues o OriginalValues, come illustrato in precedenza, consiste nell'usare il metodo Property per impostare il valore di ogni proprietà. Questa operazione può essere preferibile quando è necessario impostare i valori delle proprietà complesse. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

Nell'esempio precedente viene eseguito l'accesso alle proprietà complesse utilizzando nomi punteggiati. Per altre modalità di accesso alle proprietà complesse, vedere le due sezioni più avanti in questo argomento, in particolare sulle proprietà complesse.  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Creazione di un oggetto clonato contenente i valori correnti, originali o di database  

Per creare un clone dell'entità, è possibile usare l'oggetto elementi dbpropertyvalues restituito da CurrentValues, OriginalValues o GetDatabaseValues. Questo clone conterrà i valori delle proprietà dall'oggetto elementi dbpropertyvalues usato per crearlo. Esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

Si noti che l'oggetto restituito non è l'entità e non viene rilevata dal contesto. Anche l'oggetto restituito non dispone di relazioni impostate su altri oggetti.  

L'oggetto clonato può essere utile per risolvere i problemi relativi agli aggiornamenti simultanei al database, soprattutto in caso di utilizzo di un'interfaccia utente che include data binding a oggetti di un determinato tipo.  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>Recupero e impostazione dei valori correnti o originali delle proprietà complesse  

Il valore di un intero oggetto complesso può essere letto e impostato usando il metodo Property, così come può essere per una proprietà primitiva. Inoltre, è possibile eseguire il drill-down nell'oggetto complesso e leggere o impostare le proprietà di tale oggetto o persino un oggetto annidato. Ecco alcuni esempi:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

Usare la proprietà OriginalValue invece della proprietà CurrentValue per ottenere o impostare un valore originale.  

Si noti che è possibile usare la proprietà o il Metodo ComplexProperty per accedere a una proprietà complessa. Tuttavia, il Metodo ComplexProperty deve essere usato se si vuole eseguire il drill-down nell'oggetto complesso con chiamate di proprietà o ComplexProperty aggiuntive.  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>Uso di elementi dbpropertyvalues per accedere a proprietà complesse  

Quando si usa CurrentValues, OriginalValues o GetDatabaseValues per ottenere tutti i valori correnti, originali o di database per un'entità, i valori di tutte le proprietà complesse vengono restituiti come oggetti elementi dbpropertyvalues annidati. Questi oggetti annidati possono quindi essere utilizzati per ottenere i valori dell'oggetto complesso. Ad esempio, il metodo seguente consente di stampare i valori di tutte le proprietà, inclusi i valori di tutte le proprietà complesse e le proprietà complesse annidate.  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

Per stampare tutti i valori di proprietà correnti, il metodo verrebbe chiamato come segue:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
