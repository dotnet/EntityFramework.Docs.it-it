---
title: Introduzione alle prestazioni-EF Core
description: Guida alle prestazioni per utilizzare in modo efficiente Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/miscellaneous/performance/index
ms.openlocfilehash: 14400d81ea3c93e2ebf40e8e585a457abf31478f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657843"
---
# <a name="introduction-to-performance"></a>Introduzione alle prestazioni

Le prestazioni del database sono un argomento vasto e complesso, che include un intero stack di componenti, ovvero database, rete, driver del database e livelli di accesso ai dati, ad esempio EF Core. Sebbene i livelli generali e O/RMs, ad esempio EF Core semplificare notevolmente lo sviluppo di applicazioni e migliorare la gestibilità, possono talvolta essere opachi, nascondendo dettagli interni critici per le prestazioni, ad esempio l'esecuzione di SQL. In questa sezione viene illustrato come ottenere prestazioni ottimali con EF Core e come evitare problemi comuni che possono influire negativamente sulle prestazioni dell'applicazione.

## <a name="identify-bottlenecks-and-measure-measure-measure"></a>Identificazione di colli di bottiglia e misura, misura, misura

Come sempre con le prestazioni, è importante non accelerare l'ottimizzazione senza dati che mostrano un problema; come afferma il grande Donald Knuth, "l'ottimizzazione prematura è la radice di tutti i mali". La sezione relativa alla [diagnosi delle prestazioni](xref:core/performance/performance-diagnosis) illustra vari modi per comprendere la posizione in cui l'applicazione dedica tempo alla logica del database e come individuare aree problematiche specifiche. Una volta identificata una query lenta, è possibile prendere in considerazione le soluzioni: il database non contiene un indice? È consigliabile provare altri modelli di query?

Eseguire sempre il benchmarking del codice e delle possibili alternative manualmente: la sezione relativa alla diagnosi delle prestazioni contiene un benchmark di esempio con BenchmarkDotNet, che è possibile usare come modello per i propri benchmark. Non presupporre che i benchmark pubblici generali si applichino così come sono al caso d'uso specifico. una vasta gamma di fattori quali la latenza del database, la complessità delle query e gli importi di dati effettivi nelle tabelle possono avere un effetto significativo sulla soluzione migliore. Molti benchmark pubblici, ad esempio, vengono eseguiti in condizioni di rete ideali, in cui la latenza per il database è quasi zero e con query estremamente leggere che difficilmente richiedono alcuna elaborazione (o l'I/O del disco) sul lato del database. Sebbene siano utili per confrontare i sovraccarichi di runtime di diversi livelli di accesso ai dati, le differenze rivelate in genere dimostrano che sono trascurabili in un'applicazione reale, in cui il database esegue il lavoro effettivo e la latenza per il database è un fattore di prestazioni significativo.

## <a name="aspects-of-data-access-performance"></a>Aspetti delle prestazioni di accesso ai dati

Le prestazioni di accesso ai dati complessive possono essere suddivise nelle categorie generali seguenti:

* **Prestazioni pure del database**. Con il database relazionale, EF converte le query LINQ dell'applicazione nelle istruzioni SQL che vengono eseguite dal database. Queste istruzioni SQL possono essere eseguite in modo più o meno efficiente. L'indice corretto nel posto giusto può fare un mondo di differenza nelle prestazioni SQL oppure riscrivere la query LINQ può generare una query SQL migliore.
* **Trasferimento dati di rete**. Come nel caso di qualsiasi sistema di rete, è importante limitare la quantità di dati in transito. In questo modo si ha la certezza di inviare e caricare solo i dati effettivamente necessari, ma anche di evitare il cosiddetto effetto "esplosione cartesiana" durante il caricamento di entità correlate.
* **Round trip di rete**. Oltre alla quantità di dati in corso, il round trip di rete, dal momento in cui il tempo necessario per l'esecuzione di una query nel database può essere sminuito dall'ora in cui i pacchetti passano tra l'applicazione e il database. Il sovraccarico del round trip dipende molto dall'ambiente. più lontano è il server di database, la latenza elevata e il su ogni round trip. Con l'avvento del cloud, le applicazioni si trovano sempre più lontano dal database e le applicazioni "chiacchierone" che eseguono un numero eccessivo di round trip di prestazioni ridotte. È quindi importante comprendere esattamente quando l'applicazione contatta il database, il numero di round trip che esegue e se tale numero può essere ridotto a icona.
* **Overhead di runtime EF**. Infine, EF aggiunge un sovraccarico in fase di esecuzione alle operazioni del database: EF deve compilare le query da LINQ to SQL (anche se questa operazione dovrebbe essere eseguita una sola volta), il rilevamento delle modifiche aggiunge un sovraccarico (ma può essere disabilitato) e così via. In pratica, l'overhead EF per le applicazioni reali è probabilmente irrilevante nella maggior parte dei casi, perché il tempo di esecuzione delle query nel database e la latenza di rete dominano il tempo totale; Tuttavia, è importante comprendere le opzioni disponibili e come evitare alcune insidie.

## <a name="know-whats-happening-under-the-hood"></a>Scopri cosa accade dietro le quinte

EF consente agli sviluppatori di concentrarsi sulla logica di business generando i risultati SQL, materializzazione ed eseguendo altre attività. Analogamente a qualsiasi livello o astrazione, tende anche a nascondere ciò che avviene sotto la cappa, ad esempio le query SQL effettive in esecuzione. Le prestazioni non sono necessariamente un aspetto cruciale di ogni applicazione, ma nelle applicazioni in cui si trova, è fondamentale che lo sviluppatore conosca cosa sta facendo EF: controllare le query SQL in uscita, seguire round trip per verificare che il problema N + 1 non sia in corso e così via.

## <a name="cache-outside-the-database"></a>Cache all'esterno del database

Infine, il modo più efficiente per interagire con un database consiste nel non interagire con esso. In altre parole, se l'accesso al database viene visualizzato come un collo di bottiglia delle prestazioni nell'applicazione, potrebbe essere utile memorizzare nella cache alcuni risultati all'esterno del database, in modo da ridurre al minimo le richieste. Anche se la memorizzazione nella cache aggiunge complessità, è una parte essenziale di qualsiasi applicazione scalabile: mentre il livello applicazione può essere ridimensionato facilmente aggiungendo server aggiuntivi per gestire un aumento del carico, il ridimensionamento del livello del database è in genere molto più complesso.
