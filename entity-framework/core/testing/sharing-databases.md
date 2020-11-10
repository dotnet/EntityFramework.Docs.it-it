---
title: Condivisione di database tra test-EF Core
description: Esempio che illustra come condividere un database tra più test
author: ajcvickers
ms.date: 04/25/2020
uid: core/testing/sharing-databases
ms.openlocfilehash: 95b756c80b983356a07fd836aa1b02f2835e6629
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431505"
---
# <a name="sharing-databases-between-tests"></a>Condivisione di database tra test

Nell' [esempio EF core testing](xref:core/testing/testing-sample) è stato illustrato come testare le applicazioni in diversi sistemi di database.
Per questo esempio, ogni test ha creato un nuovo database.
Si tratta di un modello efficace quando si usa SQLite o il database in memoria EF, ma può comportare un sovraccarico significativo quando si usano altri sistemi di database.

Questo esempio si basa sull'esempio precedente spostando la creazione del database in una fixture di test.
Ciò consente la creazione e il seeding di un singolo database di SQL Server solo una volta per tutti i test.

> [!TIP]
> Prima di continuare, assicurarsi di usare l' [esempio EF core testing](xref:core/testing/testing-sample) .

Non è difficile scrivere più test sullo stesso database.
Il trucco viene eseguito in modo che i test non vengano eseguiti tra loro durante l'esecuzione.
Questa operazione richiede informazioni:

* Come condividere in modo sicuro gli oggetti tra i test
* Quando il Framework di test esegue i test in parallelo
* Come proteggere il database in uno stato pulito per ogni test  

## <a name="the-fixture"></a>Fixture

Si userà una fixture di test per la condivisione degli oggetti tra i test.
La [documentazione di xUnit](https://xunit.net/docs/shared-context.html) indica che deve essere utilizzata una fixture "quando si desidera creare un singolo contesto di test e condividerlo tra tutti i test nella classe e quindi eseguire la pulizia al termine di tutti i test nella classe".

> [!TIP]
> Questo esempio usa [xUnit](https://xunit.net/), ma esistono concetti simili in altri Framework di test, tra cui [NUnit](https://nunit.org/).

Ciò significa che è necessario spostare la creazione e il seeding del database in una classe fixture.
Di seguito è riportata un'immagine di tale finestra:

[!code-csharp[SharedDatabaseFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

Per il momento, si noti come il costruttore:

* Crea una singola connessione di database per la durata della fixture
* Crea e inizializza il database chiamando il `Seed` metodo.

Ignora il blocco per il momento; verranno riportate in un secondo momento.

> [!TIP]
> Non è necessario che il codice di creazione e seeding sia asincrono.
> La sua esecuzione asincrona complica il codice e non migliora le prestazioni o la velocità effettiva dei test.

Il database viene creato eliminando prima qualsiasi database esistente e quindi creando un nuovo database.
In questo modo si garantisce che il database corrisponda al modello EF corrente anche se è stato modificato dopo l'ultima esecuzione dei test.

> [!TIP]
> Può essere più veloce "pulire" il database esistente usando qualcosa di simile a [respawn](https://jimmybogard.com/tag/respawn/) anziché ricrearlo ogni volta.
> È tuttavia necessario prestare attenzione per assicurarsi che lo schema del database sia aggiornato con il modello EF quando si esegue questa operazione.

La connessione al database viene eliminata quando la fixture viene eliminata.
A questo punto è inoltre possibile prendere in considerazione l'eliminazione del database di prova.
Tuttavia, questa operazione richiederà un blocco aggiuntivo e un conteggio dei riferimenti se la fixture è condivisa da più classi di test.
Inoltre, è spesso utile fare in modo che il database di test sia ancora disponibile per il debug di test non superati.  

## <a name="using-the-fixture"></a>Uso della fixture

XUnit dispone di un modello comune per l'associazione di una fixture di test a una classe di test:

[!code-csharp[UsingTheFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit creerà ora una singola istanza di fixture e la passerà a ogni istanza della classe di test.
(Ricordare dal primo [esempio di test](xref:core/testing/testing-sample) che xUnit crea una nuova istanza della classe di test ogni volta che viene eseguito un test). Ciò significa che il database verrà creato e sottoposta a seeding una volta, quindi ogni test utilizzerà il database.

Si noti che i test all'interno di una singola classe non verranno eseguiti in parallelo.
Ciò significa che per ogni test è possibile utilizzare la stessa connessione di database, anche se l' `DbConnection` oggetto non è thread-safe.

## <a name="maintaining-database-state"></a>Gestione dello stato del database

I test hanno spesso la necessità di mutare i dati di test con inserimenti, aggiornamenti ed eliminazioni.
Queste modifiche, tuttavia, influiscano su altri test che prevedono un database pulito e con seeding.

Questa operazione può essere eseguita eseguendo i test di mutazione all'interno di una transazione.
Esempio:

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

Si noti che la transazione viene creata quando il test viene avviato ed eliminato al termine dell'operazione.
Se si elimina la transazione, ne verrà eseguito il rollback, quindi nessuna delle modifiche verrà visualizzata da altri test.

Il metodo helper per la creazione di un contesto (vedere il codice della fixture precedente) accetta questa transazione e sceglie il DbContext per utilizzarlo.

## <a name="sharing-the-fixture"></a>Condivisione della fixture

È possibile che sia stato notato il blocco del codice relativo alla creazione e al seeding del database.
Questa operazione non è necessaria per questo esempio perché solo una classe di test usa la fixture, quindi viene creata solo una singola istanza di fixture.

Tuttavia, potrebbe essere necessario usare la stessa fixture con più classi di test.
XUnit creerà un'istanza di fixture per ognuna di queste classi.
Questi possono essere usati da thread diversi che eseguono test in parallelo.
Pertanto, è importante avere un blocco appropriato per garantire che solo un thread faccia la creazione e il seeding del database.

> [!TIP]
> `lock`Qui è semplice.
> Non è necessario tentare di eseguire operazioni più complesse, ad esempio qualsiasi modello senza blocco.
