---
title: Gestione dei conflitti di concorrenza-EF6
description: Gestione dei conflitti di concorrenza in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
uid: ef6/saving/concurrency
ms.openlocfilehash: 1cec47ce346e8a6c86338747c01fba4d030e7388
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619882"
---
# <a name="handling-concurrency-conflicts-ef6"></a>Gestione dei conflitti di concorrenza (EF6)

La concorrenza ottimistica comporta un tentativo ottimistico di salvare l'entità nel database con la speranza che i dati non siano stati modificati dopo che l'entità è stata caricata. Se si scopre che i dati sono stati modificati, viene generata un'eccezione ed è necessario risolvere il conflitto prima di ritentare il salvataggio. Questo argomento illustra come gestire tali eccezioni in Entity Framework. Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.  

Questo post non è il posto appropriato per una descrizione completa della concorrenza ottimistica. Le sezioni seguenti presuppongono una certa conoscenza della risoluzione della concorrenza e mostrano i modelli per le attività comuni.  

Molti di questi modelli utilizzano gli argomenti illustrati in utilizzo dei [valori delle proprietà](xref:ef6/saving/change-tracking/property-values).  

La risoluzione dei problemi di concorrenza quando si utilizzano associazioni indipendenti (in cui la chiave esterna non è mappata a una proprietà nell'entità) è molto più difficile rispetto a quando si utilizzano le associazioni di chiave esterna. Se pertanto si intende eseguire la risoluzione della concorrenza nell'applicazione, è consigliabile eseguire sempre il mapping delle chiavi esterne alle entità. Tutti gli esempi seguenti presuppongono che si stiano usando associazioni di chiavi esterne.  

Un DbUpdateConcurrencyException viene generato da SaveChanges quando viene rilevata un'eccezione di concorrenza ottimistica durante il tentativo di salvare un'entità che usa associazioni di chiavi esterne.  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>Risoluzione delle eccezioni di concorrenza ottimistica con ricarica (database WINS)  

Il metodo Reload può essere utilizzato per sovrascrivere i valori correnti dell'entità con i valori ora presenti nel database. L'entità viene quindi nuovamente restituita all'utente in un certo formato ed è necessario riprovare a apportare nuovamente le modifiche e salvarle nuovamente. Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

Per simulare un'eccezione di concorrenza, è consigliabile impostare un punto di interruzione nella chiamata a SaveChanges, quindi modificare un'entità salvata nel database utilizzando un altro strumento, ad esempio SQL Server Management Studio. È anche possibile inserire una riga prima di SaveChanges per aggiornare direttamente il database usando SqlCommand. Ad esempio:  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

Il metodo entrys su DbUpdateConcurrencyException restituisce le istanze di DbEntityEntry per le entità che non sono state aggiornate. Questa proprietà restituisce attualmente sempre un valore singolo per i problemi di concorrenza. Può restituire più valori per le eccezioni di aggiornamento generali. Un'alternativa per alcune situazioni potrebbe essere quella di ottenere le voci per tutte le entità che potrebbero dover essere ricaricate dal database e chiamare il ricaricamento per ognuna di queste.  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>Risoluzione delle eccezioni di concorrenza ottimistica come WINS client  

L'esempio precedente che usa il ricaricamento è talvolta denominato WINS del database o WINS di archiviazione perché i valori nell'entità vengono sovrascritti dai valori del database. In alcuni casi può essere utile eseguire l'operazione opposta e sovrascrivere i valori nel database con i valori attualmente presenti nell'entità. Questa operazione viene a volte definita client WINS e può essere eseguita ottenendo i valori correnti del database e definendoli come valori originali per l'entità. Per informazioni sui valori correnti e originali, vedere [utilizzo dei valori delle proprietà](xref:ef6/saving/change-tracking/property-values) . Per esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>Risoluzione personalizzata delle eccezioni di concorrenza ottimistica  

In alcuni casi può essere necessario combinare i valori attualmente presenti nel database con i valori attualmente presenti nell'entità. Questa operazione richiede in genere un'interazione utente o logica personalizzata. È possibile, ad esempio, presentare un modulo all'utente che contiene i valori correnti, i valori nel database e un set predefinito di valori risolti. L'utente dovrà quindi modificare i valori risolti in base alle esigenze e questi valori risolti verranno salvati nel database. Questa operazione può essere eseguita usando gli oggetti elementi dbpropertyvalues restituiti da CurrentValues e GetDatabaseValues sulla voce dell'entità. Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>Risoluzione personalizzata delle eccezioni di concorrenza ottimistica tramite oggetti  

Il codice precedente usa le istanze di elementi dbpropertyvalues per passare i valori correnti, database e risolti. A volte può risultare più semplice usare le istanze del tipo di entità per questa operazione. Questa operazione può essere eseguita usando i metodi ToObject e SetValues di elementi dbpropertyvalues. Ad esempio:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
