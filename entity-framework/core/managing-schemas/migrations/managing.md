---
title: Gestione delle migrazioni-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: e52d3680360a1e83e05f04650c735c5a67680094
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238764"
---
# <a name="managing-migrations"></a>Gestione delle migrazioni

Quando il modello viene modificato, le migrazioni vengono aggiunte e rimosse come parte dello sviluppo normale e i file di migrazione vengono archiviati nel controllo del codice sorgente del progetto. Per gestire le migrazioni, è necessario innanzitutto installare gli [strumenti da riga di comando EF Core](xref:core/miscellaneous/cli/index).

> [!TIP]
> Se `DbContext` si trova in un assembly diverso da quello del progetto di avvio, è possibile specificare in modo esplicito i progetti di destinazione e di avvio negli [strumenti della console di Gestione pacchetti](xref:core/miscellaneous/cli/powershell#target-and-startup-project) o negli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

## <a name="add-a-migration"></a>Aggiungere una migrazione

Una volta modificato il modello, è possibile aggiungere una migrazione per la modifica:

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Il nome della migrazione può essere usato come messaggio di commit in un sistema di controllo della versione. Ad esempio, è possibile scegliere un nome come *AddBlogCreatedTimestamp* se la modifica è una nuova `CreatedTimestamp` proprietà nell' `Blog` entità.

Nella directory **Migrations** del progetto vengono aggiunti tre file:

* **XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**: il file principale delle migrazioni. Contiene le operazioni necessarie per applicare la migrazione (in `Up`) e per ripristinarla (in `Down`).
* **XXXXXXXXXXXXXX_AddCreatedTimestamp. designer. cs**, il file di metadati delle migrazioni. Contiene informazioni usate da Entity Framework.
* **MyContextModelSnapshot.cs**: snapshot del modello corrente. Usato per determinare cosa è stato modificato durante l'aggiunta della migrazione successiva.

Il timestamp nel nome file consente di mantenerne l'ordine cronologico e di visualizzare quindi la successione delle modifiche.

### <a name="namespaces"></a>Spazi dei nomi

È consentito spostare i file della cartella Migrations e modificarne lo spazio dei nomi manualmente. Le nuove migrazioni vengono create come migrazioni di pari livello rispetto all'ultima. In alternativa, è possibile specificare lo spazio dei nomi in fase di generazione come indicato di seguito:

### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a>Personalizzare il codice di migrazione

Sebbene EF Core in genere crei migrazioni accurate, è sempre necessario esaminare il codice e verificare che corrisponda alla modifica desiderata; in alcuni casi, è anche necessario eseguire questa operazione.

### <a name="column-renames"></a>Ridenominazione colonne

Un esempio importante in cui è necessario personalizzare le migrazioni è quando si rinomina una proprietà. Se ad esempio si rinomina una proprietà da `Name` a `FullName` , EF Core genererà la seguente migrazione:

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

EF Core in genere non è in grado di stabilire quando si intende eliminare una colonna e crearne una nuova (due modifiche separate) e quando una colonna deve essere rinominata. Se la migrazione precedente viene applicata così come sono, tutti i nomi dei clienti andranno persi. Per rinominare una colonna, sostituire la migrazione generata in precedenza con la seguente:

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> Durante il processo di scaffolding della migrazione viene visualizzato un avviso se un'operazione può causare una perdita di dati, ad esempio nel caso della rimozione di una colonna. Se viene visualizzato questo avviso, assicurarsi in particolare di esaminare il codice delle migrazioni per verificarne l'accuratezza.

### <a name="adding-raw-sql"></a>Aggiunta di SQL non elaborato

Sebbene la ridenominazione di una colonna possa essere eseguita tramite un'API incorporata, in molti casi ciò non è possibile. È ad esempio possibile che si desideri sostituire le `FirstName` proprietà e esistenti `LastColumn` con una singola `FullName` Proprietà nuova. La migrazione generata da EF Core sarà la seguente:

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

Come in precedenza, questo potrebbe causare una perdita di dati indesiderata. Per trasferire i dati dalle colonne precedenti, è necessario ridisporre le migrazioni e introdurre un'operazione SQL non elaborata come indicato di seguito:

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

### <a name="arbitrary-changes-via-raw-sql"></a>Modifiche arbitrarie tramite SQL non elaborato

SQL non elaborato può essere utilizzato anche per gestire gli oggetti di database che EF Core non è in grado di riconoscere. A tale scopo, aggiungere una migrazione senza apportare modifiche al modello; verrà generata una migrazione vuota, che potrà quindi essere popolata con operazioni SQL non elaborate.

Ad esempio, la seguente migrazione crea una SQL Server stored procedure:

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

Questa operazione può essere utilizzata per gestire qualsiasi aspetto del database, tra cui:

* Stored procedure
* Ricerca full-text
* Funzioni
* Trigger
* Visualizzazioni

Nella maggior parte dei casi, EF Core effettuerà automaticamente il wrapping di ogni migrazione nella propria transazione durante l'applicazione delle migrazioni. Sfortunatamente, alcune operazioni di migrazione non possono essere eseguite all'interno di una transazione in alcuni database. in questi casi, è possibile rifiutare esplicitamente la transazione passando `suppressTransaction: true` a `migrationBuilder.Sql` .

Se `DbContext` si trova in un assembly diverso da quello del progetto di avvio, è possibile specificare in modo esplicito i progetti di destinazione e di avvio negli [strumenti della console di Gestione pacchetti](xref:core/miscellaneous/cli/powershell#target-and-startup-project) o negli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).

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

> [!WARNING]
> Prestare attenzione a non rimuovere le migrazioni già applicate ai database di produzione. In caso contrario, non sarà possibile ripristinarlo e potrebbe interrompere le ipotesi effettuate dalle successive migrazioni.

## <a name="listing-migrations"></a>Elenco delle migrazioni

È possibile elencare tutte le migrazioni esistenti nel modo seguente:

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a>Reimpostazione di tutte le migrazioni

In alcuni casi estremi, potrebbe essere necessario rimuovere tutte le migrazioni e ricominciare. Questa operazione può essere eseguita facilmente eliminando la cartella **migrazioni** ed eliminando il database. a questo punto è possibile creare una nuova migrazione iniziale, che conterrà l'intero schema corrente.

È anche possibile reimpostare tutte le migrazioni e crearne una singola senza perdere i dati. Questa operazione viene a volte definita "squashing" e implica alcune operazioni manuali:

* Elimina la cartella **migrazioni**
* Creazione di una nuova migrazione e generazione di uno script SQL
* Nel database eliminare tutte le righe dalla tabella di cronologia migrazioni
* Inserire una singola riga nella cronologia delle migrazioni per registrare che la prima migrazione è già stata applicata, perché le tabelle sono già presenti. Insert SEQL è l'ultima operazione nello script SQL generato in precedenza.
