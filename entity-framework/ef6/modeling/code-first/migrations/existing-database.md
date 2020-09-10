---
title: Migrazioni Code First con un database esistente-EF6
description: Migrazioni Code First con un database esistente in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: f0cc4f93-67dd-4664-9753-0a9f913814db
uid: ef6/modeling/code-first/migrations/existing-database
ms.openlocfilehash: f7638cac17bf9152c829ae415fc941aa0a0c6cab
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616943"
---
# <a name="code-first-migrations-with-an-existing-database"></a>Migrazioni Code First con un database esistente
> [!NOTE]
> **EF 4.3 e versioni successive** : le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 4,1. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Questo articolo illustra l'uso di Migrazioni Code First con un database esistente, uno che non è stato creato da Entity Framework.

> [!NOTE]
> Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base. In caso contrario, sarà necessario leggere [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) prima di continuare.

## <a name="screencasts"></a>Screencast

Se si preferisce guardare uno screencast anziché leggere questo articolo, i due video seguenti riguardano lo stesso contenuto di questo articolo.

### <a name="video-one-migrations---under-the-hood"></a>Video uno: "migrazioni-dietro le quinte"

[Questo screencast](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) illustra il modo in cui le migrazioni tracciano e usano le informazioni sul modello per rilevare le modifiche al modello.

### <a name="video-two-migrations---existing-databases"></a>Video due: "migrazioni-database esistenti"

Basandosi sui concetti del video precedente, [questo screencast](https://channel9.msdn.com/blogs/ef/migrations-existing-databases) illustra come abilitare e usare le migrazioni con un database esistente.

## <a name="step-1-create-a-model"></a>Passaggio 1: creare un modello

Il primo passaggio consiste nel creare un modello di Code First destinato al database esistente. L'argomento [Code First a un database esistente](xref:ef6/modeling/code-first/workflows/existing-database) fornisce istruzioni dettagliate su come eseguire questa operazione.

>[!NOTE]
> Prima di apportare modifiche al modello che richiederebbero modifiche allo schema del database, è importante attenersi ai passaggi rimanenti di questo argomento. Per la procedura seguente è necessario che il modello sia sincronizzato con lo schema del database.

## <a name="step-2-enable-migrations"></a>Passaggio 2: abilitare le migrazioni

Il passaggio successivo consiste nell'abilitare le migrazioni. È possibile eseguire questa operazione eseguendo il comando **Enable-Migrations** nella console di gestione pacchetti.

Questo comando crea una cartella nella soluzione denominata migrazioni e inserisce una singola classe al suo interno chiamata configurazione. La classe di configurazione consente di configurare le migrazioni per l'applicazione. per altre informazioni, vedere l'argomento [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) .

## <a name="step-3-add-an-initial-migration"></a>Passaggio 3: aggiungere una migrazione iniziale

Al termine della creazione e dell'applicazione delle migrazioni al database locale, è possibile che si desideri applicare tali modifiche anche ad altri database. È ad esempio possibile che il database locale sia un database di prova e che sia necessario applicare le modifiche anche a un database di produzione e/o ad altri database di prova degli sviluppatori. Per questo passaggio è possibile scegliere tra due opzioni, a seconda che lo schema di altri database sia vuoto o che attualmente corrisponda allo schema del database locale.

-   **Opzione uno: utilizzare lo schema esistente come punto di partenza.** È consigliabile utilizzare questo approccio quando gli altri database a cui verranno applicate le migrazioni in futuro avranno lo stesso schema del database locale. Ad esempio, è possibile usare questo se il database di test locale corrisponde attualmente al V1 del database di produzione e in seguito si applicano queste migrazioni per aggiornare il database di produzione alla versione V2.
-   **Opzione due: usare il database vuoto come punto di partenza.** È consigliabile utilizzare questo approccio quando altri database a cui verranno applicate le migrazioni in futuro sono vuoti (o non sono ancora presenti). Ad esempio, è possibile usarlo se si è iniziato a sviluppare l'applicazione usando un database di test, ma senza usare le migrazioni e in un secondo momento si vuole creare un database di produzione da zero.

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>Opzione uno: utilizzare lo schema esistente come punto di partenza

Migrazioni Code First utilizza uno snapshot del modello archiviato nella migrazione più recente per rilevare le modifiche apportate al modello (è possibile trovare informazioni dettagliate su questo in [migrazioni Code First in ambienti Team](xref:ef6/modeling/code-first/migrations/teams)). Poiché si presuppone che i database abbiano già lo schema del modello corrente, verrà generata una migrazione vuota (no-op) con il modello corrente come snapshot.

1.  Eseguire il comando **Add-Migration InitialCreate – IgnoreChanges** nella console di gestione pacchetti. In questo modo viene creata una migrazione vuota con il modello corrente come snapshot.
2.  Eseguire il comando **Update-database** nella console di gestione pacchetti. Verrà applicata la migrazione InitialCreate al database. Poiché la migrazione effettiva non contiene alcuna modifica, viene semplicemente aggiunta una riga alla \_ \_ tabella MigrationsHistory che indica che la migrazione è già stata applicata.

### <a name="option-two-use-empty-database-as-a-starting-point"></a>Opzione due: usare un database vuoto come punto di partenza

In questo scenario sono necessarie migrazioni per poter creare l'intero database da zero, incluse le tabelle già presenti nel database locale. Verrà generata una migrazione InitialCreate che include la logica per la creazione dello schema esistente. Si renderà quindi il database esistente simile a questa migrazione è già stata applicata.

1.  Eseguire il comando **Add-Migration InitialCreate** nella console di gestione pacchetti. In questo modo viene creata una migrazione per creare lo schema esistente.
2.  Impostare come commento tutto il codice nel metodo up della migrazione appena creata. Questo consentirà di applicare la migrazione al database locale senza provare a ricreare tutte le tabelle e così via.
3.  Eseguire il comando **Update-database** nella console di gestione pacchetti. Verrà applicata la migrazione InitialCreate al database. Poiché la migrazione effettiva non contiene alcuna modifica (perché è stata impostata temporaneamente come commento), viene semplicemente aggiunta una riga alla \_ \_ tabella MigrationsHistory che indica che la migrazione è già stata applicata.
4.  Annulla il commento del codice nel metodo up. Ciò significa che quando questa migrazione viene applicata ai database futuri, lo schema già esistente nel database locale verrà creato dalle migrazioni.

## <a name="things-to-be-aware-of"></a>Aspetti da tenere presenti

Quando si usano le migrazioni su un database esistente, è necessario tenere presenti alcuni aspetti.

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>I nomi predefiniti/calcolati potrebbero non corrispondere allo schema esistente

Le migrazioni specificano in modo esplicito i nomi delle colonne e delle tabelle durante l'impalcatura di una migrazione. Tuttavia, esistono altri oggetti di database per cui la migrazione calcola un nome predefinito durante l'applicazione delle migrazioni. Sono inclusi gli indici e i vincoli FOREIGN KEY. Quando la destinazione è uno schema esistente, i nomi calcolati potrebbero non corrispondere a quello effettivamente esistente nel database.

Di seguito sono riportati alcuni esempi di quando è necessario tenere presente quanto segue:

**Se è stato usato il valore ' opzione uno: USA schema esistente come punto di partenza ' dal passaggio 3:**

-   Se le modifiche future apportate al modello richiedono la modifica o l'eliminazione di uno degli oggetti di database denominati in modo diverso, sarà necessario modificare la migrazione con impalcatura per specificare il nome corretto. Le API Migrations hanno un parametro nome facoltativo che consente di eseguire questa operazione.
    Ad esempio, lo schema esistente può includere una tabella post con una colonna chiave esterna BlogId con un indice denominato IndexFk \_ BlogId. Tuttavia, per le migrazioni predefinite si prevede che questo indice sia denominato IX \_ BlogId. Se si modifica il modello che comporta l'eliminazione di questo indice, sarà necessario modificare la chiamata DropIndex con impalcatura per specificare il nome del BlogId IndexFk \_ .

**Se è stato usato "opzione due: USA database vuoto come punto di partenza" dal passaggio 3:**

-   Il tentativo di eseguire il metodo down della migrazione iniziale (ovvero il ripristino di un database vuoto) sul database locale potrebbe non riuscire perché le migrazioni tenterà di eliminare gli indici e i vincoli di chiave esterna utilizzando i nomi non corretti. Questo influirà solo sul database locale, perché gli altri database verranno creati da zero utilizzando il metodo up della migrazione iniziale.
    Se si desidera eseguire il downgrade del database locale esistente a uno stato vuoto, è più semplice eseguire questa operazione manualmente, eliminando il database o eliminando tutte le tabelle. Al termine di questo downgrade iniziale, tutti gli oggetti di database verranno ricreati con i nomi predefiniti, pertanto questo problema non si presenta di nuovo.
-   Se le modifiche future apportate al modello richiedono la modifica o l'eliminazione di uno degli oggetti di database denominati in modo diverso, questo non funzionerà con il database locale esistente, perché i nomi non corrispondono ai valori predefiniti. Tuttavia, funzionerà con i database creati ' da zerò perché utilizzeranno i nomi predefiniti scelti dalle migrazioni.
    Queste modifiche possono essere apportate manualmente nel database locale esistente oppure è consigliabile che le migrazioni ricreino il database da zero, come in altri computer.
-   I database creati utilizzando il metodo up della migrazione iniziale possono variare leggermente rispetto al database locale, poiché verranno utilizzati i nomi predefiniti calcolati per gli indici e i vincoli di chiave esterna. È anche possibile che si verifichino indici aggiuntivi poiché le migrazioni creeranno indici per le colonne chiave esterne per impostazione predefinita. questa situazione potrebbe non essere stata eseguita nel database locale originale.

### <a name="not-all-database-objects-are-represented-in-the-model"></a>Non tutti gli oggetti di database sono rappresentati nel modello

Gli oggetti di database che non fanno parte del modello non verranno gestiti dalle migrazioni. Questo può includere viste, stored procedure, autorizzazioni, tabelle che non fanno parte del modello, indici aggiuntivi e così via.

Di seguito sono riportati alcuni esempi di quando è necessario tenere presente quanto segue:

-   Indipendentemente dall'opzione scelta nel passaggio 3, se le modifiche future apportate al modello richiedono la modifica o l'eliminazione di queste migrazioni di oggetti aggiuntivi non sapranno apportare tali modifiche. Se, ad esempio, si rilascia una colonna con un indice aggiuntivo, le migrazioni non noteranno di eliminare l'indice. Sarà necessario aggiungerlo manualmente alla migrazione con impalcature.
-   Se è stata usata l'opzione "due: USA database vuoto come punto di partenza", questi oggetti aggiuntivi non verranno creati dal metodo up della migrazione iniziale.
    Se lo si desidera, è possibile modificare i metodi up e down per gestire questi oggetti aggiuntivi. Per gli oggetti che non sono supportati in modo nativo nell'API Migrations, ad esempio views, è possibile usare il metodo [SQL](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) per eseguire SQL non elaborato per crearli o eliminarli.
