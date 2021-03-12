---
title: Alternanza tra più modelli con lo stesso tipo di DbContext-EF Core
description: Alternanza tra più modelli con lo stesso tipo di DbContext usando Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/dynamic-model
ms.openlocfilehash: 0c418000b85c508569b8146af63bff205ae6d222
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024030"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>Alternanza tra più modelli con lo stesso tipo di DbContext

Il modello incorporato `OnModelCreating` può utilizzare una proprietà nel contesto per modificare la modalità di compilazione del modello. Si supponga, ad esempio, di voler configurare un'entità in modo diverso in base a una proprietà:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

Sfortunatamente, questo codice non funziona così com'è, poiché EF compila il modello ed esegue `OnModelCreating` una sola volta, memorizzando nella cache il risultato per motivi di prestazioni. Tuttavia, è possibile associare il meccanismo di memorizzazione nella cache del modello per rendere EF consapevole della proprietà che produce modelli diversi.

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF USA `IModelCacheKeyFactory` per generare chiavi di cache per i modelli. per impostazione predefinita, EF presuppone che per qualsiasi tipo di contesto specificato il modello sarà lo stesso, quindi l'implementazione predefinita di questo servizio restituisce una chiave che contiene solo il tipo di contesto. Per produrre modelli diversi dallo stesso tipo di contesto, è necessario sostituire il `IModelCacheKeyFactory` servizio con l'implementazione corretta; la chiave generata verrà confrontata con altre chiavi del modello usando il `Equals` metodo, prendendo in considerazione tutte le variabili che interessano il modello.

Nell'implementazione seguente viene preso `UseIntProperty` in considerazione quando si produce una chiave di cache del modello:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

Infine, registrare il nuovo `IModelCacheKeyFactory` nel contesto `OnConfiguring` :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

Per ulteriori informazioni sul contesto, vedere il [progetto di esempio completo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/DynamicModel) .
