---
title: Impostazioni del file di configurazione-EF6
description: Impostazioni del file di configurazione in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: fc991810e93840c27e6631dfb5bc1796c1328d37
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063322"
---
# <a name="configuration-file-settings"></a>Impostazioni del file di configurazione
Entity Framework consente di specificare alcune impostazioni dal file di configurazione. In generale EF segue un principio di "Convenzione sulla configurazione": tutte le impostazioni descritte in questo post hanno un comportamento predefinito, è necessario preoccuparsi solo di modificare l'impostazione quando il valore predefinito non soddisfa più i requisiti.  

## <a name="a-code-based-alternative"></a>Alternativa Code-Based  

Tutte queste impostazioni possono essere applicate anche usando il codice. A partire da EF6 è stata introdotta la [configurazione basata sul codice](xref:ef6/fundamentals/configuring/code-based), che fornisce un metodo centrale per applicare la configurazione dal codice. Prima di EF6, la configurazione può comunque essere applicata dal codice, ma è necessario usare varie API per configurare aree diverse. L'opzione file di configurazione consente di modificare facilmente queste impostazioni durante la distribuzione senza aggiornare il codice.

## <a name="the-entity-framework-configuration-section"></a>Sezione di configurazione Entity Framework  

A partire da EF 4.1 è possibile impostare l'inizializzatore di database per un contesto usando la sezione **appSettings** del file di configurazione. In EF 4,3 è stata introdotta la sezione **EntityFramework** personalizzata per gestire le nuove impostazioni. Entity Framework rileverà comunque gli inizializzatori di database impostati utilizzando il formato precedente, ma è consigliabile procedere al nuovo formato, laddove possibile.

La sezione **EntityFramework** è stata aggiunta automaticamente al file di configurazione del progetto quando è stato installato il pacchetto NuGet EntityFramework.  

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

## <a name="connection-strings"></a>Stringhe di connessione  

In [Questa pagina](xref:ef6/fundamentals/configuring/connection-strings) vengono forniti ulteriori dettagli sul modo in cui Entity Framework determina il database da utilizzare, incluse le stringhe di connessione nel file di configurazione.  

Le stringhe di connessione vengono inserite nell'elemento standard **connectionStrings** e non richiedono la sezione **EntityFramework** .  

I modelli basati su Code First usano le normali stringhe di connessione di ADO.NET. Ad esempio:  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

I modelli basati su EF designer usano stringhe di connessione EF speciali. Ad esempio:  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>Tipo di configurazione Code-Based (EF6 e versioni successive)  

A partire da EF6, è possibile specificare DbConfiguration per EF da usare per la [configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based) nell'applicazione. Nella maggior parte dei casi non è necessario specificare questa impostazione perché EF individuerà automaticamente i DbConfiguration. Per informazioni dettagliate su quando potrebbe essere necessario specificare DbConfiguration nel file di configurazione, vedere la sezione relativa allo stato di **DbConfiguration** in [fase di configurazione basata su codice](xref:ef6/fundamentals/configuring/code-based).  

Per impostare un tipo DbConfiguration, è necessario specificare il nome del tipo completo dell'assembly nell'elemento **codeConfigurationType** .  

> [!NOTE]
> Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo. Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>Provider di database EF (EF6 e versioni successive)  

Nelle versioni precedenti a EF6, le parti specifiche di un provider di database di Entity Framework devono essere incluse come parte del provider ADO.NET di base. A partire da EF6, le parti specifiche di EF sono ora gestite e registrate separatamente.  

In genere non è necessario registrare manualmente i provider. Questa operazione viene in genere eseguita dal provider durante l'installazione.  

I provider vengono registrati includendo un elemento **provider** nella sezione figlio **provider** della sezione **EntityFramework** . Sono disponibili due attributi obbligatori per una voce del provider:  

- **invariname** identifica il provider ADO.NET di base a cui è destinato questo provider EF  
- **Type** è il nome del tipo completo di assembly dell'implementazione del provider EF  

> [!NOTE]
> Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo. Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.  

Ad esempio, ecco la voce creata per registrare il provider di SQL Server predefinito quando si installa Entity Framework.  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>Intercettori (EF 6.1 e versioni successive)  

A partire da EF 6.1 è possibile registrare gli intercettori nel file di configurazione. Gli intercettori consentono di eseguire logica aggiuntiva quando EF esegue determinate operazioni, ad esempio l'esecuzione di query sul database, l'apertura di connessioni e così via.  

Gli intercettori vengono registrati includendo un elemento **Interceptor** nella sezione figlio **Interceptor** della sezione **EntityFramework** . La configurazione seguente, ad esempio, registra l'intercettore **DatabaseLogger** incorporato che registrerà tutte le operazioni di database nella console.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>Registrazione delle operazioni di database in un file (EF 6.1 e versioni successive)  

La registrazione degli intercettori tramite il file di configurazione è particolarmente utile quando si desidera aggiungere la registrazione a un'applicazione esistente per facilitare il debug di un problema. **DatabaseLogger** supporta la registrazione in un file fornendo il nome del file come parametro del costruttore.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

Per impostazione predefinita, il file di log verrà sovrascritto con un nuovo file ogni volta che l'app viene avviata. Per aggiungere invece al file di log se esiste già, usare un valore simile al seguente:  

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

Per altre informazioni su **DatabaseLogger** e sulla registrazione degli intercettori, vedere il post di blog [Ef 6,1: attivazione della registrazione senza ricompilazione](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).  

## <a name="code-first-default-connection-factory"></a>Code First Factory di connessione predefinita  

La sezione di configurazione consente di specificare una factory di connessione predefinita che Code First deve usare per individuare un database da usare per un contesto. La factory di connessione predefinita viene utilizzata solo quando non è stata aggiunta alcuna stringa di connessione al file di configurazione per un contesto.  

Quando è stato installato il pacchetto NuGet EF è stata registrata una factory di connessione predefinita che punta a SQL Express o al database locale, a seconda di quale installato.  

Per impostare una factory di connessione, specificare il nome del tipo completo dell'assembly nell'elemento **defaultConnectionFactory** .  

> [!NOTE]
> Il nome completo di un assembly è il nome completo dello spazio dei nomi, seguito da una virgola, quindi dall'assembly in cui risiede il tipo. Facoltativamente, è anche possibile specificare la versione dell'assembly, le impostazioni cultura e il token di chiave pubblica.  

Di seguito è riportato un esempio di impostazione della factory di connessione predefinita:  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

L'esempio precedente richiede che la factory personalizzata disponga di un costruttore senza parametri. Se necessario, è possibile specificare i parametri del costruttore usando l'elemento **Parameters** .  

Ad esempio, SqlCeConnectionFactory, incluso in Entity Framework, richiede di specificare un nome invariante del provider per il costruttore. Il nome invariante del provider identifica la versione di SQL Compact che si desidera utilizzare. Con la seguente configurazione, i contesti utilizzeranno SQL Compact versione 4,0 per impostazione predefinita.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Se non si imposta una factory di connessione predefinita, Code First USA SqlConnectionFactory, che punta a `.\SQLEXPRESS` . SqlConnectionFactory dispone inoltre di un costruttore che consente di eseguire l'override di parti della stringa di connessione. Se si desidera utilizzare un'istanza di SQL Server diversa da `.\SQLEXPRESS` , è possibile utilizzare questo costruttore per impostare il server.  

La Code First configurazione seguente provocherà l'uso di **ServerDatabase** per i contesti che non dispongono di una stringa di connessione esplicita impostata.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Per impostazione predefinita, si presuppone che gli argomenti del costruttore siano di tipo String. Per modificare questa operazione, è possibile usare l'attributo Type.  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>Inizializzatori di database  

Gli inizializzatori di database sono configurati in base al contesto. Possono essere impostate nel file di configurazione usando l'elemento **context** . Questo elemento usa il nome completo dell'assembly per identificare il contesto da configurare.  

Per impostazione predefinita, i contesti di Code First sono configurati per l'utilizzo dell'inizializzatore CreateDatabaseIfNotExists. Nell'elemento **context** è disponibile un attributo **disableDatabaseInitialization** che può essere usato per disabilitare l'inizializzazione del database.  

La configurazione seguente, ad esempio, Disabilita l'inizializzazione del database per il contesto blogging. BlogContext definito in MyAssembly.dll.  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

Per impostare un inizializzatore personalizzato, è possibile usare l'elemento **databaseInitializer** .  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

I parametri del costruttore utilizzano la stessa sintassi delle factory di connessione predefinite.  

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

È possibile configurare uno degli inizializzatori di database generici inclusi in Entity Framework. L'attributo **Type** usa il formato .NET Framework per i tipi generici.  

Se, ad esempio, si utilizza Migrazioni Code First, è possibile configurare il database di cui eseguire la migrazione automaticamente utilizzando l' `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` inizializzatore.  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
