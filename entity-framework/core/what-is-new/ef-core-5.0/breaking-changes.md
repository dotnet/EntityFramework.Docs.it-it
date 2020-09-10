---
title: Modifiche di rilievo in EF Core 5,0-EF Core
description: Elenco completo delle modifiche di rilievo introdotte in Entity Framework Core 5,0
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618684"
---
# <a name="breaking-changes-in-ef-core-50"></a>Modifiche di rilievo nella EF Core 5,0

Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.

## <a name="summary"></a>Riepilogo

| **Modifica di rilievo**                                                                                                                   | **Impatto** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Obbligatorio per la navigazione da principale a dipendente con semantica diversa](#required-dependent)                                 | Media     |
| [Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS](#geometric-sqlite)                                                   | Basso        |
| [I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato](#non-added-generation)  | Basso        |
| [IMigrationsModelDiffer USA ora IRelationalModel](#relational-model)                                                                 | Basso        |
| [I discriminatori sono di sola lettura](#read-only-discriminators)                                                                             | Basso        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS

[Rilevamento del problema #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Comportamento precedente**

HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry. Tuttavia, la creazione del database ha avuto solo effetto. Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive. Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nuovo comportamento**

Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna. Questo corrisponde maggiormente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.

**Perché**

L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.

**Soluzioni di prevenzione**

Utilizzare `HasColumnType` per specificare la dimensione:

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="required-dependent"></a>
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Obbligatorio per la navigazione da principale a dipendente con semantica diversa

[Rilevamento del problema #17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Comportamento precedente**

È possibile configurare solo le navigazioni all'entità come richiesto. Pertanto `RequiredAttribute` , utilizzando sull'esplorazione della classe dipendente (l'entità contenente la chiave esterna) creerebbe invece la chiave esterna nel tipo di entità di definizione.

**Nuovo comportamento**

Con il supporto aggiunto per i dipendenti richiesti, è ora possibile contrassegnare qualsiasi navigazione di riferimento in base alle esigenze, vale a dire che nel caso illustrato sopra la chiave esterna verrà definita sull'altro lato della relazione e le proprietà non verranno contrassegnate come necessarie.

`IsRequired`La chiamata prima di specificare l'entità finale dipendente è ora ambigua:

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**Perché**

Il nuovo comportamento è necessario per abilitare il supporto per i dipendenti richiesti ([vedere #12100](https://github.com/dotnet/efcore/issues/12100)).

**Soluzioni di prevenzione**

Rimuovere `RequiredAttribute` da spostamento a dipendente e posizionarlo invece nella navigazione verso l'entità o configurare la relazione in `OnModelCreating` :

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>I generatori di valori vengono chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato

[Rilevamento del problema #15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Comportamento precedente**

I generatori di valori sono stati chiamati solo quando lo stato dell'entità è stato modificato in aggiunto.

**Nuovo comportamento**

I generatori di valori vengono ora chiamati quando lo stato dell'entità viene modificato da scollegato a non modificato, aggiornato o eliminato e la proprietà contiene i valori predefiniti.

**Perché**

Questa modifica era necessaria per migliorare l'esperienza con le proprietà che non sono rese permanente nell'archivio dati e il cui valore viene generato sempre nel client.

**Soluzioni di prevenzione**

Per impedire che il generatore di valori venga chiamato, assegnare un valore non predefinito alla proprietà prima che lo stato venga modificato.

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer USA ora IRelationalModel

[Rilevamento del problema #20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**Comportamento precedente**

`IMigrationsModelDiffer` L'API è stata definita usando `IModel` .

**Nuovo comportamento**

`IMigrationsModelDiffer` API USA ora `IRelationalModel` . Tuttavia, lo snapshot del modello ancora contiene solo `IModel` perché questo codice fa parte dell'applicazione e Entity Framework possibile modificarlo senza apportare una modifica sostanziale.

**Perché**

`IRelationalModel` rappresentazione appena aggiunta dello schema del database. Il suo utilizzo per individuare le differenze è più rapido e accurato.

**Soluzioni di prevenzione**

Usare il codice seguente per confrontare il modello da `snapshot` con il modello da `context` :

```cs
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

Si prevede di migliorare questa esperienza in 6,0 ([vedere #22031](https://github.com/dotnet/efcore/issues/22031))

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a>I discriminatori sono di sola lettura

[Rilevamento del problema #21154](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**Comportamento precedente**

È possibile modificare il valore del discriminatore prima di chiamare `SaveChanges`

**Nuovo comportamento**

Nel caso precedente verrà generata un'eccezione.

**Perché**

EF non prevede che il tipo di entità cambi mentre è ancora in fase di rilevamento. Pertanto, la modifica del valore del discriminatore lascia il contesto in uno stato incoerente che può causare un comportamento imprevisto.

**Soluzioni di prevenzione**

Se la modifica del valore del discriminatore è necessaria e il contesto verrà eliminato immediatamente dopo la chiamata `SaveChanges` al discriminatore può essere reso modificabile:

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
