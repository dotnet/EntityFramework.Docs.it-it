---
title: Risoluzione delle dipendenze-EF6
description: Risoluzione delle dipendenze in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 84f2391cdad974bb14aecd7e8650d46e78e47b75
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063309"
---
# <a name="dependency-resolution"></a><span data-ttu-id="fef6f-103">Risoluzione delle dipendenze</span><span class="sxs-lookup"><span data-stu-id="fef6f-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="fef6f-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="fef6f-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="fef6f-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="fef6f-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="fef6f-106">A partire da EF6, Entity Framework contiene un meccanismo di utilizzo generico per ottenere le implementazioni dei servizi richiesti.</span><span class="sxs-lookup"><span data-stu-id="fef6f-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="fef6f-107">Ovvero, quando Entity Framework usa un'istanza di alcune interfacce o classi di base, richiede un'implementazione concreta dell'interfaccia o della classe di base da usare.</span><span class="sxs-lookup"><span data-stu-id="fef6f-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="fef6f-108">Questa operazione viene eseguita usando l'interfaccia IDbDependencyResolver:</span><span class="sxs-lookup"><span data-stu-id="fef6f-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="fef6f-109">Il metodo GetService viene in genere chiamato da EF ed è gestito da un'implementazione di IDbDependencyResolver fornita da EF o dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="fef6f-110">Quando viene chiamato, l'argomento di tipo è l'interfaccia o il tipo di classe di base del servizio richiesto e l'oggetto chiave è null o un oggetto che fornisce informazioni contestuali sul servizio richiesto.</span><span class="sxs-lookup"><span data-stu-id="fef6f-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="fef6f-111">Se non diversamente specificato, qualsiasi oggetto restituito deve essere thread-safe poiché può essere usato come singleton.</span><span class="sxs-lookup"><span data-stu-id="fef6f-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="fef6f-112">In molti casi l'oggetto restituito è una factory, nel qual caso la factory deve essere thread-safe, ma non è necessario che l'oggetto restituito dalla factory sia thread-safe perché per ogni uso viene richiesta una nuova istanza dalla factory.</span><span class="sxs-lookup"><span data-stu-id="fef6f-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="fef6f-113">Questo articolo non contiene informazioni dettagliate su come implementare IDbDependencyResolver, ma funge da riferimento per i tipi di servizio (ovvero i tipi di interfaccia e di classe di base) per i quali EF chiama GetService e la semantica dell'oggetto chiave per ognuna di queste chiamate.</span><span class="sxs-lookup"><span data-stu-id="fef6f-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="fef6f-114">System. Data. Entity. IDatabaseInitializer<TContext\></span><span class="sxs-lookup"><span data-stu-id="fef6f-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="fef6f-115">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-116">**Oggetto restituito**: inizializzatore di database per il tipo di contesto specificato</span><span class="sxs-lookup"><span data-stu-id="fef6f-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="fef6f-117">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="fef6f-118">Func<System. Data. Entity. Migrations. SQL. MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="fef6f-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="fef6f-119">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="fef6f-120">**Oggetto restituito**: una factory per creare un generatore SQL che può essere usato per le migrazioni e altre azioni che comportano la creazione di un database, ad esempio la creazione di un database con inizializzatori di database.</span><span class="sxs-lookup"><span data-stu-id="fef6f-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="fef6f-121">**Key**: stringa che contiene il nome invariante del provider ADO.NET che specifica il tipo di database per cui verrà generato SQL.</span><span class="sxs-lookup"><span data-stu-id="fef6f-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="fef6f-122">Ad esempio, viene restituito il generatore di SQL Server SQL per la chiave "System. Data. SqlClient".</span><span class="sxs-lookup"><span data-stu-id="fef6f-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-123">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="fef6f-124">System. Data. Entity. Core. Common. DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="fef6f-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="fef6f-125">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-126">**Oggetto restituito**: il provider EF da usare per un nome invariante del provider specificato</span><span class="sxs-lookup"><span data-stu-id="fef6f-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="fef6f-127">**Key**: stringa che contiene il nome invariante del provider ADO.NET che specifica il tipo di database per cui è necessario un provider.</span><span class="sxs-lookup"><span data-stu-id="fef6f-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="fef6f-128">Il provider SQL Server, ad esempio, viene restituito per la chiave "System. Data. SqlClient".</span><span class="sxs-lookup"><span data-stu-id="fef6f-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-129">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="fef6f-130">System. Data. Entity. Infrastructure. IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="fef6f-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="fef6f-131">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-132">**Oggetto restituito**: la factory di connessione che verrà usata quando EF crea una connessione di database per convenzione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="fef6f-133">Ovvero, quando nessuna connessione o stringa di connessione viene assegnata a EF e non è possibile trovare una stringa di connessione nella app.config o web.config, questo servizio viene utilizzato per creare una connessione per convenzione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="fef6f-134">La modifica della factory di connessione può consentire a EF di usare un tipo diverso di database, ad esempio SQL Server Compact Edition, per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="fef6f-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="fef6f-135">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-136">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="fef6f-137">System. Data. Entity. Infrastructure. IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="fef6f-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="fef6f-138">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-139">**Oggetto restituito**: un servizio che può generare un token del manifesto del provider da una connessione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="fef6f-140">Questo servizio viene in genere usato in due modi.</span><span class="sxs-lookup"><span data-stu-id="fef6f-140">This service is typically used in two ways.</span></span> <span data-ttu-id="fef6f-141">In primo luogo, può essere utilizzato per evitare Code First la connessione al database durante la compilazione di un modello.</span><span class="sxs-lookup"><span data-stu-id="fef6f-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="fef6f-142">In secondo luogo, può essere utilizzato per forzare Code First a compilare un modello per una versione specifica del database, ad esempio per forzare un modello per SQL Server 2005 anche se talvolta viene utilizzato SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="fef6f-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="fef6f-143">**Durata degli oggetti**: Singleton: lo stesso oggetto può essere usato più volte e simultaneamente da thread diversi</span><span class="sxs-lookup"><span data-stu-id="fef6f-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="fef6f-144">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="fef6f-145">System. Data. Entity. Infrastructure. IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="fef6f-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="fef6f-146">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-147">**Oggetto restituito**: un servizio che può ottenere una factory del provider da una connessione specificata.</span><span class="sxs-lookup"><span data-stu-id="fef6f-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="fef6f-148">In .NET 4,5 il provider è accessibile pubblicamente dalla connessione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="fef6f-149">In .NET 4, l'implementazione predefinita di questo servizio usa alcune euristiche per trovare il provider corrispondente.</span><span class="sxs-lookup"><span data-stu-id="fef6f-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="fef6f-150">Se questi errori hanno esito negativo, è possibile registrare una nuova implementazione di questo servizio per fornire una soluzione appropriata.</span><span class="sxs-lookup"><span data-stu-id="fef6f-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="fef6f-151">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="fef6f-152">Func<DbContext, System. Data. Entity. Infrastructure. IDbModelCacheKey\></span><span class="sxs-lookup"><span data-stu-id="fef6f-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="fef6f-153">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-154">**Oggetto restituito**: Factory che genererà una chiave di cache del modello per un determinato contesto.</span><span class="sxs-lookup"><span data-stu-id="fef6f-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="fef6f-155">Per impostazione predefinita, EF memorizza nella cache un modello per ogni tipo di DbContext per provider.</span><span class="sxs-lookup"><span data-stu-id="fef6f-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="fef6f-156">Per aggiungere altre informazioni, ad esempio nome schema, alla chiave della cache, è possibile usare un'implementazione diversa di questo servizio.</span><span class="sxs-lookup"><span data-stu-id="fef6f-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="fef6f-157">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="fef6f-158">System. Data. Entity. Spatial. DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="fef6f-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="fef6f-159">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-160">**Oggetto restituito**: provider spaziale EF che aggiunge il supporto al provider EF di base per i tipi spaziali geography e Geometry.</span><span class="sxs-lookup"><span data-stu-id="fef6f-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="fef6f-161">**Chiave**: DbSptialServices viene richiesta in due modi.</span><span class="sxs-lookup"><span data-stu-id="fef6f-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="fef6f-162">Per prima cosa, i servizi spaziali specifici del provider vengono richiesti usando un oggetto DbProviderInfo, che contiene il nome invariante e il token del manifesto, come chiave.</span><span class="sxs-lookup"><span data-stu-id="fef6f-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="fef6f-163">In secondo luogo, è possibile chiedere a DbSpatialServices senza chiave.</span><span class="sxs-lookup"><span data-stu-id="fef6f-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="fef6f-164">Viene usato per risolvere il "provider spaziale globale" usato per la creazione di tipi DbGeography o DbGeometry autonomi.</span><span class="sxs-lookup"><span data-stu-id="fef6f-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-165">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="fef6f-166">Func<System. Data. Entity. Infrastructure. IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="fef6f-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="fef6f-167">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-168">**Oggetto restituito**: una factory per creare un servizio che consente a un provider di implementare nuovi tentativi o un altro comportamento quando le query e i comandi vengono eseguiti sul database.</span><span class="sxs-lookup"><span data-stu-id="fef6f-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="fef6f-169">Se non viene fornita alcuna implementazione, EF eseguirà semplicemente i comandi e propagherà le eccezioni generate.</span><span class="sxs-lookup"><span data-stu-id="fef6f-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="fef6f-170">Per SQL Server questo servizio viene usato per fornire un criterio di ripetizione dei tentativi particolarmente utile quando viene eseguito su server di database basati su cloud, ad esempio SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="fef6f-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="fef6f-171">**Key**: oggetto ExecutionStrategyKey contenente il nome invariante del provider e, facoltativamente, un nome del server per il quale verrà utilizzata la strategia di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-172">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="fef6f-173">Func<DbConnection, String, System. Data. Entity. Migrations. History. HistoryContext\></span><span class="sxs-lookup"><span data-stu-id="fef6f-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="fef6f-174">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-175">**Oggetto restituito**: Factory che consente a un provider di configurare il mapping di HistoryContext alla `__MigrationHistory` tabella usata dalle migrazioni di EF.</span><span class="sxs-lookup"><span data-stu-id="fef6f-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="fef6f-176">HistoryContext è un Code First DbContext e può essere configurato usando l'API Fluent normale per modificare elementi quali il nome della tabella e le specifiche di mapping delle colonne.</span><span class="sxs-lookup"><span data-stu-id="fef6f-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="fef6f-177">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-178">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="fef6f-179">System. Data. Common. DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="fef6f-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="fef6f-180">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-181">**Oggetto restituito**: provider ADO.NET da usare per un nome invariante del provider specificato.</span><span class="sxs-lookup"><span data-stu-id="fef6f-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="fef6f-182">**Key**: stringa che contiene il nome invariante del provider ADO.NET</span><span class="sxs-lookup"><span data-stu-id="fef6f-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-183">Questo servizio non viene in genere modificato direttamente perché l'implementazione predefinita Usa la registrazione del provider ADO.NET normale.</span><span class="sxs-lookup"><span data-stu-id="fef6f-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="fef6f-184">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="fef6f-185">System. Data. Entity. Infrastructure. IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="fef6f-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="fef6f-186">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-187">**Oggetto restituito**: un servizio utilizzato per determinare un nome invariante del provider per un tipo di DbProviderFactory specificato.</span><span class="sxs-lookup"><span data-stu-id="fef6f-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="fef6f-188">L'implementazione predefinita di questo servizio usa la registrazione del provider ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="fef6f-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="fef6f-189">Ciò significa che se il provider ADO.NET non è registrato in modo normale perché DbProviderFactory viene risolto da EF, sarà necessario anche risolvere questo servizio.</span><span class="sxs-lookup"><span data-stu-id="fef6f-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="fef6f-190">**Key**: istanza di DbProviderFactory per la quale è richiesto un nome invariante.</span><span class="sxs-lookup"><span data-stu-id="fef6f-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="fef6f-191">Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="fef6f-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="fef6f-192">System. Data. Entity. Core. mapping. ViewGeneration. IViewAssemblyCache</span><span class="sxs-lookup"><span data-stu-id="fef6f-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="fef6f-193">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-194">**Oggetto restituito**: una cache degli assembly che contengono visualizzazioni pregenerate.</span><span class="sxs-lookup"><span data-stu-id="fef6f-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="fef6f-195">Una sostituzione viene in genere utilizzata per indicare a EF quali assembly contengono viste pregenerate senza eseguire alcuna individuazione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="fef6f-196">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="fef6f-197">System. Data. Entity. Infrastructure. Pluraling. IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="fef6f-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="fef6f-198">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-199">**Oggetto restituito**: un servizio usato da EF ai nomi plurali e singolari.</span><span class="sxs-lookup"><span data-stu-id="fef6f-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="fef6f-200">Per impostazione predefinita, viene usato un servizio di pluralismo in inglese.</span><span class="sxs-lookup"><span data-stu-id="fef6f-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="fef6f-201">**Chiave**: non usata; sarà null</span><span class="sxs-lookup"><span data-stu-id="fef6f-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="fef6f-202">System. Data. Entity. Infrastructure. Intercettation. IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="fef6f-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="fef6f-203">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="fef6f-204">**Oggetti restituiti**: qualsiasi intercettore che deve essere registrato all'avvio dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="fef6f-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="fef6f-205">Si noti che questi oggetti vengono richiesti usando la chiamata GetServices e tutti gli intercettori restituiti da qualsiasi resolver di dipendenza verranno registrati.</span><span class="sxs-lookup"><span data-stu-id="fef6f-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="fef6f-206">**Chiave**: non usata; sarà null.</span><span class="sxs-lookup"><span data-stu-id="fef6f-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="fef6f-207">Func<System. Data. Entity. DbContext, Action<String \> , System. Data. Entity. Infrastructure. Interceptor. DatabaseLogFormatter\></span><span class="sxs-lookup"><span data-stu-id="fef6f-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="fef6f-208">**Versione introdotta**: Ef 6.0.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="fef6f-209">**Oggetto restituito**: Factory che verrà usata per creare il formattatore di log del database che verrà usato quando il contesto. La proprietà database. log è impostata nel contesto specificato.</span><span class="sxs-lookup"><span data-stu-id="fef6f-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="fef6f-210">**Chiave**: non usata; sarà null.</span><span class="sxs-lookup"><span data-stu-id="fef6f-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="fef6f-211">Func<System. Data. Entity. DbContext\></span><span class="sxs-lookup"><span data-stu-id="fef6f-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="fef6f-212">**Versione introdotta**: Ef 6.1.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="fef6f-213">**Oggetto restituito**: Factory che verrà usata per creare istanze di contesto per le migrazioni quando il contesto non dispone di un costruttore senza parametri accessibile.</span><span class="sxs-lookup"><span data-stu-id="fef6f-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="fef6f-214">**Key**: oggetto Type per il tipo di DbContext derivato per il quale è necessaria una factory.</span><span class="sxs-lookup"><span data-stu-id="fef6f-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="fef6f-215">Func<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="fef6f-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="fef6f-216">**Versione introdotta**: Ef 6.1.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="fef6f-217">**Oggetto restituito**: Factory che verrà utilizzata per creare serializzatori per la serializzazione di annotazioni personalizzate fortemente tipizzate in modo che possano essere serializzate e desterilizzate in XML per l'utilizzo in Migrazioni Code First.</span><span class="sxs-lookup"><span data-stu-id="fef6f-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="fef6f-218">**Key**: il nome dell'annotazione da serializzare o deserializzare.</span><span class="sxs-lookup"><span data-stu-id="fef6f-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="fef6f-219">Func<System. Data. Entity. Infrastructure. TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="fef6f-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="fef6f-220">**Versione introdotta**: Ef 6.1.0</span><span class="sxs-lookup"><span data-stu-id="fef6f-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="fef6f-221">**Oggetto restituito**: Factory che verrà usata per creare gestori per le transazioni, in modo che sia possibile applicare una gestione speciale per situazioni come la gestione degli errori di commit.</span><span class="sxs-lookup"><span data-stu-id="fef6f-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="fef6f-222">**Key**: oggetto ExecutionStrategyKey che contiene il nome invariante del provider e, facoltativamente, un nome di server per il quale verrà utilizzato il gestore delle transazioni.</span><span class="sxs-lookup"><span data-stu-id="fef6f-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
