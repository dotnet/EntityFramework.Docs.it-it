---
title: 'Convenzioni basate su modelli: EF6'
description: Convenzioni basate su modello in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: 30a79f505939220b3d4040778397eab972e6a712
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617180"
---
# <a name="model-based-conventions"></a>Convenzioni basate su modelli
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Le convenzioni basate su modelli sono un metodo avanzato per la configurazione del modello basato sulle convenzioni. Per la maggior parte degli scenari è consigliabile usare l' [API della convenzione di Code First personalizzata in DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) . Prima di usare le convenzioni basate su modelli, è consigliabile conoscere l'API DbModelBuilder per le convenzioni.  

Le convenzioni basate su modelli consentono la creazione di convenzioni che interessano proprietà e tabelle che non possono essere configurate tramite le convenzioni standard. Esempi di queste sono le colonne del discriminatore nella tabella per i modelli di gerarchia e le colonne di associazione indipendenti.  

## <a name="creating-a-convention"></a>Creazione di una convenzione   

Il primo passaggio per la creazione di una convenzione basata su modello è la scelta di quando nella pipeline è necessario applicare la convenzione al modello. Esistono due tipi di convenzioni del modello, concettuale (C-Space) e Store (S-space). Una convenzione C-Space viene applicata al modello compilato dall'applicazione, mentre una convenzione S-space viene applicata alla versione del modello che rappresenta il database e controlla elementi quali la modalità di denominazione delle colonne generate automaticamente.  

Una convenzione del modello è una classe che si estende da IConceptualModelConvention o IStoreModelConvention.  Queste interfacce accettano entrambi un tipo generico che può essere di tipo MetadataItem usato per filtrare il tipo di dati a cui si applica la convenzione.  

## <a name="adding-a-convention"></a>Aggiunta di una convenzione   

Le convenzioni del modello vengono aggiunte allo stesso modo delle classi di convenzioni regolari. Nel metodo **OnModelCreating** aggiungere la convenzione all'elenco di convenzioni per un modello.  

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

È anche possibile aggiungere una convenzione in relazione a un'altra convenzione usando i metodi Conventions. AddBefore \<\> o conventions. AddAfter \<\> . Per ulteriori informazioni sulle convenzioni che Entity Framework applica, vedere la sezione Note.  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>Esempio: Convenzione del modello discriminatore  

Rinominare le colonne generate da EF è un esempio di qualcosa che non è possibile eseguire con le altre API delle convenzioni.  Si tratta di una situazione in cui l'uso delle convenzioni del modello è l'unica opzione.  

Un esempio di come utilizzare una convenzione basata su modello per configurare colonne generate consiste nel personalizzare la modalità con cui vengono denominate le colonne del discriminatore.  Di seguito è riportato un esempio di una semplice convenzione basata su modello che Rinomina ogni colonna del modello denominato "discriminatore" in "EntityType".  Sono incluse le colonne che lo sviluppatore ha semplicemente denominato "discriminatore". Poiché la colonna "discriminatore" è una colonna generata, questa deve essere eseguita nello spazio S.  

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

## <a name="example-general-ia-renaming-convention"></a>Esempio: Convenzione di ridenominazione generale IA   

Un altro esempio più complesso di convenzioni basate su modelli in azione consiste nel configurare il modo in cui vengono denominate le associazioni indipendenti (IAs).  Si tratta di una situazione in cui le convenzioni del modello sono applicabili perché gli IAs vengono generati da EF e non sono presenti nel modello a cui l'API DbModelBuilder può accedere.  

Quando EF genera un'IA, viene creata una colonna denominata EntityType_KeyName. Per un'associazione denominata Customer con una colonna chiave denominata CustomerId, ad esempio, verrebbe generata una colonna denominata Customer_CustomerId. La convenzione seguente rimuove il \_ carattere '' dal nome della colonna generato per Ia.  

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

## <a name="extending-existing-conventions"></a>Estensione delle convenzioni esistenti   

Se è necessario scrivere una convenzione simile a una delle convenzioni che Entity Framework si applica già al modello, è sempre possibile estendere tale convenzione per evitare che sia necessario riscriverla da zero.  Un esempio è la sostituzione della convenzione di corrispondenza ID esistente con una personalizzata.   Un ulteriore vantaggio dell'override della convenzione di chiave è che il metodo sottoposto a override verrà chiamato solo se non è presente alcuna chiave già rilevata o configurata in modo esplicito. Un elenco delle convenzioni usate da Entity Framework è disponibile qui: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  

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

È quindi necessario aggiungere la nuova Convenzione prima della convenzione di chiave esistente. Dopo aver aggiunto il CustomKeyDiscoveryConvention, è possibile rimuovere IdKeyDiscoveryConvention.  Se non è stato rimosso il IdKeyDiscoveryConvention esistente, questa convenzione avrebbe ancora la precedenza sulla convenzione di individuazione ID poiché viene eseguita per prima, ma nel caso in cui non venga trovata alcuna proprietà "Key", verrà eseguita la convenzione "ID".  Questo comportamento si verifica perché ogni convenzione considera il modello come aggiornato dalla convenzione precedente, invece di operare su di esso in modo indipendente e tutti combinati, in modo che, se, ad esempio, una convenzione precedente abbia aggiornato un nome di colonna per trovare una corrispondenza con un elemento di interesse per la convenzione personalizzata (quando prima che il nome non fosse di interesse), verrà applicato a tale colonna.  

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

## <a name="notes"></a>Note  

Un elenco delle convenzioni attualmente applicate da Entity Framework è disponibile nella documentazione di MSDN qui: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  Questo elenco viene estratto direttamente dal codice sorgente.  Il codice sorgente per Entity Framework 6 è disponibile su [GitHub](https://github.com/aspnet/entityframework6/) e molte delle convenzioni usate da Entity Framework sono ottimi punti di partenza per le convenzioni basate su modelli personalizzati.  
