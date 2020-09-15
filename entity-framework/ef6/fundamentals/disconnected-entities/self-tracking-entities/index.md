---
title: Entità con rilevamento automatico - EF6
description: Entità con rilevamento automatico in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/index
ms.openlocfilehash: 9d5985a3f66b58a74536a1a56486bfc37c3f9f4d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070588"
---
# <a name="self-tracking-entities"></a>Entità con rilevamento automatico

> [!IMPORTANT]
> L'uso del modello di entità con rilevamento automatico non è più consigliabile. Continuerà a essere disponibile solo per supportare le applicazioni esistenti. Se l'applicazione richiede l'uso con grafici di entità disconnesse, prendere in considerazione altre alternative, come ad esempio [Trackable Entities](https://trackableentities.github.io/), che è una tecnologia simile alle entità con rilevamento automatico ma viene sviluppata in modo più attivo dalla community, oppure la scrittura di codice personalizzato usando le API di rilevamento delle modifiche di basso livello.

In un'applicazione basata su Entity Framework il rilevamento delle modifiche negli oggetti viene controllato da un contesto. Viene quindi usato il metodo SaveChanges per rendere permanenti le modifiche al database. Quando si usano le applicazioni a più livelli, gli oggetti entità vengono in genere disconnessi dal contesto ed è necessario decidere come tenere traccia delle modifiche e segnalarle al contesto. Le entità con rilevamento automatico consentono di tenere traccia delle modifiche in qualsiasi livello e di riprodurle quindi in un contesto per essere salvate.  

Usare le entità con rilevamento automatico solo se il contesto non è disponibile nel livello in cui vengono apportate le modifiche all'oggetto grafico. Se il contesto è disponibile, non è necessario usare le entità con rilevamento automatico poiché il contesto terrà traccia delle modifiche.  

Questo elemento di modello genera due file con estensione tt (modello di testo):  

- Il file **\<model name\>.tt** genera i tipi di entità e una classe helper contenente la logica di rilevamento delle modifiche usata dalle entità con rilevamento automatico, nonché i metodi di estensione che consentono l'impostazione dello stato sulle entità con rilevamento automatico.  
- Il file **\<model name\>.Context.tt** genera un contesto derivato e una classe di estensione contenente i metodi **ApplyChanges** per le classi **ObjectContext** e **ObjectSet**. Tali metodi consentono di esaminare le informazioni sul rilevamento delle modifiche contenute nel grafico delle entità con rilevamento automatico per dedurre il set di operazioni che è necessario eseguire per salvare le modifiche nel database.  

## <a name="get-started"></a>Introduzione  

Per iniziare, visitare la pagina [Self-Tracking Entities Walkthrough](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (Entità con rilevamento automatico - Procedura dettagliata).  

## <a name="functional-considerations-when-working-with-self-tracking-entities"></a>Considerazioni funzionali per l'uso delle entità con rilevamento automatico  
> [!IMPORTANT]
> L'uso del modello di entità con rilevamento automatico non è più consigliabile. Continuerà a essere disponibile solo per supportare le applicazioni esistenti. Se l'applicazione richiede l'uso con grafici di entità disconnesse, prendere in considerazione altre alternative, come ad esempio [Trackable Entities](https://trackableentities.github.io/), che è una tecnologia simile alle entità con rilevamento automatico ma viene sviluppata in modo più attivo dalla community, oppure la scrittura di codice personalizzato usando le API di rilevamento delle modifiche di basso livello.

Quando si utilizzano le entità con rilevamento automatico, tenere presenti le considerazioni seguenti.  

- Verificare che il progetto client disponga di un riferimento all'assembly che contiene i tipi di entità. Se si aggiunge solo il riferimento al servizio al progetto client, il progetto client utilizzerà i tipi proxy WCF e non i tipi di entità con rilevamento automatico effettivi. Ciò significa che non si otterranno le funzioni di notifica automatica che gestiscono il rilevamento delle entità sul client. Se non si desidera includere intenzionalmente i tipi di entità, sarà necessario impostare manualmente le informazioni sul rilevamento delle modifiche sul client affinché le modifiche vengano restituite al servizio.  
- Le chiamate all'operazione del servizio devono essere senza stato e creare una nuova istanza di contesto dell'oggetto. Si consiglia anche di creare il contesto dell'oggetto in un blocco **using**.  
- Quando si invia al servizio il grafico modificato nel client e si intende continuare a lavorare sullo stesso grafico nel client, è necessario scorrere manualmente il grafico e chiamare il metodo **AcceptChanges** su ogni oggetto per reimpostare la funzione di rilevamento delle modifiche.  

    > Se gli oggetti nel grafico contengono proprietà con valori generati dal database (ad esempio, valori di identità o concorrenza), Entity Framework sostituirà i valori di queste proprietà con i valori generati dal database dopo la chiamata al metodo **SaveChanges**. È possibile implementare l'operazione del servizio in modo che restituisca al client gli oggetti salvati o un elenco di valori di proprietà generati per gli oggetti. Il client dovrebbe quindi sostituire le istanze dell'oggetto o i valori di proprietà dell'oggetto con gli oggetti o i valori di proprietà restituiti dall'operazione del servizio.  
- È possibile che l'unione di grafici da più richieste del servizio introduca oggetti con valori di chiave duplicati nel grafico risultante. Entity Framework non rimuove gli oggetti con chiavi duplicate quando viene chiamato il metodo **ApplyChanges**, ma genera un'eccezione. Per evitare di avere grafici con valori di chiave duplicati, seguire uno dei modelli descritti nel blog seguente: [Self-Tracking Entities: ApplyChanges and duplicate entities](https://go.microsoft.com/fwlink/?LinkID=205119&clcid=0x409) (Entità con rilevamento automatico: ApplyChanges ed entità duplicate).  
- Quando si modifica la relazione tra oggetti impostando la proprietà della chiave esterna, la proprietà di navigazione di riferimento viene impostata su null e non viene sincronizzata con l'entità principale appropriata sul client. Dopo che il grafico è stato collegato al contesto dell'oggetto (ad esempio, dopo la chiamata al metodo **ApplyChanges**), le proprietà di chiave esterna e le proprietà di navigazione vengono sincronizzate.  

    > Non avere una proprietà di navigazione di riferimento sincronizzata con l'oggetto principale appropriato potrebbe essere un problema se è stata specificata l'eliminazione a catena sulla relazione di chiave esterna. L'eventuale eliminazione dell'oggetto principale non sarà propagata agli oggetti dipendenti. Se sono state specificate eliminazioni a catena, utilizzare le proprietà di navigazione per modificare le relazioni anziché impostare la proprietà della chiave esterna.  
- Le entità con rilevamento automatico non sono abilitate per eseguire il caricamento lazy.  
- La serializzazione binaria e la serializzazione agli oggetti della gestione dello stato di ASP.NET non sono supportate dalle entità con rilevamento automatico. Tuttavia, è possibile personalizzare il modello in modo da aggiungere il supporto per la serializzazione binaria. Per altre informazioni, vedere [Using Binary Serialization and ViewState with Self-Tracking Entities](https://go.microsoft.com/fwlink/?LinkId=199208) (Uso della serializzazione binaria e di ViewState con le entità con rilevamento automatico).  

## <a name="security-considerations"></a>Considerazioni sulla sicurezza  

Quando si usano le entità con rilevamento automatico tenere a mente di queste considerazioni sulla sicurezza:  

- Un servizio non dovrebbe ritenere attendibili le richieste di recuperare o aggiornare dati da un client non attendibile o tramite un canale non attendibile. È necessario autenticare un client: dovrebbe essere utilizzato un canale sicuro o un envelop del messaggio. È necessario convalidare le richieste dei client di aggiornare o recuperare i dati per assicurarsi che siano conformi alle modifiche previste e legittime per lo scenario specificato.  
- Evitare di utilizzare informazioni sensibili come chiavi di entità, ad esempio numeri di previdenza sociale. In questo modo si riduce la possibilità di serializzare inavvertitamente informazioni sensibili nei grafici dell'entità con rilevamento automatico in un client che non è completamente attendibile. Con le associazioni indipendenti, la chiave originale di un'entità correlata a quella sottoposta a serializzazione potrebbe essere inviata anche al client.  
- Per evitare di propagare al livello client messaggi di eccezione contenenti dati sensibili, le chiamate a **ApplyChanges** e **SaveChanges** nel livello server devono essere incapsulate nel codice di gestione delle eccezioni.  
