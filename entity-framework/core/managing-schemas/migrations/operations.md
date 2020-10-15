---
title: Operazioni di migrazione personalizzate-EF Core
description: Gestione di migrazioni SQL personalizzate e non elaborate per la gestione dello schema del database con Entity Framework Core
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: d1d29b7789eea5e887490364a7ce3abfdc903545
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062035"
---
# <a name="custom-migrations-operations"></a>Operazioni di migrazione personalizzate

L'API MigrationBuilder consente di eseguire molti tipi diversi di operazioni durante una migrazione, ma è molto più esaustiva. Tuttavia, l'API è anche estendibile e consente di definire le proprie operazioni. Esistono due modi per estendere l'API: usando il `Sql()` metodo o definendo `MigrationOperation` oggetti personalizzati.

Per illustrare l'implementazione di un'operazione che consente di creare un utente del database utilizzando ogni approccio. Nelle migrazioni è necessario abilitare la scrittura del codice seguente:

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Utilizzo di MigrationBuilder. SQL ()

Il modo più semplice per implementare un'operazione personalizzata consiste nel definire un metodo di estensione che chiama `MigrationBuilder.Sql()` . Di seguito è riportato un esempio che genera l'oggetto Transact-SQL appropriato.

```csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

Se le migrazioni devono supportare più provider di database, è possibile usare la `MigrationBuilder.ActiveProvider` Proprietà. Di seguito è riportato un esempio che supporta sia Microsoft SQL Server che PostgreSQL.

```csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

Questo approccio funziona solo se si conoscono tutti i provider in cui verrà applicata l'operazione personalizzata.

## <a name="using-a-migrationoperation"></a>Uso di un MigrationOperation

Per separare l'operazione personalizzata da SQL, è possibile definirne una personalizzata `MigrationOperation` per rappresentarla. L'operazione viene quindi passata al provider in modo da poter determinare il SQL appropriato da generare.

```csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

Con questo approccio, il metodo di estensione deve semplicemente aggiungere una di queste operazioni a `MigrationBuilder.Operations` .

```csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

Questo approccio richiede che ogni provider sappia come generare SQL per questa operazione nel `IMigrationsSqlGenerator` servizio. Di seguito è riportato un esempio che esegue l'override del generatore del SQL Server per gestire la nuova operazione.

```csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

Sostituire il servizio Generatore SQL per migrazioni predefinite con quello aggiornato.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
