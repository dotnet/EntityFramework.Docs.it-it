---
title: Conversione da EF6 a EF Core - EF
description: Informazioni generali sul trasferimento di un'applicazione da Entity Framework 6 a Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 62360dda92d951fe9a4f43a2b742a7457ad8692f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073591"
---
# <a name="porting-from-ef6-to-ef-core"></a>Conversione da EF6 a EF Core

A seguito delle modifiche sostanziali in EF Core, non è consigliabile tentare di spostare un'applicazione da EF6 a EF Core se non in presenza di un valido motivo per apportare la modifica.
Il trasferimento da EF6 a EF Core deve essere visto come una conversione più che come un aggiornamento.

> [!IMPORTANT]
> Prima di iniziare il processo di trasferimento, è importante verificare che EF Core soddisfi i requisiti di accesso ai dati per l'applicazione.

## <a name="missing-features"></a>Funzionalità mancanti

Verificare che EF Core disponga di tutte le funzionalità necessarie per l'uso nell'applicazione. Vedere [Confronto delle funzionalità](xref:efcore-and-ef6/index) per un confronto dettagliato tra il set di funzionalità di EF Core e il set di EF6. Se mancano alcune funzionalità necessarie, assicurarsi che sia possibile compensare la mancanza di queste funzionalità prima di eseguire il trasferimento a EF Core.

## <a name="behavior-changes"></a>Modifiche del comportamento

Questo è un elenco non esaustivo delle modifiche del comportamento tra EF6 ed EF Core. È importante tenere presenti queste considerazioni quando si trasferisce l'applicazione, in quanto possono cambiare il comportamento dell'applicazione, ma non vengono visualizzate come errori di compilazione dopo il passaggio a EF Core.

### <a name="dbsetaddattach-and-graph-behavior"></a>Comportamento di DbSet.Add/Attach e del grafico

Quando in EF6 si chiama `DbSet.Add()` su un'entità viene eseguita una ricerca ricorsiva di tutte le entità a cui si fa riferimento nelle proprietà di navigazione. Anche tutte le entità trovate e non ancora rilevate dal contesto vengono contrassegnate come aggiunte. `DbSet.Attach()` si comporta allo stesso modo, ad eccezione del fatto che tutte le entità sono contrassegnate come non modificate.

**EF Core esegue una ricerca ricorsiva simile, ma con alcune regole leggermente diverse.**

*  L'entità radice è sempre nello stato richiesto (aggiunto per `DbSet.Add` e non modificato per `DbSet.Attach`).

*  **Per le entità rilevate durante la ricerca ricorsiva delle proprietà di navigazione:**

    *  **Se la chiave primaria dell'entità è generata dall'archivio**

        * Se la chiave primaria non è impostata su un valore, lo stato viene impostato su aggiunto. Il valore della chiave primaria viene considerato come "non impostato" se riceve il valore CLR predefinito per il tipo di proprietà, ad esempio `0` per `int`, `null` per `string` e così via.

        * Se la chiave primaria è impostata su un valore, lo stato è impostato su non modificato.

    *  Se la chiave primaria non è generata dal database, l'entità viene impostata con lo stesso stato della radice.

### <a name="code-first-database-initialization"></a>Inizializzazione del database Code First

**EF6 si basa su varie regole magic che esegue per selezionare la connessione al database e inizializzare il database. Alcune di queste regole sono:**

* Se non viene eseguita alcuna configurazione, EF6 seleziona un database in SQL Express o in Local DB.

* Se nel file `App/Web.config` delle applicazioni è disponibile una stringa di connessione con lo stesso nome del contesto, viene usata questa connessione.

* Se il database non esiste, viene creato.

* Se nel database non esiste nessuna delle tabelle del modello, viene aggiunto al database lo schema del modello corrente. Se le migrazioni sono attivate, vengono usate per creare il database.

* Se il database esiste ed EF6 ha creato lo schema in precedenza, viene verificata la compatibilità dello schema con il modello corrente. Se il modello è stato modificato dopo la creazione dello schema, viene generata un'eccezione.

**EF Core non esegue nessuna di queste regole magic.**

* La connessione al database deve essere configurata in modo esplicito nel codice.

* Non viene eseguita alcuna inizializzazione. È necessario usare `DbContext.Database.Migrate()` per applicare le migrazioni (o `DbContext.Database.EnsureCreated()` e `EnsureDeleted()` per creare/eliminare il database senza usare le migrazioni).

### <a name="code-first-table-naming-convention"></a>Convenzione di denominazione della tabella Code First

EF6 elabora il nome della classe di entità in un servizio di pluralizzazione per calcolare il nome predefinito della tabella alla quale è stata mappata l'entità.

EF Core usa il nome della proprietà `DbSet` in cui è esposta l'entità nel contesto derivato. Se l'entità non dispone di una proprietà `DbSet` viene usato il nome della classe.
