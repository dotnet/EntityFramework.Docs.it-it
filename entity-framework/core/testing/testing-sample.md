---
title: Esempio di test di EF Core-EF Core
description: Esempio che illustra come testare le applicazioni che usano Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: 7af516421a6ec3040b636507a6cd7976cabce3a9
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128797"
---
# <a name="ef-core-testing-sample"></a>Esempio di test di EF Core

> [!TIP]
> Il codice in questo documento è disponibile in GitHub come [esempio eseguibile](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).
> Si noti che alcuni di questi test **dovrebbero avere esito negativo**. I motivi di questo problema sono descritti di seguito.

Questo documento illustra un esempio per il test del codice che usa EF Core.

## <a name="the-application"></a>Applicazione

L' [esempio](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contiene due progetti:

- ItemsWebApi: un' [API Web molto semplice supportata da ASP.NET Core](/aspnet/core/tutorials/first-web-api) con un singolo controller
- Test: un progetto di test [xUnit](https://xunit.net/) per testare il controller

### <a name="the-model-and-business-rules"></a>Modello e regole business

Il modello che supporta questa API è costituito da due tipi di entità: Items e Tags .

- Items hanno un nome con distinzione tra maiuscole e minuscole e una raccolta di Tags .
- Ogni Tag ha un'etichetta e un conteggio che rappresenta il numero di volte in cui è stato applicato a Item .
- Ogni Item deve avere un solo Tag oggetto con un'etichetta specificata.
  - Se un elemento viene contrassegnato con la stessa etichetta più di una volta, il conteggio del tag esistente con tale etichetta viene incrementato al posto di un nuovo tag creato.
- L'eliminazione di un oggetto Item deve eliminare tutti gli associati Tags .

#### <a name="the-no-locitem-entity-type"></a>ItemTipo di entità

`Item`Tipo di entità:

[!code-csharp[ItemEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

E la relativa configurazione in `DbContext.OnModelCreating` :

[!code-csharp[ConfigureItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

Si noti che il tipo di entità vincola il modo in cui può essere usato per riflettere il modello di dominio e le regole business. In particolare:

- La chiave primaria viene mappata direttamente al `_id` campo e non esposta pubblicamente
  - EF rileva e usa il costruttore privato che accetta il nome e il valore della chiave primaria.
- La `Name` proprietà è di sola lettura e viene impostata solo nel costruttore.
- Tags vengono esposti come `IReadOnlyList<Tag>` per impedire la modifica arbitraria.
  - EF associa la `Tags` proprietà al `_tags` campo sottostante mediante la corrispondenza dei nomi.
  - Il `AddTag` metodo accetta un'etichetta tag e implementa la regola business descritta in precedenza.
    Ovvero, viene aggiunto un tag solo per le nuove etichette.
    In caso contrario, il conteggio su un'etichetta esistente viene incrementato.
- La `Tags` proprietà di navigazione è configurata per una relazione molti-a-uno
  - Non è necessario disporre di una proprietà di navigazione da Tag a Item , pertanto non è inclusa.
  - Inoltre, non Tag definisce una proprietà di chiave esterna.
    Al contrario, EF creerà e gestirà una proprietà in stato shadow.

#### <a name="the-no-loctag-entity-type"></a>TagTipo di entità

`Tag`Tipo di entità:

[!code-csharp[TagEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

E la relativa configurazione in `DbContext.OnModelCreating` :

[!code-csharp[ConfigureTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

In modo analogo a Item , Tag nasconde la chiave primaria e rende la proprietà di sola `Label` lettura.

### <a name="the-no-locitemscontroller"></a>Il Items controller

Il controller API Web è piuttosto semplice.
Ottiene un oggetto `DbContext` dal contenitore di inserimento delle dipendenze tramite l'inserimento del costruttore:

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

Dispone di metodi per ottenere tutti Items o un oggetto Item con un nome specificato:

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

Dispone di un metodo per aggiungere un nuovo Item :

[!code-csharp[PostItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

Metodo per contrassegnare un oggetto Item con un'etichetta:

[!code-csharp[PostTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

E un metodo per eliminare un oggetto Item e tutti gli associati Tags :

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

La maggior parte della convalida e della gestione degli errori sono state rimosse per ridurre il disordine.

## <a name="the-tests"></a>Test

I test sono organizzati per l'esecuzione con più configurazioni del provider di database:

- Provider di SQL Server, che è il provider utilizzato dall'applicazione
- Provider SQLite
- Provider SQLite che usa i database SQLite in memoria
- Provider di database in memoria EF

Questa operazione viene eseguita inserendo tutti i test in una classe di base, quindi ereditando da questo oggetto per eseguire il test con ogni provider.

> [!TIP]
> Se non si usa local DB, sarà necessario modificare la stringa di connessione SQL Server.
> Vedere [test con SQLite](xref:core/testing/sqlite) per istruzioni sull'uso di SQLite per i test in memoria.

I due test seguenti dovrebbero avere esito negativo:

- `Can_remove_item_and_all_associated_tags` Quando si esegue con il provider di database in memoria EF
- `Can_add_item_differing_only_by_case` Quando si esegue con il provider di SQL Server

Questo aspetto viene trattato in modo più dettagliato di seguito.

### <a name="setting-up-and-seeding-the-database"></a>Impostazione e seeding del database

XUnit, come la maggior parte dei framework di test, creerà una nuova istanza della classe di test per ogni esecuzione dei test.
Inoltre, XUnit non eseguirà i test in una determinata classe di test in parallelo.
Ciò significa che è possibile impostare e configurare il database nel costruttore di test e che sarà in uno stato noto per ogni test.

> [!TIP]
> Questo esempio ricrea il database per ogni test.
> Questo approccio funziona bene per i test di database SQLite e EF in memoria, ma può comportare un sovraccarico significativo con altri sistemi di database, tra cui SQL Server.
> Gli approcci per la riduzione di questo overhead sono trattati nella [condivisione dei database tra i test](xref:core/testing/sharing-databases).

Quando ogni test viene eseguito:

- DbContextOptions sono configurati per il provider in uso e passati al costruttore della classe base
  - Queste opzioni vengono archiviate in una proprietà e utilizzate in tutti i test per la creazione di istanze di DbContext
- Viene chiamato un metodo di inizializzazione per creare ed eseguire il seeding del database
  - Il metodo Seed garantisce che il database sia pulito e quindi ricreandolo
  - Alcune entità di test note vengono create e salvate nel database

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

Ogni classe di test concreta eredita quindi da questa.
Ad esempio:

[!code-csharp[SqliteItemsControllerTest](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a>Struttura di test

Anche se l'applicazione usa l'inserimento delle dipendenze, i test non lo eseguono.
Per usare l'inserimento delle dipendenze, tuttavia, il codice aggiuntivo richiesto ha un valore minimo.
Viene invece creato un DbContext utilizzando `new` e quindi passato direttamente come dipendenza al controller.

Ogni test esegue quindi il metodo sottoposto a test sul controller e dichiara i risultati come previsto.
Ad esempio:

[!code-csharp[CanGetItems](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

Si noti che per eseguire il seeding del database e per eseguire i test vengono usate istanze DbContext diverse.
In questo modo si garantisce che il test non stia usando (o inciampare) le entità rilevate dal contesto durante il seeding.
Corrisponde inoltre a ciò che accade in app Web e servizi.

I test che mutano il database creano una seconda istanza di DbContext nel test per motivi analoghi.
Ovvero creando un nuovo contesto pulito e quindi leggendolo dal database per assicurarsi che le modifiche siano state salvate nel database.
Ad esempio:

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

Due test leggermente più necessari coprono la logica di business per l'aggiunta di tags .

[!code-csharp[CanAddTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a>Problemi relativi all'utilizzo di provider di database diversi

Il test con un sistema di database diverso da quello usato nell'applicazione di produzione può causare problemi.
Questi sono trattati a livello concettuale nel [codice di test che usa EF Core](xref:core/testing/index).
Le sezioni seguenti illustrano due esempi di problemi che si verificano nei test di questo esempio.

### <a name="test-passes-when-the-application-is-broken"></a>Test superato quando l'applicazione è interruppe

Uno dei requisiti per l'applicazione è che " Items hanno un nome con distinzione tra maiuscole e minuscole e una raccolta di Tags ".
Questo è piuttosto semplice da testare:

[!code-csharp[CanAddItemCaseInsensitive](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

L'esecuzione di questo test nel database in memoria EF indica che tutto funziona correttamente.
Tutto sembra corretto quando si usa SQLite.
Tuttavia, il test ha esito negativo quando viene eseguito su SQL Server.

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

Ciò è dovuto al fatto che il database in memoria EF e il database SQLite fanno distinzione tra maiuscole e minuscole per impostazione predefinita.
SQL Server, d'altra parte, non fa distinzione tra maiuscole e minuscole.

EF Core, da progettazione, non modifica questi comportamenti perché la forzatura di una modifica con distinzione maiuscole/minuscole può avere un notevole effetto sulle prestazioni.

Quando sappiamo che questo è un problema, possiamo correggere l'applicazione e compensare i test.
Tuttavia, il punto in questo caso è che questo bug può essere ignorato se si verifica solo con il database in memoria EF o con i provider SQLite.

### <a name="test-fails-when-the-application-is-correct"></a>Il test ha esito negativo quando l'applicazione è corretta

Un altro requisito per l'applicazione è che "l'eliminazione di un Item dovrebbe eliminare tutti gli associati Tags ".
Anche in questo caso, è facile da testare:

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

Questo test viene superato SQL Server e SQLite, ma ha esito negativo con il database in memoria EF.

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

In questo caso, l'applicazione funziona correttamente perché SQL Server supporta le [eliminazioni a catena](xref:core/saving/cascade-delete).
SQLite supporta inoltre le eliminazioni a catena, così come la maggior parte dei database relazionali, quindi il test di questa operazione su SQLite funziona.
D'altra parte, il database in memoria EF [non supporta le eliminazioni a catena](https://github.com/dotnet/efcore/issues/3924).
Questo significa che questa parte dell'applicazione non può essere testata con il provider di database in memoria EF.
