---
title: Sequenze-EF Core
description: Configurazione di sequenze in un modello di Entity Framework Core
author: roji
ms.date: 12/18/2019
uid: core/modeling/sequences
ms.openlocfilehash: e388bde11b1251564b37227e8884d2b8c34dc02e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128524"
---
# <a name="sequences"></a><span data-ttu-id="f8ea4-103">Sequenze</span><span class="sxs-lookup"><span data-stu-id="f8ea4-103">Sequences</span></span>

> [!NOTE]
> <span data-ttu-id="f8ea4-104">Le sequenze sono una funzionalità supportata in genere solo da database relazionali.</span><span class="sxs-lookup"><span data-stu-id="f8ea4-104">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="f8ea4-105">Se si usa un database non relazionale, ad esempio Cosmos, controllare la documentazione del database per la generazione di valori univoci.</span><span class="sxs-lookup"><span data-stu-id="f8ea4-105">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="f8ea4-106">Una sequenza genera valori numerici univoci e sequenziali nel database.</span><span class="sxs-lookup"><span data-stu-id="f8ea4-106">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="f8ea4-107">Le sequenze non sono associate a una tabella specifica e possono essere impostate più tabelle per creare valori dalla stessa sequenza.</span><span class="sxs-lookup"><span data-stu-id="f8ea4-107">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="f8ea4-108">Uso di base</span><span class="sxs-lookup"><span data-stu-id="f8ea4-108">Basic usage</span></span>

<span data-ttu-id="f8ea4-109">È possibile impostare una sequenza nel modello e quindi utilizzarla per generare valori per le proprietà:</span><span class="sxs-lookup"><span data-stu-id="f8ea4-109">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="f8ea4-110">Si noti che l'oggetto SQL specifico usato per generare un valore da una sequenza è specifico del database. L'esempio precedente funziona in SQL Server, ma avrà esito negativo in altri database.</span><span class="sxs-lookup"><span data-stu-id="f8ea4-110">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="f8ea4-111">Per ulteriori informazioni, consultare la documentazione del database specifico.</span><span class="sxs-lookup"><span data-stu-id="f8ea4-111">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="f8ea4-112">Configurazione delle impostazioni di sequenza</span><span class="sxs-lookup"><span data-stu-id="f8ea4-112">Configuring sequence settings</span></span>

<span data-ttu-id="f8ea4-113">È anche possibile configurare altri aspetti della sequenza, ad esempio il relativo schema, il valore iniziale, l'incremento e così via:</span><span class="sxs-lookup"><span data-stu-id="f8ea4-113">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
