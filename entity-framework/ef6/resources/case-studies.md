---
title: Case Study per Entity Framework-EF6
description: Case Study per Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 342929f70aa09704c934bd1d89e177f6953bfc43
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073821"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Case Study Microsoft per Entity Framework
Il case study in questa pagina evidenzia alcuni progetti di produzione reali che hanno usato Entity Framework.
> [!NOTE]
> Le versioni dettagliate di questi case study non sono più disponibili nel sito Web Microsoft. Di conseguenza, i collegamenti sono stati rimossi.

## <a name="epicor"></a>Epicor
Epicor è una grande società di software globale, con oltre 400 sviluppatori, che sviluppa soluzioni ERP (Enterprise Resource Planning) per le aziende in oltre 150 paesi.
Il prodotto principale, Epicor 9, si basa su un'architettura orientata ai servizi (SOA) che usa la .NET Framework.
Con numerose richieste dei clienti per fornire supporto per LINQ (Language Integrated Query) e per ridurre il carico sui server SQL back-end, il team ha deciso di eseguire l'aggiornamento a Visual Studio 2010 e a .NET Framework 4,0.
Utilizzando la Entity Framework 4,0, sono riusciti a raggiungere questi obiettivi e a semplificare notevolmente lo sviluppo e la manutenzione.
In particolare, il supporto T4 completo del Entity Framework consentiva di assumere il controllo completo del codice generato e di compilare automaticamente le funzionalità di salvataggio delle prestazioni, ad esempio le query precompilate e la memorizzazione nella cache.

> "Abbiamo eseguito recentemente alcuni test delle prestazioni con il codice esistente e abbiamo potuto ridurre le richieste a SQL Server del 90%.
A causa di ADO.NET Entity Framework 4 ". – Erik Johnson, vicepresidente, ricerca prodotti  

## <a name="veracity-solutions"></a>Soluzioni di veridicità
Avendo acquisito un sistema software per la pianificazione degli eventi che avrebbe dovuto essere difficile da gestire ed estendere attraverso le soluzioni di veridicità a lungo termine utilizzate da Visual Studio 2010 per riscriverlo come un'applicazione Internet potente e facile da utilizzare basata su Silverlight 4.
Con i servizi RIA .NET è stato possibile creare rapidamente un livello di servizio sopra il Entity Framework che evitava la duplicazione del codice e consentiva la convalida comune e la logica di autenticazione tra i livelli.  

> "Siamo stati venduti sul Entity Framework al momento dell'introduzione e il Entity Framework 4 si è rivelato ancora migliore.
Gli strumenti sono stati migliorati ed è più semplice modificare i file con estensione edmx che definiscono il modello concettuale, il modello di archiviazione e il mapping tra tali modelli... Con il Entity Framework, posso far funzionare il livello di accesso ai dati in un giorno e compilarlo come faccio.
Il Entity Framework è il livello di accesso ai dati di fatto; Non so perché nessuno lo utilizzerebbe ". – Joe McBride, Senior Developer

## <a name="nec-display-solutions-of-america"></a>NEC Visualizza le soluzioni americane
NEC ha voluto entrare sul mercato per la pubblicità digitale basata sul posto con una soluzione per avvantaggiare gli inserzionisti e i proprietari di rete e aumentarne i ricavi.
A tale scopo, è stata avviata una coppia di applicazioni Web che consente di automatizzare i processi manuali necessari in una campagna pubblicitaria tradizionale.
I siti sono stati creati con ASP.NET, Silverlight 3, AJAX e WCF, insieme al Entity Framework nel livello di accesso ai dati per comunicare con SQL Server 2008.

> "Con SQL Server, abbiamo pensato che potessimo ottenere la velocità effettiva necessaria per servire gli inserzionisti e le reti con informazioni in tempo reale e l'affidabilità per garantire che le informazioni nelle applicazioni cruciali fossero sempre disponibili"-Mike Corcoran, Director of IT

## <a name="darwin-dimensions"></a>Dimensioni di Darwin
Grazie a un'ampia gamma di tecnologie Microsoft, il team di Darwin ha deciso di creare un portale di avatar online che i consumatori potevano usare per creare avatar accattivanti e realistici da usare in giochi, animazioni e pagine di social networking.
Con i vantaggi di produttività del Entity Framework e il pull di componenti quali Windows Workflow Foundation (WF) e Windows Server AppFabric (una cache di applicazioni in memoria altamente scalabile), il team è stato in grado di offrire un prodotto straordinario nel 35% meno tempo di sviluppo.
Nonostante la suddivisione dei membri del team in più paesi, il team che segue un processo di sviluppo agile con versioni settimanali.

 > "Cerchiamo di non creare tecnologia per la tecnologia. Come avvio, è fondamentale sfruttare la tecnologia che consente di risparmiare tempo e denaro.
 .NET è la scelta ideale per lo sviluppo rapido e conveniente. " – Zachary Olsen, Architect  

## <a name="silverware"></a>Argenteria
Con oltre 15 anni di esperienza nello sviluppo di soluzioni POS (Point-of-sale) per gruppi di ristoranti di piccole e medie dimensioni, il team di sviluppo di argenteria ha deciso di migliorare il proprio prodotto con funzionalità di livello aziendale per attirare catene di ristoranti più grandi.
Utilizzando la versione più recente degli strumenti di sviluppo di Microsoft, è stato possibile creare la nuova soluzione quattro volte più velocemente rispetto a prima.
Le nuove funzionalità principali come LINQ e la Entity Framework semplificano il passaggio da Crystal Reports a SQL Server 2008 e SQL Server Reporting Services (SSRS) per le proprie esigenze di archiviazione e Reporting dei dati.

> "La gestione efficace dei dati è fondamentale per il successo delle posate, ed è per questo motivo che abbiamo deciso di adottare il servizio di report SQL". -Nicholas Romanidis, Director of IT/Software Engineering
