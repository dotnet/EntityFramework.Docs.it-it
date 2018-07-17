---
title: Provider di Entity Framework - EF6
author: divega
ms.date: 2018-06-27
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 7BFB7763-CD6C-4520-93A2-7B265F5FA586
caps.latest.revision: 3
ms.openlocfilehash: 8bd5a5a420d741accd1167845575e23c09579ae1
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2018
ms.locfileid: "37914297"
---
# <a name="entity-framework-6-providers"></a><span data-ttu-id="1440e-102">Provider di Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="1440e-102">Entity Framework 6 Providers</span></span>
> [!NOTE]
> <span data-ttu-id="1440e-103">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="1440e-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1440e-104">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="1440e-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="1440e-105">Entity Framework viene ora sviluppato in base a una licenza open source e EF6 e versioni successive non faranno parte di .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="1440e-105">The Entity Framework is now being developed under an open-source license and EF6 and above will not be shipped as part of the .NET Framework.</span></span> <span data-ttu-id="1440e-106">Questa novità offre molti vantaggi ma comporta anche la necessità di ricompilare i provider di Entity Framework in base agli assembly di EF6.</span><span class="sxs-lookup"><span data-stu-id="1440e-106">This has many advantages but also requires that EF providers be rebuilt against the EF6 assemblies.</span></span> <span data-ttu-id="1440e-107">I provider di Entity Framework per EF5 e versioni precedenti funzioneranno infatti con EF6 solo dopo che saranno stati ricompilati.</span><span class="sxs-lookup"><span data-stu-id="1440e-107">This means that EF providers for EF5 and below will not work with EF6 until they have been rebuilt.</span></span>

## <a name="which-providers-are-available-for-ef6"></a><span data-ttu-id="1440e-108">Quali provider sono disponibili per EF6?</span><span class="sxs-lookup"><span data-stu-id="1440e-108">Which providers are available for EF6?</span></span>

<span data-ttu-id="1440e-109">In base alle informazioni attuali, i provider ricompilati per EF6 includono:</span><span class="sxs-lookup"><span data-stu-id="1440e-109">Providers we are aware of that have been rebuilt for EF6 include:</span></span>

*   <span data-ttu-id="1440e-110">**Provider Microsoft SQL Server**</span><span class="sxs-lookup"><span data-stu-id="1440e-110">**Microsoft SQL Server provider**</span></span>
    *   <span data-ttu-id="1440e-111">Compilato dalla [codebase open source di Entity Framework](http://github.com/aspnet/EntityFramework6)</span><span class="sxs-lookup"><span data-stu-id="1440e-111">Built from the [Entity Framework open source code base](http://github.com/aspnet/EntityFramework6)</span></span>
    *   <span data-ttu-id="1440e-112">Incluso nel [pacchetto NuGet EntityFramework](http://nuget.org/packages/EntityFramework)</span><span class="sxs-lookup"><span data-stu-id="1440e-112">Shipped as part of the [EntityFramework NuGet package](http://nuget.org/packages/EntityFramework)</span></span>
*   <span data-ttu-id="1440e-113">**Provider Microsoft SQL Server Compact Edition**</span><span class="sxs-lookup"><span data-stu-id="1440e-113">**Microsoft SQL Server Compact Edition provider**</span></span>
    *   <span data-ttu-id="1440e-114">Compilato dalla [codebase open source di Entity Framework](http://github.com/aspnet/EntityFramework6)</span><span class="sxs-lookup"><span data-stu-id="1440e-114">Built from the [Entity Framework open source code base](http://github.com/aspnet/EntityFramework6)</span></span>
    *   <span data-ttu-id="1440e-115">Incluso nel [pacchetto NuGet EntityFramework.SqlServerCompact](http://nuget.org/packages/EntityFramework.SqlServerCompact)</span><span class="sxs-lookup"><span data-stu-id="1440e-115">Shipped in the [EntityFramework.SqlServerCompact NuGet package](http://nuget.org/packages/EntityFramework.SqlServerCompact)</span></span>
*   [<span data-ttu-id="1440e-116">**Provider di dati Devart dotConnect**</span><span class="sxs-lookup"><span data-stu-id="1440e-116">**Devart dotConnect Data Providers**</span></span>](http://www.devart.com/dotconnect/)
    *   <span data-ttu-id="1440e-117">Sono disponibili provider di terze parti di [Devart](http://www.devart.com/) per un'ampia gamma di database tra cui Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2 e SQL Server</span><span class="sxs-lookup"><span data-stu-id="1440e-117">There are third-party providers from [Devart](http://www.devart.com/) for a variety of databases including Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2, and SQL Server</span></span>
*   [<span data-ttu-id="1440e-118">**Provider CData Software**</span><span class="sxs-lookup"><span data-stu-id="1440e-118">**CData Software providers**</span></span>](http://www.cdata.com/ado/)
    *   <span data-ttu-id="1440e-119">Sono disponibili provider di terze parti di [CData Software](http://www.cdata.com/ado/) per un'ampia gamma di archivi dati tra cui Salesforce, Archiviazione tabelle di Azure, MySql e molti altri</span><span class="sxs-lookup"><span data-stu-id="1440e-119">There are third-party providers from [CData Software](http://www.cdata.com/ado/) for a variety of data stores including Salesforce, Azure Table Storage, MySql, and many more</span></span>
*   <span data-ttu-id="1440e-120">**Provider Firebird**</span><span class="sxs-lookup"><span data-stu-id="1440e-120">**Firebird provider**</span></span>
    *   <span data-ttu-id="1440e-121">Disponibile come [pacchetto NuGet](http://www.nuget.org/packages/FirebirdSql.Data.FirebirdClient/)</span><span class="sxs-lookup"><span data-stu-id="1440e-121">Available as a [NuGet Package](http://www.nuget.org/packages/FirebirdSql.Data.FirebirdClient/)</span></span>
*   <span data-ttu-id="1440e-122">**Provider Visual Fox Pro**</span><span class="sxs-lookup"><span data-stu-id="1440e-122">**Visual Fox Pro provider**</span></span>
    *   <span data-ttu-id="1440e-123">Disponibile come [pacchetto NuGet](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)</span><span class="sxs-lookup"><span data-stu-id="1440e-123">Available as a [NuGet package](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)</span></span>
*   <span data-ttu-id="1440e-124">**MySQL**</span><span class="sxs-lookup"><span data-stu-id="1440e-124">**MySQL**</span></span>
    *   [<span data-ttu-id="1440e-125">MySQL Connector/Net</span><span class="sxs-lookup"><span data-stu-id="1440e-125">MySQL Connector/Net</span></span>](http://dev.mysql.com/downloads/connector/net/)
*   <span data-ttu-id="1440e-126">**PostgreSQL**</span><span class="sxs-lookup"><span data-stu-id="1440e-126">**PostgreSQL**</span></span>
    *   <span data-ttu-id="1440e-127">Npgsql è disponibile come [pacchetto NuGet](http://www.nuget.org/packages/Npgsql.EF6/)</span><span class="sxs-lookup"><span data-stu-id="1440e-127">Npgsql is available as a [NuGet package](http://www.nuget.org/packages/Npgsql.EF6/)</span></span>
*   <span data-ttu-id="1440e-128">**Oracle**</span><span class="sxs-lookup"><span data-stu-id="1440e-128">**Oracle**</span></span>
    *   <span data-ttu-id="1440e-129">ODP.NET è disponibile come [pacchetto NuGet](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)</span><span class="sxs-lookup"><span data-stu-id="1440e-129">ODP.NET is available as a [NuGet package](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)</span></span>

<span data-ttu-id="1440e-130">Tenere presente che l'inclusione in questo elenco non indica il livello di funzionalità o supporto per un determinato provider, ma solo che è stata resa disponibile una build per EF6.</span><span class="sxs-lookup"><span data-stu-id="1440e-130">Note that inclusion in this list does not indicate the level of functionality or support for a given provider, only that a build for EF6 has been made available.</span></span>

## <a name="registering-ef-providers"></a><span data-ttu-id="1440e-131">Registrazione dei provider di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="1440e-131">Registering EF providers</span></span>

<span data-ttu-id="1440e-132">A partire da Entity Framework 6, è possibile registrare i provider di Entity Framework usando la configurazione basata su codice o nel file di configurazione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1440e-132">Starting with Entity Framework 6 EF providers can be registered using either code-based configuration or in the application’s config file.</span></span>

### <a name="config-file-registration"></a><span data-ttu-id="1440e-133">Registrazione nel file di configurazione</span><span class="sxs-lookup"><span data-stu-id="1440e-133">Config file registration</span></span>

<span data-ttu-id="1440e-134">La registrazione del provider di Entity Framework in app.config o web.config ha il formato seguente:</span><span class="sxs-lookup"><span data-stu-id="1440e-134">Registration of the EF provider in app.config or web.config has the following format:</span></span>


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

<span data-ttu-id="1440e-135">Tenere presente che se il provider di Entity Framework viene installato da NuGet, il pacchetto NuGet spesso aggiungerà automaticamente la registrazione al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1440e-135">Note that often if the EF provider is installed from NuGet, then the NuGet package will automatically add this registration to the config file.</span></span> <span data-ttu-id="1440e-136">Se il pacchetto NuGet viene installato in un progetto che non è il progetto di avvio per l'app, potrebbe essere necessario copiare la registrazione nel file di configurazione per il progetto di avvio.</span><span class="sxs-lookup"><span data-stu-id="1440e-136">If you install the NuGet package into a project that is not the startup project for your app, then you may need to copy the registration into the config file for your startup project.</span></span>

<span data-ttu-id="1440e-137">"invariantName" in questa registrazione è lo stesso nome invariante usato per identificare un provider ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="1440e-137">The “invariantName” in this registration is the same invariant name used to identify an ADO.NET provider.</span></span> <span data-ttu-id="1440e-138">È indicato come attributo "invariant" in una registrazione DbProviderFactories e come attributo "providerName" nella registrazione di una stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="1440e-138">This can be found as the “invariant” attribute in a DbProviderFactories registration and as the “providerName” attribute in a connection string registration.</span></span> <span data-ttu-id="1440e-139">Il nome invariante da usare deve essere incluso anche nella documentazione del provider.</span><span class="sxs-lookup"><span data-stu-id="1440e-139">The invariant name to use should also be included in documentation for the provider.</span></span> <span data-ttu-id="1440e-140">Esempi di nomi invarianti sono "System.Data.SqlClient" per SQL Server e "System.Data.SqlServerCe.4.0" per SQL Server Compact.</span><span class="sxs-lookup"><span data-stu-id="1440e-140">Examples of invariant names are “System.Data.SqlClient” for SQL Server and “System.Data.SqlServerCe.4.0” for SQL Server Compact.</span></span>

<span data-ttu-id="1440e-141">Il "tipo" in questa registrazione è il nome qualificato dall'assembly del tipo di provider che deriva da "System.Data.Entity.Core.Common.DbProviderServices".</span><span class="sxs-lookup"><span data-stu-id="1440e-141">The “type” in this registration is the assembly-qualified name of the provider type that derives from “System.Data.Entity.Core.Common.DbProviderServices”.</span></span> <span data-ttu-id="1440e-142">La stringa da usare per SQL Compact, ad esempio, è "System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact".</span><span class="sxs-lookup"><span data-stu-id="1440e-142">For example, the string to use for SQL Compact is “System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact”.</span></span> <span data-ttu-id="1440e-143">Il tipo da usare qui deve essere incluso nella documentazione del provider.</span><span class="sxs-lookup"><span data-stu-id="1440e-143">The type to use here should be included in documentation for the provider.</span></span>

### <a name="code-based-registration"></a><span data-ttu-id="1440e-144">Registrazione basata su codice</span><span class="sxs-lookup"><span data-stu-id="1440e-144">Code-based registration</span></span>

<span data-ttu-id="1440e-145">A partire da Entity Framework 6, la configurazione a livello di applicazione per Entity Framework può essere specificata nel codice.</span><span class="sxs-lookup"><span data-stu-id="1440e-145">Starting with Entity Framework 6 application-wide configuration for EF can be specified in code.</span></span> <span data-ttu-id="1440e-146">Per i dettagli completi, vedere _[Entity Framework - configurazione basata su codice](https://msdn.microsoft.com/en-us/data/jj680699)_.</span><span class="sxs-lookup"><span data-stu-id="1440e-146">For full details see _[Entity Framework Code-Based Configuration](https://msdn.microsoft.com/en-us/data/jj680699)_.</span></span> <span data-ttu-id="1440e-147">La normale modalità di registrazione di un provider di Entity Framework tramite la configurazione basata su codice consiste nel creare una nuova classe che deriva da System.Data.Entity.DbConfiguration e inserirla nello stesso assembly della classe DbContext.</span><span class="sxs-lookup"><span data-stu-id="1440e-147">The normal way to register an EF provider using code-based configuration is to create a new class that derives from System.Data.Entity.DbConfiguration and place it in the same assembly as your DbContext class.</span></span> <span data-ttu-id="1440e-148">La classe DbConfiguration registrerà quindi il provider nel proprio costruttore.</span><span class="sxs-lookup"><span data-stu-id="1440e-148">Your DbConfiguration class should then register the provider in its constructor.</span></span> <span data-ttu-id="1440e-149">Ad esempio, per la registrazione del provider SQL Compact, la classe DbConfiguration ha questo aspetto:</span><span class="sxs-lookup"><span data-stu-id="1440e-149">For example, to register the SQL Compact provider the DbConfiguration class looks like this:</span></span>

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

<span data-ttu-id="1440e-150">In questo codice "SqlCeProviderServices.ProviderInvariantName" è un elemento utile per la stringa del nome invariante del provider SQL Server Compact ("System.Data.SqlServerCe.4.0") e SqlCeProviderServices.Instance restituisce l'istanza singleton del provider di Entity Framework per SQL Compact.</span><span class="sxs-lookup"><span data-stu-id="1440e-150">In this code “SqlCeProviderServices.ProviderInvariantName” is a convenience for the SQL Server Compact provider invariant name string (“System.Data.SqlServerCe.4.0”) and SqlCeProviderServices.Instance returns the singleton instance of the SQL Compact EF provider.</span></span>

## <a name="what-if-the-provider-i-need-isnt-available"></a><span data-ttu-id="1440e-151">Cosa si può fare se si ha necessità di un provider che non è disponibile?</span><span class="sxs-lookup"><span data-stu-id="1440e-151">What if the provider I need isn’t available?</span></span>

<span data-ttu-id="1440e-152">Se il provider è disponibile per le versioni precedenti di Entity Framework, contattare il proprietario del provider e chiedere di creare una versione per Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="1440e-152">If the provider is available for previous versions of EF, then we encourage you to contact the owner of the provider and ask them to create an EF6 version.</span></span> <span data-ttu-id="1440e-153">Includere un riferimento alla [documentazione del modello di provider di Entity Framework 6](~/ef6/fundamentals/providers/provider-model.md).</span><span class="sxs-lookup"><span data-stu-id="1440e-153">You should include a reference to the [documentation for the EF6 provider model](~/ef6/fundamentals/providers/provider-model.md).</span></span>

## <a name="can-i-write-a-provider-myself"></a><span data-ttu-id="1440e-154">Si può scrivere autonomamente un provider?</span><span class="sxs-lookup"><span data-stu-id="1440e-154">Can I write a provider myself?</span></span>

<span data-ttu-id="1440e-155">È certamente possibile creare autonomamente un provider di Entity Framework, anche se si tratta di un'operazione tutt'altro che banale.</span><span class="sxs-lookup"><span data-stu-id="1440e-155">It is certainly possible to create an EF provider yourself although it should not be considered a trivial undertaking.</span></span> <span data-ttu-id="1440e-156">Il collegamento sopra riportato relativo al modello di provider di Entity Framework 6 è un buon punto di partenza.</span><span class="sxs-lookup"><span data-stu-id="1440e-156">The the link above about the EF6 provider model is a good place to start.</span></span> <span data-ttu-id="1440e-157">Può anche essere utile usare il codice per il provider SQL Server e SQL CE incluso nella [codebase open source di Entity Framework](https://github.com/aspnet/EntityFramework6) come punto di partenza o come riferimento.</span><span class="sxs-lookup"><span data-stu-id="1440e-157">You may also find it useful to use the code for the SQL Server and SQL CE provider included in the [EF open source codebase](https://github.com/aspnet/EntityFramework6) as a starting point or for reference.</span></span>

<span data-ttu-id="1440e-158">Si noti che a partire da EF6, il collegamento tra il provider di Entity Framework e il provider ADO.NET sottostante è meno rigido.</span><span class="sxs-lookup"><span data-stu-id="1440e-158">Note that starting with EF6 the EF provider is less tightly coupled to the underlying ADO.NET provider.</span></span> <span data-ttu-id="1440e-159">Ciò semplifica la scrittura di un provider di Entity Framework poiché evita la necessità di scrivere le classi ADO.NET o di eseguirne il wrapping.</span><span class="sxs-lookup"><span data-stu-id="1440e-159">This makes it easier to write an EF provider without needing to write or wrap the ADO.NET classes.</span></span>