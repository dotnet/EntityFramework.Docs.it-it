---
title: Applicazione delle migrazioni-EF Core
description: Strategie per l'applicazione delle migrazioni dello schema ai database di produzione e di sviluppo con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: cde83a944e1e698a7f8a00c4692c0ce08a87b5ab
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210406"
---
# <a name="applying-migrations"></a>Applicazione di migrazioni

Una volta aggiunte le migrazioni, è necessario distribuirle e applicarle ai database. Per eseguire questa operazione sono disponibili diverse strategie, con alcune più appropriate per gli ambienti di produzione e altre per il ciclo di vita dello sviluppo.

> [!NOTE]
> Indipendentemente dalla strategia di distribuzione, controllare sempre le migrazioni generate e testarle prima di applicare a un database di produzione. Una migrazione può eliminare una colonna quando lo scopo è rinominarla o potrebbe non riuscire per vari motivi quando viene applicata a un database.

## <a name="sql-scripts"></a>Script SQL

La modalità consigliata per distribuire le migrazioni in un database di produzione è la generazione di script SQL. I vantaggi di questa strategia includono i seguenti:

* È possibile esaminare gli script SQL per verificarne l'accuratezza; Questo aspetto è importante perché l'applicazione delle modifiche dello schema ai database di produzione è un'operazione potenzialmente pericolosa che può implicare la perdita di dati.
* In alcuni casi, gli script possono essere ottimizzati per soddisfare le esigenze specifiche di un database di produzione.
* Gli script SQL possono essere usati insieme a una tecnologia di distribuzione e possono anche essere generati come parte del processo CI.
* Gli script SQL possono essere forniti a un amministratore di database e possono essere gestiti e archiviati separatamente.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Utilizzo di base

Il codice seguente genera uno script SQL da un database vuoto alla migrazione più recente:

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Con from (destinazione implicita)

Il codice seguente genera uno script SQL dalla migrazione specificata alla migrazione più recente.

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>Con from e to

Il codice seguente genera uno script SQL dalla `from` migrazione specificata alla migrazione specificata `to` .

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback.

> [!WARNING]
> Tenere conto degli scenari con potenziale perdita di dati.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Utilizzo di base

Il codice seguente genera uno script SQL da un database vuoto alla migrazione più recente:

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Con from (destinazione implicita)

Il codice seguente genera uno script SQL dalla migrazione specificata alla migrazione più recente.

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>Con from e to

Il codice seguente genera uno script SQL dalla `from` migrazione specificata alla migrazione specificata `to` .

```powershell
Script-Migration AddNewTables AddAuditTable
```
È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback. *Tenere conto degli scenari con potenziale perdita di dati.*

***

La generazione di script accetta i due argomenti seguenti in indica l'intervallo di migrazioni da generare:

* La migrazione **di origine** deve essere l'ultima migrazione applicata al database prima dell'esecuzione dello script. Se non è stata applicata alcuna migrazione, specificare `0` (valore predefinito).
* La migrazione **di destinazione** è l'ultima migrazione applicata al database dopo l'esecuzione dello script. L'impostazione predefinita corrisponde all'ultima migrazione nel progetto.

## <a name="idempotent-sql-scripts"></a>Script SQL idempotente

Gli script SQL generati in precedenza possono essere applicati solo per modificare lo schema da una migrazione a un'altra. è responsabilità dell'utente applicare lo script in modo appropriato e solo al database nello stato di migrazione corretto. EF Core supporta anche la generazione di script **idempotente** , che controllano internamente le migrazioni già applicate (tramite la tabella di cronologia delle migrazioni) e applicano solo quelle mancanti. Questa operazione è utile se non si conosce esattamente l'ultima migrazione applicata al database o se si esegue la distribuzione in più database che possono essere in una migrazione diversa.

Il codice seguente genera migrazioni idempotente:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a>Strumenti da riga di comando

Gli strumenti da riga di comando EF possono essere usati per applicare le migrazioni a un database. Sebbene sia produttivo per lo sviluppo e il test locali delle migrazioni, questo approccio non è ideale per la gestione dei database di produzione:

* I comandi SQL vengono applicati direttamente dallo strumento, senza concedere allo sviluppatore la possibilità di ispezionarli o modificarli. Questa situazione può essere pericolosa in un ambiente di produzione.
* .NET SDK e lo strumento EF devono essere installati nei server di produzione.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

Il codice seguente consente di aggiornare il database alla migrazione più recente:

```dotnetcli
dotnet ef database update
```

Il codice seguente consente di aggiornare il database a una determinata migrazione:

```dotnetcli
dotnet ef database update AddNewTables
```

Si noti che questa operazione può essere usata anche per eseguire il rollback a una migrazione precedente.

> [!WARNING]
> Tenere conto degli scenari con potenziale perdita di dati.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Il codice seguente consente di aggiornare il database alla migrazione più recente:

``` powershell
Update-Database
```

Il codice seguente consente di aggiornare il database a una determinata migrazione:

``` powershell
Update-Database AddNewTables
```

Si noti che questa operazione può essere usata anche per eseguire il rollback a una migrazione precedente.

> [!WARNING]
> Tenere conto degli scenari con potenziale perdita di dati.

***

Per ulteriori informazioni sull'applicazione delle migrazioni tramite gli strumenti da riga di comando, vedere la Guida di [riferimento agli strumenti EF Core](xref:core/miscellaneous/cli/index).

## <a name="apply-migrations-at-runtime"></a>Applicare migrazioni al runtime

È possibile che l'applicazione stessa applichi le migrazioni a livello di codice, in genere durante l'avvio. Sebbene sia produttivo per lo sviluppo e il test locali delle migrazioni, questo approccio non è appropriato per la gestione dei database di produzione, per i motivi seguenti:

* Se sono in esecuzione più istanze dell'applicazione, entrambe le applicazioni potrebbero tentare di applicare la migrazione simultaneamente e non riuscire (o peggiori, causare il danneggiamento dei dati).
* Analogamente, se un'applicazione accede al database mentre viene eseguita da un'altra applicazione, ciò può causare gravi problemi.
* Per modificare lo schema del database, l'applicazione deve disporre di accesso con privilegi elevati. È in genere consigliabile limitare le autorizzazioni per il database dell'applicazione in produzione.
* È importante essere in grado di eseguire il rollback di una migrazione applicata in caso di problemi. Le altre strategie forniscono questa operazione in modo semplice e rapido.
* I comandi SQL vengono applicati direttamente dal programma, senza concedere allo sviluppatore la possibilità di ispezionarli o modificarli. Questa situazione può essere pericolosa in un ambiente di produzione.

Per applicare le migrazioni a livello di codice, chiamare `context.Database.Migrate()` . Ad esempio, un'applicazione ASP.NET tipica può eseguire le operazioni seguenti:

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

Si noti che si `Migrate()` basa sul `IMigrator` servizio, che può essere usato per scenari più avanzati. Usare `myDbContext.GetInfrastructure().GetService<IMigrator>()` per accedervi.

> [!WARNING]
>
> * Valutare attentamente prima di usare questo approccio nell'ambiente di produzione. L'esperienza ha dimostrato che la semplicità di questa strategia di distribuzione è stata superata dai problemi che crea. Si consiglia di generare script SQL dalle migrazioni.
> * Non chiamare `EnsureCreated()` prima di `Migrate()`. `EnsureCreated()` ignora la cartella Migrations per creare lo schema, causando un errore di `Migrate()`.
