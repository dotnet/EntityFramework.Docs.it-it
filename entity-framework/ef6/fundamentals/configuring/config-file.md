---
title: Impostazioni del file di configurazione-EF6
description: Impostazioni del file di configurazione in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: b407ae7d64df18f6a8d80aa13703a8c243c6f3a2
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070718"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="4228f-103">Impostazioni del file di configurazione</span><span class="sxs-lookup"><span data-stu-id="4228f-103">Configuration File Settings</span></span>
<span data-ttu-id="4228f-104">Entity Framework consente di specificare alcune impostazioni dal file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="4228f-104">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="4228f-105">In generale EF segue un principio di "Convenzione sulla configurazione": tutte le impostazioni descritte in questo post hanno un comportamento predefinito, è necessario preoccuparsi solo di modificare l'impostazione quando il valore predefinito non soddisfa più i requisiti.</span><span class="sxs-lookup"><span data-stu-id="4228f-105">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="4228f-106">Un'alternativa basata sul codice</span><span class="sxs-lookup"><span data-stu-id="4228f-106">A Code-Based Alternative</span></span>  

<span data-ttu-id="4228f-107">Tutte queste impostazioni possono essere applicate anche usando il codice.</span><span class="sxs-lookup"><span data-stu-id="4228f-107">All of these settings can also be applied using code.</span></span> <span data-ttu-id="4228f-108">A partire da EF6 è stata introdotta la [configurazione basata sul codice](xref:ef6/fundamentals/configuring/code-based), che fornisce un metodo centrale per applicare la configurazione dal codice.</span><span class="sxs-lookup"><span data-stu-id="4228f-108">Starting in EF6 we introduced [code-based configuration](xref:ef6/fundamentals/configuring/code-based), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="4228f-109">Prima di EF6, la configurazione può comunque essere applicata dal codice, ma è necessario usare varie API per configurare aree diverse.</span><span class="sxs-lookup"><span data-stu-id="4228f-109">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="4228f-110">L'opzione file di configurazione consente di modificare facilmente queste impostazioni durante la distribuzione senza aggiornare il codice.</span><span class="sxs-lookup"><span data-stu-id="4228f-110">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="4228f-111">Sezione di configurazione Entity Framework</span><span class="sxs-lookup"><span data-stu-id="4228f-111">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="4228f-112">A partire da EF 4.1 è possibile impostare l'inizializzatore di database per un contesto usando la sezione **appSettings** del file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="4228f-112">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="4228f-113">In EF 4,3 è stata introdotta la sezione **EntityFramework** personalizzata per gestire le nuove impostazioni.</span><span class="sxs-lookup"><span data-stu-id="4228f-113">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="4228f-114">Entity Framework rileverà comunque gli inizializzatori di database impostati utilizzando il formato precedente, ma è consigliabile procedere al nuovo formato, laddove possibile.</span><span class="sxs-lookup"><span data-stu-id="4228f-114">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="4228f-115">La sezione **EntityFramework** è stata aggiunta automaticamente al file di configurazione del progetto quando è stato installato il pacchetto NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="4228f-115">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="4228f-116">Stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="4228f-116">Connection Strings</span></span>  

<span data-ttu-id="4228f-117">In [Questa pagina](xref:ef6/fundamentals/configuring/connection-strings) vengono forniti ulteriori dettagli sul modo in cui Entity Framework determina il database da utilizzare, incluse le stringhe di connessione nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="4228f-117">[This page](xref:ef6/fundamentals/configuring/connection-strings) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="4228f-118">Le stringhe di connessione vengono inserite nell'elemento standard **connectionStrings** e non richiedono la sezione **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="4228f-118">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="4228f-119">I modelli basati su Code First usano le normali stringhe di connessione di ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="4228f-119">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="4228f-120">Esempio:</span><span class="sxs-lookup"><span data-stu-id="4228f-120">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="4228f-121">I modelli basati su EF designer usano stringhe di connessione EF speciali.</span><span class="sxs-lookup"><span data-stu-id="4228f-121">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="4228f-122">Esempio:</span><span class="sxs-lookup"><span data-stu-id="4228f-122">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="4228f-123">Tipo di configurazione basata su codice (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="4228f-123">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="4228f-124">A partire da EF6, è possibile specificare DbConfiguration per EF da usare per la [configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based) nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="4228f-124">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](xref:ef6/fundamentals/configuring/code-based) in your application.</span></span> <span data-ttu-id="4228f-125">Nella maggior parte dei casi non è necessario specificare questa impostazione perché EF individuerà automaticamente i DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="4228f-125">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="4228f-126">Per informazioni dettagliate su quando potrebbe essere necessario specificare DbConfiguration nel file di configurazione, vedere la sezione relativa allo stato di **DbConfiguration** in [fase di configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based).</span><span class="sxs-lookup"><span data-stu-id="4228f-126">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span>  

<span data-ttu-id="4228f-127">Per impostare un tipo DbConfiguration, è necessario specificare il nome del tipo completo dell'assembly nell'elemento **codeConfigurationType** .</span><span class="sxs-lookup"><span data-stu-id="4228f-127">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="4228f-128">Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo.</span><span class="sxs-lookup"><span data-stu-id="4228f-128">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="4228f-129">Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="4228f-129">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="4228f-130">Provider di database EF (EF6 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="4228f-130">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="4228f-131">Nelle versioni precedenti a EF6, le parti specifiche di un provider di database di Entity Framework devono essere incluse come parte del provider ADO.NET di base.</span><span class="sxs-lookup"><span data-stu-id="4228f-131">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="4228f-132">A partire da EF6, le parti specifiche di EF sono ora gestite e registrate separatamente.</span><span class="sxs-lookup"><span data-stu-id="4228f-132">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="4228f-133">In genere non è necessario registrare manualmente i provider.</span><span class="sxs-lookup"><span data-stu-id="4228f-133">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="4228f-134">Questa operazione viene in genere eseguita dal provider durante l'installazione.</span><span class="sxs-lookup"><span data-stu-id="4228f-134">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="4228f-135">I provider vengono registrati includendo un elemento **provider** nella sezione figlio **provider** della sezione **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="4228f-135">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="4228f-136">Sono disponibili due attributi obbligatori per una voce del provider:</span><span class="sxs-lookup"><span data-stu-id="4228f-136">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="4228f-137">**invariname** identifica il provider ADO.NET di base a cui è destinato questo provider EF</span><span class="sxs-lookup"><span data-stu-id="4228f-137">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="4228f-138">**Type** è il nome del tipo completo di assembly dell'implementazione del provider EF</span><span class="sxs-lookup"><span data-stu-id="4228f-138">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="4228f-139">Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo.</span><span class="sxs-lookup"><span data-stu-id="4228f-139">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="4228f-140">Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="4228f-140">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="4228f-141">Ad esempio, ecco la voce creata per registrare il provider di SQL Server predefinito quando si installa Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="4228f-141">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="4228f-142">Intercettori (EF 6.1 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="4228f-142">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="4228f-143">A partire da EF 6.1 è possibile registrare gli intercettori nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="4228f-143">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="4228f-144">Gli intercettori consentono di eseguire logica aggiuntiva quando EF esegue determinate operazioni, ad esempio l'esecuzione di query sul database, l'apertura di connessioni e così via.</span><span class="sxs-lookup"><span data-stu-id="4228f-144">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="4228f-145">Gli intercettori vengono registrati includendo un elemento **Interceptor** nella sezione figlio **Interceptor** della sezione **EntityFramework** .</span><span class="sxs-lookup"><span data-stu-id="4228f-145">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="4228f-146">La configurazione seguente, ad esempio, registra l'intercettore **DatabaseLogger** incorporato che registrerà tutte le operazioni di database nella console.</span><span class="sxs-lookup"><span data-stu-id="4228f-146">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="4228f-147">Registrazione delle operazioni di database in un file (EF 6.1 e versioni successive)</span><span class="sxs-lookup"><span data-stu-id="4228f-147">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="4228f-148">La registrazione degli intercettori tramite il file di configurazione è particolarmente utile quando si desidera aggiungere la registrazione a un'applicazione esistente per facilitare il debug di un problema.</span><span class="sxs-lookup"><span data-stu-id="4228f-148">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="4228f-149">**DatabaseLogger** supporta la registrazione in un file fornendo il nome del file come parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="4228f-149">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="4228f-150">Per impostazione predefinita, il file di log verrà sovrascritto con un nuovo file ogni volta che l'app viene avviata.</span><span class="sxs-lookup"><span data-stu-id="4228f-150">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="4228f-151">Per aggiungere invece al file di log se esiste già, usare un valore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="4228f-151">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="4228f-152">Per altre informazioni su **DatabaseLogger** e sulla registrazione degli intercettori, vedere il post di blog [Ef 6,1: attivazione della registrazione senza ricompilazione](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span><span class="sxs-lookup"><span data-stu-id="4228f-152">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="4228f-153">Code First Factory di connessione predefinita</span><span class="sxs-lookup"><span data-stu-id="4228f-153">Code First Default Connection Factory</span></span>  

<span data-ttu-id="4228f-154">La sezione di configurazione consente di specificare una factory di connessione predefinita che Code First deve usare per individuare un database da usare per un contesto.</span><span class="sxs-lookup"><span data-stu-id="4228f-154">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="4228f-155">La factory di connessione predefinita viene utilizzata solo quando non è stata aggiunta alcuna stringa di connessione al file di configurazione per un contesto.</span><span class="sxs-lookup"><span data-stu-id="4228f-155">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="4228f-156">Quando è stato installato il pacchetto NuGet EF è stata registrata una factory di connessione predefinita che punta a SQL Express o al database locale, a seconda di quale installato.</span><span class="sxs-lookup"><span data-stu-id="4228f-156">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="4228f-157">Per impostare una factory di connessione, specificare il nome del tipo completo dell'assembly nell'elemento **defaultConnectionFactory** .</span><span class="sxs-lookup"><span data-stu-id="4228f-157">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="4228f-158">Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo.</span><span class="sxs-lookup"><span data-stu-id="4228f-158">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="4228f-159">Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.</span><span class="sxs-lookup"><span data-stu-id="4228f-159">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="4228f-160">Di seguito è riportato un esempio di impostazione della factory di connessione predefinita:</span><span class="sxs-lookup"><span data-stu-id="4228f-160">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="4228f-161">L'esempio precedente richiede che la factory personalizzata disponga di un costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="4228f-161">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="4228f-162">Se necessario, è possibile specificare i parametri del costruttore usando l'elemento **Parameters** .</span><span class="sxs-lookup"><span data-stu-id="4228f-162">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="4228f-163">Ad esempio, SqlCeConnectionFactory, incluso in Entity Framework, richiede di specificare un nome invariante del provider per il costruttore.</span><span class="sxs-lookup"><span data-stu-id="4228f-163">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="4228f-164">Il nome invariante del provider identifica la versione di SQL Compact che si desidera utilizzare.</span><span class="sxs-lookup"><span data-stu-id="4228f-164">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="4228f-165">Con la seguente configurazione, i contesti utilizzeranno SQL Compact versione 4,0 per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="4228f-165">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="4228f-166">Se non si imposta una factory di connessione predefinita, Code First USA SqlConnectionFactory, che punta a `.\SQLEXPRESS` .</span><span class="sxs-lookup"><span data-stu-id="4228f-166">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="4228f-167">SqlConnectionFactory dispone inoltre di un costruttore che consente di eseguire l'override di parti della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="4228f-167">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="4228f-168">Se si desidera utilizzare un'istanza di SQL Server diversa da `.\SQLEXPRESS` , è possibile utilizzare questo costruttore per impostare il server.</span><span class="sxs-lookup"><span data-stu-id="4228f-168">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="4228f-169">La Code First configurazione seguente provocherà l'uso di **ServerDatabase** per i contesti che non dispongono di una stringa di connessione esplicita impostata.</span><span class="sxs-lookup"><span data-stu-id="4228f-169">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="4228f-170">Per impostazione predefinita, si presuppone che gli argomenti del costruttore siano di tipo String.</span><span class="sxs-lookup"><span data-stu-id="4228f-170">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="4228f-171">Per modificare questa operazione, è possibile usare l'attributo Type.</span><span class="sxs-lookup"><span data-stu-id="4228f-171">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="4228f-172">Inizializzatori di database</span><span class="sxs-lookup"><span data-stu-id="4228f-172">Database Initializers</span></span>  

<span data-ttu-id="4228f-173">Gli inizializzatori di database sono configurati in base al contesto.</span><span class="sxs-lookup"><span data-stu-id="4228f-173">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="4228f-174">Possono essere impostate nel file di configurazione usando l'elemento **context** .</span><span class="sxs-lookup"><span data-stu-id="4228f-174">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="4228f-175">Questo elemento usa il nome completo dell'assembly per identificare il contesto da configurare.</span><span class="sxs-lookup"><span data-stu-id="4228f-175">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="4228f-176">Per impostazione predefinita, i contesti di Code First sono configurati per l'utilizzo dell'inizializzatore CreateDatabaseIfNotExists.</span><span class="sxs-lookup"><span data-stu-id="4228f-176">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="4228f-177">Nell'elemento **context** è disponibile un attributo **disableDatabaseInitialization** che può essere usato per disabilitare l'inizializzazione del database.</span><span class="sxs-lookup"><span data-stu-id="4228f-177">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="4228f-178">La configurazione seguente, ad esempio, Disabilita l'inizializzazione del database per il contesto blogging. BlogContext definito in MyAssembly.dll.</span><span class="sxs-lookup"><span data-stu-id="4228f-178">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="4228f-179">Per impostare un inizializzatore personalizzato, è possibile usare l'elemento **databaseInitializer** .</span><span class="sxs-lookup"><span data-stu-id="4228f-179">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="4228f-180">I parametri del costruttore utilizzano la stessa sintassi delle factory di connessione predefinite.</span><span class="sxs-lookup"><span data-stu-id="4228f-180">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="4228f-181">È possibile configurare uno degli inizializzatori di database generici inclusi in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="4228f-181">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="4228f-182">L'attributo **Type** usa il formato .NET Framework per i tipi generici.</span><span class="sxs-lookup"><span data-stu-id="4228f-182">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="4228f-183">Se, ad esempio, si utilizza Migrazioni Code First, è possibile configurare il database di cui eseguire la migrazione automaticamente utilizzando l' `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` inizializzatore.</span><span class="sxs-lookup"><span data-stu-id="4228f-183">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
