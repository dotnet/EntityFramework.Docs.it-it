---
title: Glossario Entity Framework-EF6
description: Glossario di Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: 19d5e9e3a480337c2bcb93be5f989cc622b67dad
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620195"
---
# <a name="entity-framework-glossary"></a>Glossario Entity Framework
## <a name="code-first"></a>Code First
Creazione di un modello di Entity Framework utilizzando il codice. Il modello può essere destinato a un database esistente o a un nuovo database.

## <a name="context"></a>Contesto
Classe che rappresenta una sessione con il database, che consente di eseguire query e salvare i dati. Un contesto deriva dalla classe DbContext o ObjectContext.

## <a name="convention-code-first"></a>Convenzione (Code First)
Regola che Entity Framework utilizza per dedurre la forma del modello dalle classi.

## <a name="database-first"></a>Database First
Creazione di un modello di Entity Framework, usando la finestra di progettazione EF, destinata a un database esistente.

## <a name="eager-loading"></a>Caricamento eager
Modello di caricamento di dati correlati in cui una query per un tipo di entità carica anche le entità correlate come parte della query.

## <a name="ef-designer"></a>EF Designer
Finestra di progettazione visiva in Visual Studio che consente di creare un modello di Entity Framework usando caselle e linee.

## <a name="entity"></a>Entità
Classe o oggetto che rappresenta i dati dell'applicazione come clienti, prodotti e ordini.

## <a name="entity-data-model"></a>Entity Data Model
Modello che descrive le entità e le relazioni tra di esse. EF utilizza EDM per descrivere il modello concettuale su cui si basano i programmi per sviluppatori. EDM si basa sul modello Entity Relationship introdotto da Dr. Peter Chen. EDM è stato originariamente sviluppato con l'obiettivo principale di diventare il modello di dati comune in una suite di tecnologie per sviluppatori e server Microsoft. EDM viene utilizzato anche come parte del protocollo OData.

## <a name="explicit-loading"></a>Caricamento esplicito
Modello di caricamento di dati correlati in cui gli oggetti correlati vengono caricati chiamando un'API.

## <a name="fluent-api"></a>API Fluent
API che può essere utilizzata per configurare un modello di Code First.

## <a name="foreign-key-association"></a>Associazione di chiavi esterne
Associazione tra entità in cui una proprietà che rappresenta la chiave esterna è inclusa nella classe dell'entità dipendente. Il prodotto, ad esempio, contiene una proprietà CategoryId.

## <a name="identifying-relationship"></a>Relazione di identificazione
Relazione in cui la chiave primaria dell'entità principale fa parte della chiave primaria dell'entità dipendente. In questo tipo di relazione l'entità dipendente non può esistere senza l'entità principale.

## <a name="independent-association"></a>Associazione indipendente
Associazione tra entità in cui non è presente alcuna proprietà che rappresenta la chiave esterna nella classe dell'entità dipendente. Una classe prodotto, ad esempio, contiene una relazione con una categoria, ma nessuna proprietà CategoryId. Entity Framework tiene traccia dello stato dell'associazione indipendentemente dallo stato delle entità alle due estremità dell'associazione.

## <a name="lazy-loading"></a>Caricamento lazy
Modello di caricamento di dati correlati in cui gli oggetti correlati vengono caricati automaticamente quando si accede a una proprietà di navigazione.

## <a name="model-first"></a>Model First
Creazione di un modello di Entity Framework, usando la finestra di progettazione EF, che viene quindi usato per creare un nuovo database.

## <a name="navigation-property"></a>Proprietà di navigazione
Proprietà di un'entità che fa riferimento a un'altra entità. Il prodotto, ad esempio, contiene una proprietà di navigazione di categoria e la categoria contiene una proprietà di navigazione Products.

## <a name="poco"></a>POCO
Acronimo dell'oggetto CLR normale. Una semplice classe utente che non ha dipendenze con alcun Framework. Nel contesto di EF, una classe di entità che non deriva da EntityObject, implementa qualsiasi interfaccia o contiene attributi definiti in EF. Anche le classi di entità separate dal framework di persistenza sono definite "persistenza ignorata".  

## <a name="relationship-inverse"></a>Relazione inversa
Estremità opposta di una relazione, ad esempio Product. Categoria e categoria. Prodotto.

## <a name="self-tracking-entity"></a>Entità con rilevamento automatico
Entità compilata da un modello di generazione del codice che facilita lo sviluppo a più livelli.

## <a name="table-per-concrete-type-tpc"></a>Tipo tabella per calcestruzzo (TPC)
Metodo di mapping dell'ereditarietà in cui ogni tipo non astratto nella gerarchia viene mappato a una tabella separata nel database.

## <a name="table-per-hierarchy-tph"></a>Tabella per gerarchia (TPH)
Metodo di mapping dell'ereditarietà in cui viene eseguito il mapping di tutti i tipi nella gerarchia alla stessa tabella nel database. Per identificare il tipo a cui è associata ogni riga, viene utilizzata una o più colonne discriminatore.

## <a name="table-per-type-tpt"></a>Tabella per tipo (TPT)
Metodo di mapping dell'ereditarietà in cui viene eseguito il mapping delle proprietà comuni di tutti i tipi nella gerarchia alla stessa tabella nel database, ma le proprietà univoche per ogni tipo vengono mappate a una tabella separata.

## <a name="type-discovery"></a>Individuazione del tipo
Processo di identificazione dei tipi che devono far parte di un modello di Entity Framework.
