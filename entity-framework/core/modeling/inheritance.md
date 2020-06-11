---
title: Ereditarietà-EF Core
description: Come configurare l'ereditarietà del tipo di entità usando Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664052"
---
# <a name="inheritance"></a>Ereditarietà

EF è in grado di eseguire il mapping di una gerarchia di tipi .NET a un database. In questo modo è possibile scrivere le entità .NET nel codice come di consueto, usando i tipi base e derivati e fare in modo che EF crei facilmente lo schema del database appropriato, emette query e così via. I dettagli effettivi su come viene eseguito il mapping di una gerarchia dei tipi sono dipendenti dal provider. in questa pagina viene descritto il supporto dell'ereditarietà nel contesto di un database relazionale.

Al momento, EF Core supporta solo il modello tabella per gerarchia (TPH). TPH usa una singola tabella per archiviare i dati per tutti i tipi nella gerarchia e viene usata una colonna discriminatore per identificare il tipo rappresentato da ogni riga.

> [!NOTE]
> La tabella per tipo (TPT) e la tabella per tipo concreto (TPC), supportati da EF6, non sono ancora supportate da EF Core. TPT è una funzionalità principale progettata per EF Core 5,0.

## <a name="entity-type-hierarchy-mapping"></a>Mapping della gerarchia dei tipi di entità

Per convenzione, EF non analizzerà automaticamente i tipi di base o derivati; Ciò significa che se si desidera che venga eseguito il mapping di un tipo CLR nella gerarchia, è necessario specificare in modo esplicito quel tipo nel modello. Se, ad esempio, si specifica solo il tipo di base di una gerarchia, EF Core includere in modo implicito tutti i relativi sottotipi.

Nell'esempio seguente viene esposto un DbSet per `Blog` e la relativa sottoclasse `RssBlog` . Se `Blog` dispone di qualsiasi altra sottoclasse, non verrà inclusa nel modello.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

Questo modello è mappato allo schema di database seguente (si noti la colonna *discriminatore* creata in modo implicito, che identifica il tipo di *Blog* archiviato in ogni riga):

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> Le colonne del database vengono rese automaticamente Nullable quando necessario quando si usa il mapping di TPH. Ad esempio, la colonna *RssUrl* ammette i valori null perché le istanze di *Blog* normali non hanno tale proprietà.

Se non si vuole esporre un DbSet per una o più entità nella gerarchia, è anche possibile usare l'API Fluent per assicurarsi che siano incluse nel modello.

> [!TIP]
> Se non si basano sulle convenzioni, è possibile specificare il tipo di base in modo esplicito utilizzando `HasBaseType` . È inoltre possibile utilizzare `.HasBaseType((Type)null)` per rimuovere un tipo di entità dalla gerarchia.

## <a name="discriminator-configuration"></a>Configurazione discriminatore

È possibile configurare il nome e il tipo della colonna discriminatore e i valori utilizzati per identificare ogni tipo nella gerarchia:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

Negli esempi precedenti, EF ha aggiunto il discriminatore in modo implicito come [proprietà shadow](xref:core/modeling/shadow-properties) nell'entità di base della gerarchia. Questa proprietà può essere configurata come qualsiasi altra:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Infine, è anche possibile eseguire il mapping del discriminatore a una normale proprietà .NET nell'entità:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a>Colonne condivise

Per impostazione predefinita, quando due tipi di entità di pari livello nella gerarchia hanno una proprietà con lo stesso nome, verrà eseguito il mapping a due colonne separate. Tuttavia, se il tipo è identico, è possibile eseguirne il mapping alla stessa colonna di database:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
