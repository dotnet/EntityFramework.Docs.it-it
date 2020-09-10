---
title: Sequenze-EF Core
description: Configurazione di sequenze in un modello di Entity Framework Core
author: roji
ms.date: 12/18/2019
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/sequences
ms.openlocfilehash: 1a212f3944ca95ebc7675ff29b2687c82fdf7fc7
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619080"
---
# <a name="sequences"></a>Sequenze

> [!NOTE]  
> Le sequenze sono una funzionalità supportata in genere solo da database relazionali. Se si usa un database non relazionale, ad esempio Cosmos, controllare la documentazione del database per la generazione di valori univoci.

Una sequenza genera valori numerici univoci e sequenziali nel database. Le sequenze non sono associate a una tabella specifica e possono essere impostate più tabelle per creare valori dalla stessa sequenza.

## <a name="basic-usage"></a>Uso di base

È possibile impostare una sequenza nel modello e quindi utilizzarla per generare valori per le proprietà:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

Si noti che l'oggetto SQL specifico usato per generare un valore da una sequenza è specifico del database. L'esempio precedente funziona in SQL Server, ma avrà esito negativo in altri database. Per ulteriori informazioni, consultare la documentazione del database specifico.

## <a name="configuring-sequence-settings"></a>Configurazione delle impostazioni di sequenza

È anche possibile configurare altri aspetti della sequenza, ad esempio il relativo schema, il valore iniziale, l'incremento e così via:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
