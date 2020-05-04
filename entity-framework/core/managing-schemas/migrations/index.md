---
title: Migrazioni - Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/05/2018
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 99bb420d95cb86443b63ba05ce9e6b4ab838eff9
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538448"
---
# <a name="migrations"></a>Migrazioni

In fase di sviluppo un modello di dati viene modificato e non rimane sincronizzato con il database. È possibile eliminare il database e consentire a EF di crearne uno nuovo che corrisponda al modello, ma questa procedura comporta la perdita dei dati. La funzionalità delle migrazioni in EF Core rappresenta un metodo per l'aggiornamento incrementale dello schema del database per mantenere quest'ultimo sincronizzato con il modello di dati dell'applicazione mantenendo i dati esistenti nel database.

Le migrazioni includono strumenti da riga di comando e API che consentono le attività seguenti:

* [Creare una migrazione](#create-a-migration). Generare codice in grado di aggiornare il database in modo da sincronizzarlo con un set di modifiche al modello.
* [Aggiornare il database](#update-the-database). Applicare migrazioni in sospeso per aggiornare lo schema del database.
* [Personalizzare il codice di migrazione](#customize-migration-code). A volte il codice generato deve essere modificato o integrato.
* [Rimuovere una migrazione](#remove-a-migration). Eliminare il codice generato.
* [Ripristinare una migrazione](#revert-a-migration). Annullare le modifiche al database.
* [Generare script SQL](#generate-sql-scripts). Potrebbe essere necessario uno script per aggiornare un database di produzione o per risolvere i problemi del codice di migrazione.
* [Applicare migrazioni al runtime](#apply-migrations-at-runtime). Quando gli aggiornamenti in fase di progettazione e l'esecuzione di script non sono le opzioni più appropriate, chiamare il metodo `Migrate()`.

> [!TIP]
> Se `DbContext` si trova in un assembly diverso da quello del progetto di avvio, è possibile specificare in modo esplicito i progetti di destinazione e di avvio negli [strumenti della console di Gestione pacchetti](xref:core/miscellaneous/cli/powershell#target-and-startup-project) o negli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

## <a name="install-the-tools"></a>Installare gli strumenti

Installare gli [strumenti da riga di comando](xref:core/miscellaneous/cli/index):

* Per Visual Studio, sono consigliati gli [strumenti della console di Gestione pacchetti](xref:core/miscellaneous/cli/powershell).
* Per gli altri ambienti di sviluppo, scegliere gli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet).

## <a name="create-a-migration"></a>Creare una migrazione

Dopo la [definizione del modello iniziale](xref:core/modeling/index) è il momento di creare il database. Per aggiungere una migrazione iniziale, eseguire il comando seguente.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

Nella directory **Migrations** del progetto vengono aggiunti tre file:

* **XXXXXXXXXXXXXX_InitialCreate.cs**: file principale della migrazione. Contiene le operazioni necessarie per applicare la migrazione (in `Up()`) e per ripristinarla (in `Down()`).
* **XXXXXXXXXXXXXX_InitialCreate.Designer.cs**: file di metadati della migrazione. Contiene informazioni usate da Entity Framework.
* **MyContextModelSnapshot.cs**: snapshot del modello corrente. Usato per determinare cosa è stato modificato durante l'aggiunta della migrazione successiva.

Il timestamp nel nome file consente di mantenerne l'ordine cronologico e di visualizzare quindi la successione delle modifiche.

> [!TIP]
> È consentito spostare i file della cartella Migrations e modificarne lo spazio dei nomi manualmente. Le nuove migrazioni vengono create come migrazioni di pari livello rispetto all'ultima.
> 
> In alternativa, è possibile usare `-Namespace` (console di gestione pacchetti) o `--namespace` (interfaccia della riga di comando di .NET Core) per specificare lo spazio dei nomi in fase di generazione.
> ### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)
> 
> ```dotnetcli
> dotnet ef migrations add InitialCreate --namespace Your.Namespace
> ```
> 
> ### <a name="visual-studio"></a>[Visual Studio](#tab/vs)
> 
> ``` powershell
> Add-Migration InitialCreate -Namespace Your.Namespace
> ```

## <a name="update-the-database"></a>Aggiornare il database

Applicare quindi la migrazione al database per creare lo schema.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

## <a name="customize-migration-code"></a>Personalizzare il codice di migrazione

Dopo le modifiche al modello di EF Core, lo schema del database potrebbe non essere sincronizzato. Per aggiornarlo, aggiungere un'altra migrazione. Il nome della migrazione può essere usato come messaggio di commit in un sistema di controllo della versione. Ad esempio, è possibile scegliere un nome come *AddProductReviews* se la modifica è una nuova classe di entità per le revisioni.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddProductReviews
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddProductReviews
```

***

Dopo che la migrazione è stata creata tramite scaffolding ed è stato generato il codice, esaminare tale codice per verificarne l'accuratezza e aggiungere, rimuovere o modificare eventuali operazioni necessarie a garantirne una corretta applicazione.

Una migrazione, ad esempio, può contenere le operazioni seguenti:

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

Queste operazioni rendono compatibile lo schema del database, ma non consentono di mantenere i nomi dei clienti esistenti. Per migliorarle, riscriverle come segue.

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

> [!TIP]
> Durante il processo di scaffolding della migrazione viene visualizzato un avviso se un'operazione può causare una perdita di dati, ad esempio nel caso della rimozione di una colonna. Se viene visualizzato questo avviso, assicurarsi in particolare di esaminare il codice delle migrazioni per verificarne l'accuratezza.

Applicare la migrazione al database tramite il comando appropriato.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

### <a name="empty-migrations"></a>Migrazioni vuote

È talvolta utile aggiungere una migrazione senza apportare alcuna modifica al modello. In questo caso, l'aggiunta di una nuova migrazione crea file di codice con classi vuote. È possibile personalizzare questa migrazione per eseguire operazioni non direttamente correlate al modello di Entity Framework Core. Ecco alcune operazioni che è necessario gestire in questo modo:

* Ricerca full-text
* Funzioni
* Stored procedure
* Trigger
* Visualizzazioni

## <a name="remove-a-migration"></a>Rimuovere una migrazione

Dopo l'aggiunta di una migrazione ci si rende talvolta conto che prima di applicarla sono necessarie altre modifiche al modello di Entity Framework Core. Per rimuovere l'ultima migrazione, usare questo comando.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Remove-Migration
```

***

Dopo la rimozione della migrazione, è possibile apportare le modifiche aggiuntive al modello. La migrazione può quindi essere aggiunta di nuovo.

## <a name="revert-a-migration"></a>Ripristinare una migrazione

Se una o più migrazioni sono già state applicate ma è necessario ripristinarle, è possibile usare lo stesso comando impiegato per applicarle, specificando però il nome della migrazione a cui si vuole eseguire il rollback.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update LastGoodMigration
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database LastGoodMigration
```

***

## <a name="generate-sql-scripts"></a>Generare script SQL

Per il debug delle migrazioni o la distribuzione di queste in un database di produzione è utile generare uno script SQL. Lo script può quindi essere rivisto per garantirne la correttezza e ottimizzato in base alle esigenze del database di produzione. È anche possibile combinare lo script con una tecnologia di distribuzione. Il comando di base è il seguente.

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Utilizzo di base
```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Con from (destinazione implicita)
Verrà generato uno script SQL da questa migrazione alla migrazione più recente.
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>Con from e to
Verrà generato uno script SQL dalla migrazione `from` alla migrazione `to` specificata.
```dotnetcli
dotnet ef migrations script 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback. *Tenere conto degli scenari con potenziale perdita di dati.*

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Utilizzo di base
``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Con from (destinazione implicita)
Verrà generato uno script SQL da questa migrazione alla migrazione più recente.
```powershell
Script-Migration 20190725054716_Add_new_tables
```

#### <a name="with-from-and-to"></a>Con from e to
Verrà generato uno script SQL dalla migrazione `from` alla migrazione `to` specificata.
```powershell
Script-Migration 20190725054716_Add_new_tables 20190829031257_Add_audit_table
```
È possibile usare un `from` più recente rispetto a `to` per generare uno script di rollback. *Tenere conto degli scenari con potenziale perdita di dati.*

***

Con questo comando è possibile usare diverse opzioni.

La migrazione **di origine** deve essere l'ultima migrazione applicata al database prima dell'esecuzione dello script. Se non è stata applicata alcuna migrazione, specificare `0` (valore predefinito).

La migrazione **di destinazione** è l'ultima migrazione applicata al database dopo l'esecuzione dello script. L'impostazione predefinita corrisponde all'ultima migrazione nel progetto.

Facoltativamente, è possibile generare uno script **idempotente**. Questo tipo di script applica le migrazioni solo se non sono già state applicate al database ed è utile se non si sa esattamente quale migrazione è stata applicata per ultima al database o se si stanno applicando migrazioni a più database a cui in precedenza sono state applicate migrazioni diverse.

## <a name="apply-migrations-at-runtime"></a>Applicare migrazioni al runtime

Alcune applicazioni devono applicare migrazioni in runtime durante l'avvio o la prima esecuzione. In questo caso usare il metodo `Migrate()`.

Questo metodo si basa sul servizio `IMigrator`, che può essere usato per scenari più avanzati. Usare `myDbContext.GetInfrastructure().GetService<IMigrator>()` per accedervi.

``` csharp
myDbContext.Database.Migrate();
```

> [!WARNING]
>
> * Questo approccio non è adatto a tutte le situazioni. È utile per applicazioni con un database locale, ma la maggior parte delle applicazioni richiede una strategia di distribuzione più solida, ad esempio la generazione di script SQL.
> * Non chiamare `EnsureCreated()` prima di `Migrate()`. `EnsureCreated()` ignora la cartella Migrations per creare lo schema, causando un errore di `Migrate()`.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere <xref:core/miscellaneous/cli/index>.
