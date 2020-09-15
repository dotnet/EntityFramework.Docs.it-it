---
title: Panoramica di Entity Framework Core - EF Core
description: Panoramica introduttiva di Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/index
ms.openlocfilehash: 1d320ecae06d9c05fe3a14ec955f7151de6a56e5
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071927"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core è una versione semplice, estendibile, [open source](https://github.com/aspnet/EntityFrameworkCore) e multipiattaforma della tecnologia di accesso ai dati di grande diffusione Entity Framework.

Entity Framework Core può essere usato come mapper relazionale a oggetti (O/RM), consentendo agli sviluppatori .NET di utilizzare un database con oggetti .NET ed eliminando la necessità della maggior parte del codice di accesso ai dati che è in genere necessario scrivere.

EF Core supporta molti motori di database. Per informazioni dettagliate, vedere [Provider di Database](xref:core/providers/index).

## <a name="the-model"></a>Il modello

Con Entity Framework Core, l'accesso ai dati viene eseguito tramite un modello. Un modello è costituito da classi di entità e da un contesto dell'oggetto che rappresenta una sessione con il database che consente di eseguire una query e salvare i dati. Per altre informazioni, vedere [Creazione di un modello](xref:core/modeling/index).

È possibile generare un modello da un database esistente, scrivere manualmente il codice di un modello in modo che corrisponda al database o usare [migrazioni di Entity Framework](xref:core/managing-schemas/migrations/index) per creare un database a partire dal modello e quindi svilupparlo man mano che il modello cambia nel tempo.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Query

Le istanze di classi di entità vengono recuperate dal database tramite LINQ (Language Integrated Query). Per altre informazioni, vedere [Esecuzione di query su dati](xref:core/querying/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Salvataggio di dati

I dati vengano creati, eliminati e modificati nel database tramite le istanze di classi di entità. Per altre informazioni, vedere [Salvataggio di dati](xref:core/saving/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a>Passaggi successivi

Per le esercitazioni introduttive, vedere [Introduzione a Entity Framework Core](xref:core/get-started/index).
