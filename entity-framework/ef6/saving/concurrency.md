---
title: Gestione dei conflitti di concorrenza-EF6
description: Gestione dei conflitti di concorrenza in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/concurrency
ms.openlocfilehash: 0cec285ab6071120e162567506d397a23c299177
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064510"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="7ea1a-103">Gestione dei conflitti di concorrenza (EF6)</span><span class="sxs-lookup"><span data-stu-id="7ea1a-103">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="7ea1a-104">La concorrenza ottimistica comporta un tentativo ottimistico di salvare l'entità nel database con la speranza che i dati non siano stati modificati dopo che l'entità è stata caricata.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-104">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="7ea1a-105">Se si scopre che i dati sono stati modificati, viene generata un'eccezione ed è necessario risolvere il conflitto prima di ritentare il salvataggio.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-105">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="7ea1a-106">Questo argomento illustra come gestire tali eccezioni in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-106">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="7ea1a-107">Le tecniche illustrate in questo argomento si applicano in modo analogo ai modelli creati con Code First ed EF Designer.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="7ea1a-108">Questo post non è il posto appropriato per una descrizione completa della concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-108">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="7ea1a-109">Le sezioni seguenti presuppongono una certa conoscenza della risoluzione della concorrenza e mostrano i modelli per le attività comuni.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-109">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="7ea1a-110">Molti di questi modelli utilizzano gli argomenti illustrati in utilizzo dei [valori delle proprietà](xref:ef6/saving/change-tracking/property-values).</span><span class="sxs-lookup"><span data-stu-id="7ea1a-110">Many of these patterns make use of the topics discussed in [Working with Property Values](xref:ef6/saving/change-tracking/property-values).</span></span>  

<span data-ttu-id="7ea1a-111">La risoluzione dei problemi di concorrenza quando si utilizzano associazioni indipendenti (in cui la chiave esterna non è mappata a una proprietà nell'entità) è molto più difficile rispetto a quando si utilizzano le associazioni di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-111">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="7ea1a-112">Se pertanto si intende eseguire la risoluzione della concorrenza nell'applicazione, è consigliabile eseguire sempre il mapping delle chiavi esterne alle entità.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-112">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="7ea1a-113">Tutti gli esempi seguenti presuppongono che si stiano usando associazioni di chiavi esterne.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-113">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="7ea1a-114">Un DbUpdateConcurrencyException viene generato da SaveChanges quando viene rilevata un'eccezione di concorrenza ottimistica durante il tentativo di salvare un'entità che usa associazioni di chiavi esterne.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-114">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="7ea1a-115">Risoluzione delle eccezioni di concorrenza ottimistica con ricarica (database WINS)</span><span class="sxs-lookup"><span data-stu-id="7ea1a-115">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="7ea1a-116">Il metodo Reload può essere utilizzato per sovrascrivere i valori correnti dell'entità con i valori ora presenti nel database.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-116">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="7ea1a-117">L'entità viene quindi nuovamente restituita all'utente in un certo formato ed è necessario riprovare a apportare nuovamente le modifiche e salvarle nuovamente.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-117">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="7ea1a-118">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7ea1a-118">For example:</span></span>  

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

<span data-ttu-id="7ea1a-119">Per simulare un'eccezione di concorrenza, è consigliabile impostare un punto di interruzione nella chiamata a SaveChanges, quindi modificare un'entità salvata nel database utilizzando un altro strumento, ad esempio SQL Server Management Studio.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-119">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="7ea1a-120">È anche possibile inserire una riga prima di SaveChanges per aggiornare direttamente il database usando SqlCommand.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-120">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="7ea1a-121">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7ea1a-121">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="7ea1a-122">Il metodo entrys su DbUpdateConcurrencyException restituisce le istanze di DbEntityEntry per le entità che non sono state aggiornate.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-122">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="7ea1a-123">Questa proprietà restituisce attualmente sempre un valore singolo per i problemi di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-123">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="7ea1a-124">Può restituire più valori per le eccezioni di aggiornamento generali. Un'alternativa per alcune situazioni potrebbe essere quella di ottenere le voci per tutte le entità che potrebbero dover essere ricaricate dal database e chiamare il ricaricamento per ognuna di queste.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-124">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="7ea1a-125">Risoluzione delle eccezioni di concorrenza ottimistica come WINS client</span><span class="sxs-lookup"><span data-stu-id="7ea1a-125">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="7ea1a-126">L'esempio precedente che usa il ricaricamento è talvolta denominato WINS del database o WINS di archiviazione perché i valori nell'entità vengono sovrascritti dai valori del database.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-126">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="7ea1a-127">In alcuni casi può essere utile eseguire l'operazione opposta e sovrascrivere i valori nel database con i valori attualmente presenti nell'entità.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-127">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="7ea1a-128">Questa operazione viene a volte definita client WINS e può essere eseguita ottenendo i valori correnti del database e definendoli come valori originali per l'entità.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-128">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="7ea1a-129">Per informazioni sui valori correnti e originali, vedere [utilizzo dei valori delle proprietà](xref:ef6/saving/change-tracking/property-values) . Per esempio:</span><span class="sxs-lookup"><span data-stu-id="7ea1a-129">(See [Working with Property Values](xref:ef6/saving/change-tracking/property-values) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="7ea1a-130">Risoluzione personalizzata delle eccezioni di concorrenza ottimistica</span><span class="sxs-lookup"><span data-stu-id="7ea1a-130">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="7ea1a-131">In alcuni casi può essere necessario combinare i valori attualmente presenti nel database con i valori attualmente presenti nell'entità.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-131">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="7ea1a-132">Questa operazione richiede in genere un'interazione utente o logica personalizzata.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-132">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="7ea1a-133">È possibile, ad esempio, presentare un modulo all'utente che contiene i valori correnti, i valori nel database e un set predefinito di valori risolti.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-133">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="7ea1a-134">L'utente dovrà quindi modificare i valori risolti in base alle esigenze e questi valori risolti verranno salvati nel database.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-134">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="7ea1a-135">Questa operazione può essere eseguita usando gli oggetti elementi dbpropertyvalues restituiti da CurrentValues e GetDatabaseValues sulla voce dell'entità.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-135">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="7ea1a-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7ea1a-136">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="7ea1a-137">Risoluzione personalizzata delle eccezioni di concorrenza ottimistica tramite oggetti</span><span class="sxs-lookup"><span data-stu-id="7ea1a-137">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="7ea1a-138">Il codice precedente usa le istanze di elementi dbpropertyvalues per passare i valori correnti, database e risolti.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-138">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="7ea1a-139">A volte può risultare più semplice usare le istanze del tipo di entità per questa operazione.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-139">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="7ea1a-140">Questa operazione può essere eseguita usando i metodi ToObject e SetValues di elementi dbpropertyvalues.</span><span class="sxs-lookup"><span data-stu-id="7ea1a-140">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="7ea1a-141">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7ea1a-141">For example:</span></span>  

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
