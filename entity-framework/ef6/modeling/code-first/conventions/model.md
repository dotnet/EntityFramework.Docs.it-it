---
title: Convenzioni Model-Based-EF6
description: Convenzioni di Model-Based in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: c4978277ced72cc5c017af5f69b8760f316087d5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066434"
---
# <a name="model-based-conventions"></a><span data-ttu-id="38493-103">Convenzioni di Model-Based</span><span class="sxs-lookup"><span data-stu-id="38493-103">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="38493-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="38493-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="38493-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="38493-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="38493-106">Le convenzioni basate su modelli sono un metodo avanzato per la configurazione del modello basato sulle convenzioni.</span><span class="sxs-lookup"><span data-stu-id="38493-106">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="38493-107">Per la maggior parte degli scenari è consigliabile usare l' [API della convenzione di Code First personalizzata in DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) .</span><span class="sxs-lookup"><span data-stu-id="38493-107">For most scenarios the [Custom Code First Convention API on DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) should be used.</span></span> <span data-ttu-id="38493-108">Prima di usare le convenzioni basate su modelli, è consigliabile conoscere l'API DbModelBuilder per le convenzioni.</span><span class="sxs-lookup"><span data-stu-id="38493-108">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="38493-109">Le convenzioni basate su modelli consentono la creazione di convenzioni che interessano proprietà e tabelle che non possono essere configurate tramite le convenzioni standard.</span><span class="sxs-lookup"><span data-stu-id="38493-109">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="38493-110">Esempi di queste sono le colonne del discriminatore nella tabella per i modelli di gerarchia e le colonne di associazione indipendenti.</span><span class="sxs-lookup"><span data-stu-id="38493-110">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="38493-111">Creazione di una convenzione</span><span class="sxs-lookup"><span data-stu-id="38493-111">Creating a Convention</span></span>   

<span data-ttu-id="38493-112">Il primo passaggio per la creazione di una convenzione basata su modello è la scelta di quando nella pipeline è necessario applicare la convenzione al modello.</span><span class="sxs-lookup"><span data-stu-id="38493-112">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="38493-113">Esistono due tipi di convenzioni del modello, concettuale (C-Space) e Store (S-space).</span><span class="sxs-lookup"><span data-stu-id="38493-113">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="38493-114">Una convenzione C-Space viene applicata al modello compilato dall'applicazione, mentre una convenzione S-space viene applicata alla versione del modello che rappresenta il database e controlla elementi quali la modalità di denominazione delle colonne generate automaticamente.</span><span class="sxs-lookup"><span data-stu-id="38493-114">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="38493-115">Una convenzione del modello è una classe che si estende da IConceptualModelConvention o IStoreModelConvention.</span><span class="sxs-lookup"><span data-stu-id="38493-115">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="38493-116">Queste interfacce accettano entrambi un tipo generico che può essere di tipo MetadataItem usato per filtrare il tipo di dati a cui si applica la convenzione.</span><span class="sxs-lookup"><span data-stu-id="38493-116">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="38493-117">Aggiunta di una convenzione</span><span class="sxs-lookup"><span data-stu-id="38493-117">Adding a Convention</span></span>   

<span data-ttu-id="38493-118">Le convenzioni del modello vengono aggiunte allo stesso modo delle classi di convenzioni regolari.</span><span class="sxs-lookup"><span data-stu-id="38493-118">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="38493-119">Nel metodo **OnModelCreating** aggiungere la convenzione all'elenco di convenzioni per un modello.</span><span class="sxs-lookup"><span data-stu-id="38493-119">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

<span data-ttu-id="38493-120">È anche possibile aggiungere una convenzione in relazione a un'altra convenzione usando i metodi Conventions. AddBefore \<\> o conventions. AddAfter \<\> .</span><span class="sxs-lookup"><span data-stu-id="38493-120">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="38493-121">Per ulteriori informazioni sulle convenzioni che Entity Framework applica, vedere la sezione Note.</span><span class="sxs-lookup"><span data-stu-id="38493-121">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="38493-122">Esempio: Convenzione del modello discriminatore</span><span class="sxs-lookup"><span data-stu-id="38493-122">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="38493-123">Rinominare le colonne generate da EF è un esempio di qualcosa che non è possibile eseguire con le altre API delle convenzioni.</span><span class="sxs-lookup"><span data-stu-id="38493-123">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="38493-124">Si tratta di una situazione in cui l'uso delle convenzioni del modello è l'unica opzione.</span><span class="sxs-lookup"><span data-stu-id="38493-124">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="38493-125">Un esempio di come utilizzare una convenzione basata su modello per configurare colonne generate consiste nel personalizzare la modalità con cui vengono denominate le colonne del discriminatore.</span><span class="sxs-lookup"><span data-stu-id="38493-125">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="38493-126">Di seguito è riportato un esempio di una semplice convenzione basata su modello che Rinomina ogni colonna del modello denominato "discriminatore" in "EntityType".</span><span class="sxs-lookup"><span data-stu-id="38493-126">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="38493-127">Sono incluse le colonne che lo sviluppatore ha semplicemente denominato "discriminatore".</span><span class="sxs-lookup"><span data-stu-id="38493-127">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="38493-128">Poiché la colonna "discriminatore" è una colonna generata, questa deve essere eseguita nello spazio S.</span><span class="sxs-lookup"><span data-stu-id="38493-128">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="38493-129">Esempio: Convenzione di ridenominazione generale IA</span><span class="sxs-lookup"><span data-stu-id="38493-129">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="38493-130">Un altro esempio più complesso di convenzioni basate su modelli in azione consiste nel configurare il modo in cui vengono denominate le associazioni indipendenti (IAs).</span><span class="sxs-lookup"><span data-stu-id="38493-130">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="38493-131">Si tratta di una situazione in cui le convenzioni del modello sono applicabili perché gli IAs vengono generati da EF e non sono presenti nel modello a cui l'API DbModelBuilder può accedere.</span><span class="sxs-lookup"><span data-stu-id="38493-131">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="38493-132">Quando EF genera un'IA, viene creata una colonna denominata EntityType_KeyName.</span><span class="sxs-lookup"><span data-stu-id="38493-132">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="38493-133">Per un'associazione denominata Customer con una colonna chiave denominata CustomerId, ad esempio, verrebbe generata una colonna denominata Customer_CustomerId.</span><span class="sxs-lookup"><span data-stu-id="38493-133">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="38493-134">La convenzione seguente rimuove il \_ carattere '' dal nome della colonna generato per Ia.</span><span class="sxs-lookup"><span data-stu-id="38493-134">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i < properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a><span data-ttu-id="38493-135">Estensione delle convenzioni esistenti</span><span class="sxs-lookup"><span data-stu-id="38493-135">Extending Existing Conventions</span></span>   

<span data-ttu-id="38493-136">Se è necessario scrivere una convenzione simile a una delle convenzioni che Entity Framework si applica già al modello, è sempre possibile estendere tale convenzione per evitare che sia necessario riscriverla da zero.</span><span class="sxs-lookup"><span data-stu-id="38493-136">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="38493-137">Un esempio è la sostituzione della convenzione di corrispondenza ID esistente con una personalizzata.</span><span class="sxs-lookup"><span data-stu-id="38493-137">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="38493-138">Un ulteriore vantaggio dell'override della convenzione di chiave è che il metodo sottoposto a override verrà chiamato solo se non è presente alcuna chiave già rilevata o configurata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="38493-138">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="38493-139">Un elenco delle convenzioni usate da Entity Framework è disponibile qui: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .</span><span class="sxs-lookup"><span data-stu-id="38493-139">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

<span data-ttu-id="38493-140">È quindi necessario aggiungere la nuova Convenzione prima della convenzione di chiave esistente.</span><span class="sxs-lookup"><span data-stu-id="38493-140">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="38493-141">Dopo aver aggiunto il CustomKeyDiscoveryConvention, è possibile rimuovere IdKeyDiscoveryConvention.</span><span class="sxs-lookup"><span data-stu-id="38493-141">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="38493-142">Se non è stato rimosso il IdKeyDiscoveryConvention esistente, questa convenzione avrebbe ancora la precedenza sulla convenzione di individuazione ID poiché viene eseguita per prima, ma nel caso in cui non venga trovata alcuna proprietà "Key", verrà eseguita la convenzione "ID".</span><span class="sxs-lookup"><span data-stu-id="38493-142">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="38493-143">Questo comportamento si verifica perché ogni convenzione considera il modello come aggiornato dalla convenzione precedente, invece di operare su di esso in modo indipendente e tutti combinati, in modo che, se, ad esempio, una convenzione precedente abbia aggiornato un nome di colonna per trovare una corrispondenza con un elemento di interesse per la convenzione personalizzata (quando prima che il nome non fosse di interesse), verrà applicato a tale colonna.</span><span class="sxs-lookup"><span data-stu-id="38493-143">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a><span data-ttu-id="38493-144">Note</span><span class="sxs-lookup"><span data-stu-id="38493-144">Notes</span></span>  

<span data-ttu-id="38493-145">Un elenco delle convenzioni attualmente applicate da Entity Framework è disponibile nella documentazione di MSDN qui: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .</span><span class="sxs-lookup"><span data-stu-id="38493-145">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="38493-146">Questo elenco viene estratto direttamente dal codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="38493-146">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="38493-147">Il codice sorgente per Entity Framework 6 è disponibile su [GitHub](https://github.com/aspnet/entityframework6/) e molte delle convenzioni usate da Entity Framework sono ottimi punti di partenza per le convenzioni basate su modelli personalizzati.</span><span class="sxs-lookup"><span data-stu-id="38493-147">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
