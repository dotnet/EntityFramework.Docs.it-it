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
# <a name="custom-migrations-operations"></a><span data-ttu-id="479ee-103">Operazioni di migrazione personalizzate</span><span class="sxs-lookup"><span data-stu-id="479ee-103">Custom Migrations Operations</span></span>

<span data-ttu-id="479ee-104">L'API MigrationBuilder consente di eseguire molti tipi diversi di operazioni durante una migrazione, ma è molto più esaustiva.</span><span class="sxs-lookup"><span data-stu-id="479ee-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="479ee-105">Tuttavia, l'API è anche estendibile e consente di definire le proprie operazioni.</span><span class="sxs-lookup"><span data-stu-id="479ee-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="479ee-106">Esistono due modi per estendere l'API: usando il `Sql()` metodo o definendo `MigrationOperation` oggetti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="479ee-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="479ee-107">Per illustrare l'implementazione di un'operazione che consente di creare un utente del database utilizzando ogni approccio.</span><span class="sxs-lookup"><span data-stu-id="479ee-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="479ee-108">Nelle migrazioni è necessario abilitare la scrittura del codice seguente:</span><span class="sxs-lookup"><span data-stu-id="479ee-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="479ee-109">Utilizzo di MigrationBuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="479ee-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="479ee-110">Il modo più semplice per implementare un'operazione personalizzata consiste nel definire un metodo di estensione che chiama `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="479ee-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="479ee-111">Di seguito è riportato un esempio che genera l'oggetto Transact-SQL appropriato.</span><span class="sxs-lookup"><span data-stu-id="479ee-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

```csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="479ee-112">Se le migrazioni devono supportare più provider di database, è possibile usare la `MigrationBuilder.ActiveProvider` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="479ee-112">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="479ee-113">Di seguito è riportato un esempio che supporta sia Microsoft SQL Server che PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="479ee-113">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

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

<span data-ttu-id="479ee-114">Questo approccio funziona solo se si conoscono tutti i provider in cui verrà applicata l'operazione personalizzata.</span><span class="sxs-lookup"><span data-stu-id="479ee-114">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="479ee-115">Uso di un MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="479ee-115">Using a MigrationOperation</span></span>

<span data-ttu-id="479ee-116">Per separare l'operazione personalizzata da SQL, è possibile definirne una personalizzata `MigrationOperation` per rappresentarla.</span><span class="sxs-lookup"><span data-stu-id="479ee-116">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="479ee-117">L'operazione viene quindi passata al provider in modo da poter determinare il SQL appropriato da generare.</span><span class="sxs-lookup"><span data-stu-id="479ee-117">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

```csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="479ee-118">Con questo approccio, il metodo di estensione deve semplicemente aggiungere una di queste operazioni a `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="479ee-118">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

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

<span data-ttu-id="479ee-119">Questo approccio richiede che ogni provider sappia come generare SQL per questa operazione nel `IMigrationsSqlGenerator` servizio.</span><span class="sxs-lookup"><span data-stu-id="479ee-119">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="479ee-120">Di seguito è riportato un esempio che esegue l'override del generatore del SQL Server per gestire la nuova operazione.</span><span class="sxs-lookup"><span data-stu-id="479ee-120">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

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

<span data-ttu-id="479ee-121">Sostituire il servizio Generatore SQL per migrazioni predefinite con quello aggiornato.</span><span class="sxs-lookup"><span data-stu-id="479ee-121">Replace the default migrations sql generator service with the updated one.</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
