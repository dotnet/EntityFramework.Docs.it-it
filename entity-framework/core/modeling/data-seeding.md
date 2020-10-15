---
title: Seeding dei dati-EF Core
description: Uso del seeding dei dati per popolare un database con un set di dati iniziale usando Entity Framework Core
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: f8284d7473c6fbcafde25d01ec0709c2d50b666f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063699"
---
# <a name="data-seeding"></a>Seeding dei dati

Il seeding dei dati è il processo di popolamento di un database con un set di dati iniziale.

Questa operazione può essere eseguita in diversi modi EF Core:

* Dati di inizializzazione del modello
* Personalizzazione manuale della migrazione
* Logica di inizializzazione personalizzata

## <a name="model-seed-data"></a>Dati di inizializzazione del modello

Diversamente da EF6, in EF Core, il seeding dei dati può essere associato a un tipo di entità come parte della configurazione del modello. Le [migrazioni](xref:core/managing-schemas/migrations/index) EF core possono quindi calcolare automaticamente le operazioni di inserimento, aggiornamento o eliminazione da applicare durante l'aggiornamento del database a una nuova versione del modello.

> [!NOTE]
> Per determinare l'operazione da eseguire per ottenere i dati di inizializzazione nello stato desiderato, le migrazioni considerano solo le modifiche al modello. Pertanto, le eventuali modifiche apportate ai dati eseguiti al di fuori delle migrazioni potrebbero andare perse o causare un errore.

Ad esempio, vengono configurati i dati di inizializzazione per un `Blog` in `OnModelCreating` :

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

Per aggiungere entità che hanno una relazione, è necessario specificare i valori di chiave esterna:

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

Se il tipo di entità dispone di proprietà nello stato di ombreggiatura, è possibile usare una classe anonima per fornire i valori:

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

I tipi di entità di proprietà possono essere sottoposte a seeding in modo analogo:

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

Per ulteriori informazioni sul contesto, vedere il [progetto di esempio completo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) .

Una volta aggiunti i dati al modello, è necessario utilizzare le [migrazioni](xref:core/managing-schemas/migrations/index) per applicare le modifiche.

> [!TIP]
> Se è necessario applicare migrazioni come parte di una distribuzione automatica, è possibile [creare uno script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) che può essere visualizzato in anteprima prima dell'esecuzione.

In alternativa, è possibile utilizzare `context.Database.EnsureCreated()` per creare un nuovo database contenente i dati di inizializzazione, ad esempio per un database di prova o quando si utilizza il provider in memoria o qualsiasi database non relazionale. Si noti che se il database esiste già, `EnsureCreated()` non aggiornerà lo schema né i dati di inizializzazione nel database. Per i database relazionali non è necessario chiamare `EnsureCreated()` se si prevede di utilizzare le migrazioni.

### <a name="limitations-of-model-seed-data"></a>Limitazioni dei dati di inizializzazione del modello

Questo tipo di dati di inizializzazione è gestito dalle migrazioni e lo script per aggiornare i dati già presenti nel database deve essere generato senza connettersi al database. Questa operazione impone alcune restrizioni:

* Il valore della chiave primaria deve essere specificato anche se viene in genere generato dal database. Verrà usato per rilevare le modifiche dei dati tra le migrazioni.
* Se la chiave primaria viene modificata in qualsiasi modo, i dati con seeding in precedenza verranno rimossi.

Pertanto questa funzionalità è particolarmente utile per i dati statici che non si prevede di modificare al di fuori delle migrazioni e non dipende da altri elementi nel database, ad esempio i codici postali.

Se lo scenario include uno degli elementi seguenti, è consigliabile usare la logica di inizializzazione personalizzata descritta nell'ultima sezione:

* Dati temporanei per il test
* Dati che dipendono dallo stato del database
* Dati che richiedono la generazione di valori di chiave da parte del database, incluse le entità che utilizzano chiavi alternative come identità
* Dati che richiedono una trasformazione personalizzata (non gestita da [conversioni di valori](xref:core/modeling/value-conversions)), ad esempio un hash delle password
* Dati che richiedono chiamate a un'API esterna, ad esempio ASP.NET Core ruoli di identità e creazione di utenti

## <a name="manual-migration-customization"></a>Personalizzazione manuale della migrazione

Quando viene aggiunta una migrazione, le modifiche apportate ai dati specificati con `HasData` vengono trasformate in chiamate a `InsertData()` , `UpdateData()` e `DeleteData()` . Un modo per aggirare alcune delle limitazioni di `HasData` consiste nell'aggiungere manualmente queste chiamate o [operazioni personalizzate](xref:core/managing-schemas/migrations/operations) alla migrazione.

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>Logica di inizializzazione personalizzata

Un modo semplice e potente per eseguire il seeding dei dati consiste nell'usare prima che venga [`DbContext.SaveChanges()`](xref:core/saving/index) avviata l'esecuzione della logica dell'applicazione principale.

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> Il codice di seeding non deve far parte della normale esecuzione dell'app, perché ciò può causare problemi di concorrenza quando sono in esecuzione più istanze e richiede anche che l'app disponga dell'autorizzazione per modificare lo schema del database.

A seconda dei vincoli della distribuzione, il codice di inizializzazione può essere eseguito in modi diversi:

* Esecuzione locale dell'app di inizializzazione
* Distribuire l'app di inizializzazione con l'app principale, richiamando la routine di inizializzazione e disabilitando o rimuovendo l'app di inizializzazione.

Questo può essere in genere automatizzato tramite i [profili di pubblicazione](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).
