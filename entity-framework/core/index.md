---
title: Panoramica di Entity Framework Core - EF Core
description: Panoramica introduttiva generale di Entity Framework Core
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: ccb1fa4cbc0bd1a02e1aeb613475bfe4b2c1d118
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429897"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core è una versione semplice, estendibile, [open source](https://github.com/dotnet/efcore) e multipiattaforma della tecnologia di accesso ai dati di grande diffusione Entity Framework.

EF Core può fungere da mapper relazionale a oggetti (O/RM), che:

* Consente agli sviluppatori .NET di utilizzare un database di utilizzando oggetti .NET.
* Elimina la necessità della maggior parte del codice di accesso ai dati che in genere deve essere scritto.

EF Core supporta molti motori di database. Per informazioni dettagliate, vedere [Provider di Database](xref:core/providers/index).

## <a name="the-model"></a>Il modello

Con Entity Framework Core, l'accesso ai dati viene eseguito tramite un modello. Un modello è costituito da classi di entità e da un oggetto di contesto che rappresenta una sessione con il database. L'oggetto Context consente l'esecuzione di query e il salvataggio dei dati. Per ulteriori informazioni, vedere [creazione di un modello](xref:core/modeling/index).

EF supporta gli approcci di sviluppo del modello seguenti:

* Genera un modello da un database esistente.
* Codificare manualmente un modello in modo che corrisponda al database.
* Una volta creato un modello, usare le [migrazioni](xref:core/managing-schemas/migrations/index) di Entity Framework per creare un database dal modello. Le migrazioni consentono di evolvere il database quando il modello viene modificato.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Query

Le istanze delle classi di entità vengono recuperate dal database utilizzando [LINQ (Language Integrated Query)](/dotnet/csharp/programming-guide/concepts/linq/). Per ulteriori informazioni, vedere [esecuzione di query sui dati](xref:core/querying/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Salvataggio dei dati

I dati vengano creati, eliminati e modificati nel database tramite le istanze di classi di entità. Per altre informazioni, vedere [Salvataggio di dati](xref:core/saving/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a>Considerazioni su EF O/RM

Sebbene EF Core sia efficace per astrarre molti dettagli di programmazione, esistono alcune procedure consigliate applicabili a qualsiasi O/RM che consentono di evitare problemi comuni nelle app di produzione:

* Le conoscenze a livello intermedio o superiori del server di database sottostante sono essenziali per progettare, eseguire il debug, profilare ed eseguire la migrazione dei dati in applicazioni di produzione a prestazioni elevate. Ad esempio, conoscenza di chiavi primarie ed esterne, vincoli, indici, normalizzazione, istruzioni DML e DDL, tipi di dati, profilatura e così via.
* Test funzionali e di integrazione: è importante replicare l'ambiente di produzione il più vicino possibile a:
  * Individuare i problemi nell'app che vengono visualizzati solo quando si usa una versione o un'edizione specifica del server di database.
  * Rilevare le modifiche di rilievo quando si aggiornano EF Core e altre dipendenze. Ad esempio, l'aggiunta o l'aggiornamento di Framework come ASP.NET Core, OData o automapper. Queste dipendenze possono influire EF Core in modi imprevisti.
* Test di stress e prestazioni con carichi rappresentativi. L'utilizzo ingenuo di alcune funzionalità non viene ridimensionato correttamente. Ad esempio, più raccolte includono, utilizzo intensivo del caricamento lazy, query condizionali su colonne non indicizzate, aggiornamenti e inserimenti di grandi dimensioni con valori generati dall'archivio, mancanza di gestione della concorrenza, modelli di grandi dimensioni, criteri di cache inadeguati.
* Revisione della sicurezza: ad esempio la gestione di stringhe di connessione e altri segreti, le autorizzazioni di database per l'operazione non di distribuzione, la convalida dell'input per SQL non elaborato e la crittografia per i dati sensibili.
* Assicurarsi che la registrazione e la diagnostica siano sufficienti e utilizzabili. Ad esempio, configurazione di registrazione appropriata, tag di query e Application Insights.
* Ripristino degli errori. Preparare le contingenze per scenari di errore comuni, ad esempio il rollback della versione, i server di fallback, la scalabilità orizzontale e il bilanciamento del carico, la mitigazione DoS e i backup dei dati.
* Distribuzione e migrazione di applicazioni. Pianificare il modo in cui le migrazioni verranno applicate durante la distribuzione. l'esecuzione all'avvio dell'applicazione può subire problemi di concorrenza e richiede autorizzazioni più elevate del necessario per il normale funzionamento. Utilizzare la gestione temporanea per facilitare il ripristino da errori irreversibili durante la migrazione. Per ulteriori informazioni, vedere [applicazione delle migrazioni](xref:core/managing-schemas/migrations/applying).
* Analisi e test dettagliati delle migrazioni generate. Le migrazioni devono essere testate accuratamente prima di essere applicate ai dati di produzione. Non è possibile modificare facilmente la forma dello schema e i tipi di colonna quando le tabelle contengono dati di produzione. Ad esempio, in SQL Server `nvarchar(max)` e `decimal(18, 2)` sono raramente i tipi migliori per le colonne mappate alle proprietà di stringa e decimali, ma queste sono le impostazioni predefinite usate da EF perché non sono in grado di conoscere lo scenario specifico.

## <a name="next-steps"></a>Passaggi successivi

Per le esercitazioni introduttive, vedere [Introduzione a Entity Framework Core](xref:core/get-started/overview/first-app).
