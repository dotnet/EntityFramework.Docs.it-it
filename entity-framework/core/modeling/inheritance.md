---
title: Ereditarietà-EF Core
description: Come configurare l'ereditarietà del tipo di entità usando Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 3ec6e7bd98f9c9716c460d69fc707d95e5e47a05
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429520"
---
# <a name="inheritance"></a>Ereditarietà

EF è in grado di eseguire il mapping di una gerarchia di tipi .NET a un database. In questo modo è possibile scrivere le entità .NET nel codice come di consueto, usando i tipi base e derivati e fare in modo che EF crei facilmente lo schema del database appropriato, emette query e così via. I dettagli effettivi su come viene eseguito il mapping di una gerarchia dei tipi sono dipendenti dal provider. in questa pagina viene descritto il supporto dell'ereditarietà nel contesto di un database relazionale.

## <a name="entity-type-hierarchy-mapping"></a>Mapping della gerarchia dei tipi di entità

Per convenzione, EF non analizzerà automaticamente i tipi di base o derivati; Ciò significa che se si desidera che venga eseguito il mapping di un tipo CLR nella gerarchia, è necessario specificare in modo esplicito quel tipo nel modello. Se, ad esempio, si specifica solo il tipo di base di una gerarchia, EF Core includere in modo implicito tutti i relativi sottotipi.

Nell'esempio seguente viene esposto un DbSet per `Blog` e la relativa sottoclasse `RssBlog` . Se `Blog` dispone di qualsiasi altra sottoclasse, non verrà inclusa nel modello.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> Le colonne del database vengono rese automaticamente Nullable quando necessario quando si usa il mapping di TPH. Ad esempio, la `RssUrl` colonna ammette valori null perché le `Blog` istanze regolari non dispongono di tale proprietà.

Se non si vuole esporre un `DbSet` per una o più entità nella gerarchia, è anche possibile usare l'API Fluent per assicurarsi che siano incluse nel modello.

> [!TIP]
> Se non si basano sulle convenzioni, è possibile specificare il tipo di base in modo esplicito utilizzando `HasBaseType` . È inoltre possibile utilizzare `.HasBaseType((Type)null)` per rimuovere un tipo di entità dalla gerarchia.

## <a name="table-per-hierarchy-and-discriminator-configuration"></a>Configurazione tabella per gerarchia e discriminatore

Per impostazione predefinita, EF esegue il mapping dell'ereditarietà usando il modello *tabella per gerarchia* (TPH). TPH usa una singola tabella per archiviare i dati per tutti i tipi nella gerarchia e viene usata una colonna discriminatore per identificare il tipo rappresentato da ogni riga.

Il modello precedente è mappato allo schema di database seguente (si noti la colonna creata in modo implicito `Discriminator` , che identifica il tipo di `Blog` Archiviato in ogni riga).

![Screenshot dei risultati dell'esecuzione di una query sulla gerarchia di entità del blog usando un modello tabella per gerarchia](_static/inheritance-tph-data.png)

È possibile configurare il nome e il tipo della colonna discriminatore e i valori utilizzati per identificare ogni tipo nella gerarchia:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

Negli esempi precedenti, EF ha aggiunto il discriminatore in modo implicito come [proprietà shadow](xref:core/modeling/shadow-properties) nell'entità di base della gerarchia. Questa proprietà può essere configurata come qualsiasi altra:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Infine, è anche possibile eseguire il mapping del discriminatore a una normale proprietà .NET nell'entità:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

Quando si eseguono query per le entità derivate che utilizzano il modello TPH, EF Core aggiunge un predicato su una colonna discriminatore nella query. Questo filtro assicura che non vengano riportate righe aggiuntive per i tipi di base o i tipi di pari livello non presenti nel risultato. Questo predicato di filtro viene ignorato per il tipo di entità di base, poiché l'esecuzione di query per l'entità di base otterrà risultati per tutte le entità nella gerarchia. Quando materializzazione deriva da una query, se si passa a un valore discriminante, che non è mappato a un tipo di entità nel modello, viene generata un'eccezione perché non è noto come materializzare i risultati. Questo errore si verifica solo se il database contiene righe con valori discriminanti, di cui non è stato eseguito il mapping nel modello EF. Se si dispone di tali dati, è possibile contrassegnare il mapping del discriminatore in EF Core modello come incompleto per indicare che è necessario aggiungere sempre il predicato del filtro per l'esecuzione di query su qualsiasi tipo nella gerarchia. `IsComplete(false)` la chiamata sulla configurazione del discriminatore contrassegna il mapping come incompleto.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a>Colonne condivise

Per impostazione predefinita, quando due tipi di entità di pari livello nella gerarchia hanno una proprietà con lo stesso nome, verrà eseguito il mapping a due colonne separate. Tuttavia, se il tipo è identico, è possibile eseguirne il mapping alla stessa colonna di database:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a>Configurazione tabella per tipo

> [!NOTE]
> La tabella per tipo (TPT) è una nuova funzionalità di EF Core 5,0. La tabella per tipo concreto (TPC) è supportata da EF6, ma non è ancora supportata da EF Core.

Nel modello di mapping di TPT, viene eseguito il mapping di tutti i tipi a singole tabelle. Le proprietà che appartengono esclusivamente a un tipo di base o derivato sono archiviate in una tabella che viene mappata a quel tipo. Le tabelle con mapping ai tipi derivati archiviano anche una chiave esterna che unisce la tabella derivata alla tabella di base.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

EF creerà lo schema di database seguente per il modello precedente.

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> Se il vincolo PRIMARY KEY viene rinominato, il nuovo nome verrà applicato a tutte le tabelle di cui è stato eseguito il mapping alla gerarchia, le versioni future di EF consentiranno di rinominare il vincolo solo per una determinata tabella quando il [problema 19970](https://github.com/dotnet/efcore/issues/19970) è fisso.

Se si utilizza la configurazione bulk, è possibile recuperare il nome della colonna per una tabella specifica chiamando <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
