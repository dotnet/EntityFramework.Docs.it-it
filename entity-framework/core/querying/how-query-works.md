---
title: Funzionamento delle query - EF Core
description: Informazioni generali sul modo in cui Entity Framework Core compila ed esegue internamente le query
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: f3a6794b9bfdf70ae40bc8e97ee41861931b9b46
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071186"
---
# <a name="how-queries-work"></a>Funzionamento delle query

Entity Framework Core usa query LINQ (Language Integrated Query) per eseguire query sui dati dal database. Le query LINQ consentono di usare C#, o il linguaggio .NET che si preferisce, per generare query fortemente tipizzate in base alle classi di contesto ed entità derivate.

## <a name="the-life-of-a-query"></a>Ciclo di vita di una query

La descrizione seguente è una panoramica di alto livello del processo di esecuzione di ogni query.

1. La query LINQ viene elaborata da Entity Framework Core per creare una rappresentazione pronta per essere elaborata dal provider di database
   1. Il risultato viene memorizzato nella cache in modo che tale elaborazione non debba essere ripetuta per ogni esecuzione della query
2. Il risultato viene passato al provider di database
   1. Il provider di database identifica le parti della query che possono essere valutate nel database
   2. Queste parti della query vengono convertite in un linguaggio di query specifico del database, ad esempio SQL per un database relazionale.
   3. Viene inviata una query al database e il set di risultati restituito (i risultati sono valori del database, non istanze di entità)
3. Per ogni elemento nel set di risultati
   1. Se la query è una query di rilevamento, EF controlla se i dati rappresentano un'entità già presente nello strumento di rilevamento delle modifiche per l'istanza del contesto
      * In caso affermativo, viene restituita l'entità esistente
      * In caso contrario, viene creata una nuova entità, il rilevamento delle modifiche viene configurato e viene restituita la nuova entità
   2. Se la query è una query senza rilevamento, viene sempre creata e restituita una nuova entità

## <a name="when-queries-are-executed"></a>Quando vengono eseguite le query

Quando si chiamano operatori LINQ, si sta semplicemente creando una rappresentazione in memoria della query. La query viene inviata al database solo al momento dell'utilizzo dei risultati.

Le operazioni più comuni che causano l'invio della query al database sono:

* Iterazione dei risultati in un ciclo `for`
* Utilizzando un operatore come `ToList` ,, `ToArray` `Single` , `Count` o gli overload asincroni equivalenti

> [!WARNING]  
> **Convalidare sempre l'input dell'utente**: anche se EF Core offre protezione da attacchi SQL injection tramite l'uso di parametri e valori letterali di escape nelle query, non convalida gli input. La convalida appropriata, in base ai requisiti dell'applicazione, deve essere eseguita prima che i valori provenienti da origini non attendibili vengano usati nelle query LINQ, assegnati alle proprietà dell'entità o passati ad altre API EF Core. Ciò include qualsiasi input dell'utente usato per costruire query in modo dinamico. Anche quando si usa LINQ, se si accetta l'input dell'utente per la creazione delle espressioni, è necessario assicurarsi che possano essere costruite solo le espressioni previste.
