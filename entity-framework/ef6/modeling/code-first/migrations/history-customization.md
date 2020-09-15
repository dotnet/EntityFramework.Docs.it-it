---
title: Personalizzazione della tabella di cronologia delle migrazioni-EF6
description: Personalizzazione della tabella di cronologia delle migrazioni in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: b71ac7635d6804af2db9bf00acf7e8f4b2a11033
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073461"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="a92e9-103">Personalizzazione della tabella di cronologia delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="a92e9-103">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="a92e9-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="a92e9-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a92e9-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="a92e9-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="a92e9-106">Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base.</span><span class="sxs-lookup"><span data-stu-id="a92e9-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="a92e9-107">In caso contrario, sarà necessario leggere [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="a92e9-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="a92e9-108">Che cos'è la tabella di cronologia delle migrazioni?</span><span class="sxs-lookup"><span data-stu-id="a92e9-108">What is Migrations History Table?</span></span>

<span data-ttu-id="a92e9-109">Tabella di cronologia migrazioni è una tabella utilizzata da Migrazioni Code First per archiviare i dettagli sulle migrazioni applicate al database.</span><span class="sxs-lookup"><span data-stu-id="a92e9-109">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="a92e9-110">Per impostazione predefinita, il nome della tabella nel database è \_ \_ MigrationHistory e viene creato quando si applica la prima migrazione al database.</span><span class="sxs-lookup"><span data-stu-id="a92e9-110">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="a92e9-111">In Entity Framework 5 questa tabella è una tabella di sistema se l'applicazione utilizza il database di Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a92e9-111">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="a92e9-112">Questa operazione è stata modificata in Entity Framework 6, ma la tabella di cronologia delle migrazioni non è più contrassegnata come tabella di sistema.</span><span class="sxs-lookup"><span data-stu-id="a92e9-112">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="a92e9-113">Perché personalizzare la tabella di cronologia delle migrazioni?</span><span class="sxs-lookup"><span data-stu-id="a92e9-113">Why customize Migrations History Table?</span></span>

<span data-ttu-id="a92e9-114">La tabella di cronologia delle migrazioni dovrebbe essere utilizzata esclusivamente da Migrazioni Code First e modificarla manualmente può suddividere le migrazioni.</span><span class="sxs-lookup"><span data-stu-id="a92e9-114">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="a92e9-115">Tuttavia, a volte la configurazione predefinita non è adatta e la tabella deve essere personalizzata, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="a92e9-115">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="a92e9-116">Per abilitare un provider di migrazioni<sup>di terze</sup> parti, è necessario modificare i nomi e/o i facet delle colonne</span><span class="sxs-lookup"><span data-stu-id="a92e9-116">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="a92e9-117">Si desidera modificare il nome della tabella</span><span class="sxs-lookup"><span data-stu-id="a92e9-117">You want to change the name of the table</span></span>
-   <span data-ttu-id="a92e9-118">È necessario usare uno schema non predefinito per la \_ \_ tabella MigrationHistory</span><span class="sxs-lookup"><span data-stu-id="a92e9-118">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="a92e9-119">È necessario archiviare dati aggiuntivi per una determinata versione del contesto e pertanto è necessario aggiungere una colonna aggiuntiva alla tabella.</span><span class="sxs-lookup"><span data-stu-id="a92e9-119">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="a92e9-120">Parole di precauzione</span><span class="sxs-lookup"><span data-stu-id="a92e9-120">Words of precaution</span></span>

<span data-ttu-id="a92e9-121">Modificare la tabella di cronologia della migrazione è potente, ma è necessario prestare attenzione a non esagerare.</span><span class="sxs-lookup"><span data-stu-id="a92e9-121">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="a92e9-122">EF Runtime attualmente non controlla se la tabella di cronologia delle migrazioni personalizzate è compatibile con il Runtime.</span><span class="sxs-lookup"><span data-stu-id="a92e9-122">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="a92e9-123">In caso contrario, l'applicazione potrebbe interrompersi in fase di esecuzione o comportarsi in modi imprevedibili.</span><span class="sxs-lookup"><span data-stu-id="a92e9-123">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="a92e9-124">Questa operazione è ancora più importante se si usano più contesti per database, nel qual caso più contesti possono usare la stessa tabella di cronologia della migrazione per archiviare le informazioni sulle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="a92e9-124">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="a92e9-125">Come personalizzare la tabella di cronologia delle migrazioni</span><span class="sxs-lookup"><span data-stu-id="a92e9-125">How to customize Migrations History Table?</span></span>

<span data-ttu-id="a92e9-126">Prima di iniziare, è necessario tenere presente che è possibile personalizzare la tabella di cronologia delle migrazioni solo prima di applicare la prima migrazione.</span><span class="sxs-lookup"><span data-stu-id="a92e9-126">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="a92e9-127">A questo punto, al codice.</span><span class="sxs-lookup"><span data-stu-id="a92e9-127">Now, to the code.</span></span>

<span data-ttu-id="a92e9-128">In primo luogo, sarà necessario creare una classe derivata dalla classe System. Data. Entity. Migrations. History. HistoryContext.</span><span class="sxs-lookup"><span data-stu-id="a92e9-128">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="a92e9-129">La classe HistoryContext è derivata dalla classe DbContext, quindi la configurazione della tabella di cronologia delle migrazioni è molto simile alla configurazione dei modelli EF con l'API Fluent.</span><span class="sxs-lookup"><span data-stu-id="a92e9-129">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="a92e9-130">È sufficiente eseguire l'override del metodo OnModelCreating e usare l'API Fluent per configurare la tabella.</span><span class="sxs-lookup"><span data-stu-id="a92e9-130">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="a92e9-131">In genere, quando si configurano i modelli EF non è necessario chiamare base. OnModelCreating () dal metodo OnModelCreating sottoposto a override poiché DbContext. OnModelCreating () ha un corpo vuoto.</span><span class="sxs-lookup"><span data-stu-id="a92e9-131">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="a92e9-132">Questa situazione non si verifica quando si configura la tabella di cronologia delle migrazioni.</span><span class="sxs-lookup"><span data-stu-id="a92e9-132">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="a92e9-133">In questo caso, la prima operazione da eseguire nell'override di OnModelCreating () consiste nell'eseguire effettivamente la chiamata di base. OnModelCreating ().</span><span class="sxs-lookup"><span data-stu-id="a92e9-133">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="a92e9-134">Verrà configurata la tabella di cronologia delle migrazioni con la modalità predefinita, che verrà quindi modificata nel metodo di override.</span><span class="sxs-lookup"><span data-stu-id="a92e9-134">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="a92e9-135">Si noti che si vuole rinominare la tabella di cronologia delle migrazioni e inserirla in uno schema personalizzato denominato "admin".</span><span class="sxs-lookup"><span data-stu-id="a92e9-135">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="a92e9-136">Inoltre, l'amministratore di database desidera rinominare la colonna MigrationId in ID migrazione \_ .</span><span class="sxs-lookup"><span data-stu-id="a92e9-136">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="a92e9-137">È possibile ottenere questo risultato creando la classe seguente derivata da HistoryContext:</span><span class="sxs-lookup"><span data-stu-id="a92e9-137"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

<span data-ttu-id="a92e9-138">Quando il HistoryContext personalizzato è pronto, è necessario fare in modo che EF lo renda conto mediante la registrazione tramite la [configurazione basata su codice](https://msdn.com/data/jj680699):</span><span class="sxs-lookup"><span data-stu-id="a92e9-138">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

<span data-ttu-id="a92e9-139">Questo è molto simile.</span><span class="sxs-lookup"><span data-stu-id="a92e9-139">That’s pretty much it.</span></span> <span data-ttu-id="a92e9-140">A questo punto è possibile passare alla console di gestione pacchetti, abilitare-Migrations, Add-Migration e infine Update-database.</span><span class="sxs-lookup"><span data-stu-id="a92e9-140">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="a92e9-141">Ciò comporta l'aggiunta al database di una tabella di cronologia delle migrazioni configurata in base ai dettagli specificati nella classe derivata HistoryContext.</span><span class="sxs-lookup"><span data-stu-id="a92e9-141">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![Tabella di cronologia migrazioni](~/ef6/media/database.png)
