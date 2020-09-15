---
title: Risoluzione delle dipendenze-EF6
description: Risoluzione delle dipendenze in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 24e932646f0c44f642bff3f6db321590abae1c8d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070549"
---
# <a name="dependency-resolution"></a>Risoluzione delle dipendenze
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

A partire da EF6, Entity Framework contiene un meccanismo di utilizzo generico per ottenere le implementazioni dei servizi richiesti. Ovvero, quando Entity Framework usa un'istanza di alcune interfacce o classi di base, richiede un'implementazione concreta dell'interfaccia o della classe di base da usare. Questa operazione viene eseguita usando l'interfaccia IDbDependencyResolver:  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

Il metodo GetService viene in genere chiamato da EF ed è gestito da un'implementazione di IDbDependencyResolver fornita da EF o dall'applicazione. Quando viene chiamato, l'argomento di tipo è l'interfaccia o il tipo di classe di base del servizio richiesto e l'oggetto chiave è null o un oggetto che fornisce informazioni contestuali sul servizio richiesto.  

Se non diversamente specificato, qualsiasi oggetto restituito deve essere thread-safe poiché può essere usato come singleton. In molti casi l'oggetto restituito è una factory, nel qual caso la factory deve essere thread-safe, ma non è necessario che l'oggetto restituito dalla factory sia thread-safe perché per ogni uso viene richiesta una nuova istanza dalla factory.

Questo articolo non contiene informazioni dettagliate su come implementare IDbDependencyResolver, ma funge da riferimento per i tipi di servizio (ovvero i tipi di interfaccia e di classe di base) per i quali EF chiama GetService e la semantica dell'oggetto chiave per ognuna di queste chiamate.

## <a name="systemdataentityidatabaseinitializertcontext"></a>System. Data. Entity. IDatabaseInitializer<TContext\>  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: inizializzatore di database per il tipo di contesto specificato  

**Chiave**: non usata; sarà null  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>Func<System. Data. Entity. Migrations. SQL. MigrationSqlGenerator\>  

**Versione introdotta**: Ef 6.0.0

**Oggetto restituito**: una factory per creare un generatore SQL che può essere usato per le migrazioni e altre azioni che comportano la creazione di un database, ad esempio la creazione di un database con inizializzatori di database.  

**Key**: stringa che contiene il nome invariante del provider ADO.NET che specifica il tipo di database per cui verrà generato SQL. Ad esempio, viene restituito il generatore di SQL Server SQL per la chiave "System. Data. SqlClient".  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycorecommondbproviderservices"></a>System. Data. Entity. Core. Common. DbProviderServices  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: il provider EF da usare per un nome invariante del provider specificato  

**Key**: stringa che contiene il nome invariante del provider ADO.NET che specifica il tipo di database per cui è necessario un provider. Il provider SQL Server, ad esempio, viene restituito per la chiave "System. Data. SqlClient".  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System. Data. Entity. Infrastructure. IDbConnectionFactory  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: la factory di connessione che verrà usata quando EF crea una connessione di database per convenzione. Ovvero, quando nessuna connessione o stringa di connessione viene assegnata a EF e non è possibile trovare una stringa di connessione nella app.config o web.config, questo servizio viene utilizzato per creare una connessione per convenzione. La modifica della factory di connessione può consentire a EF di usare un tipo diverso di database, ad esempio SQL Server Compact Edition, per impostazione predefinita.  

**Chiave**: non usata; sarà null  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System. Data. Entity. Infrastructure. IManifestTokenService  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: un servizio che può generare un token del manifesto del provider da una connessione. Questo servizio viene in genere usato in due modi. In primo luogo, può essere utilizzato per evitare Code First la connessione al database durante la compilazione di un modello. In secondo luogo, può essere utilizzato per forzare Code First a compilare un modello per una versione specifica del database, ad esempio per forzare un modello per SQL Server 2005 anche se talvolta viene utilizzato SQL Server 2008.  

**Durata degli oggetti**: Singleton: lo stesso oggetto può essere usato più volte e simultaneamente da thread diversi  

**Chiave**: non usata; sarà null  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System. Data. Entity. Infrastructure. IDbProviderFactoryService  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: un servizio che può ottenere una factory del provider da una connessione specificata. In .NET 4,5 il provider è accessibile pubblicamente dalla connessione. In .NET 4, l'implementazione predefinita di questo servizio usa alcune euristiche per trovare il provider corrispondente. Se questi errori hanno esito negativo, è possibile registrare una nuova implementazione di questo servizio per fornire una soluzione appropriata.  

**Chiave**: non usata; sarà null  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func<DbContext, System. Data. Entity. Infrastructure. IDbModelCacheKey\>  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: Factory che genererà una chiave di cache del modello per un determinato contesto. Per impostazione predefinita, EF memorizza nella cache un modello per ogni tipo di DbContext per provider. Per aggiungere altre informazioni, ad esempio nome schema, alla chiave della cache, è possibile usare un'implementazione diversa di questo servizio.  

**Chiave**: non usata; sarà null  

## <a name="systemdataentityspatialdbspatialservices"></a>System. Data. Entity. Spatial. DbSpatialServices  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: provider spaziale EF che aggiunge il supporto al provider EF di base per i tipi spaziali geography e Geometry.  

**Chiave**: DbSptialServices viene richiesta in due modi. Per prima cosa, i servizi spaziali specifici del provider vengono richiesti usando un oggetto DbProviderInfo, che contiene il nome invariante e il token del manifesto, come chiave. In secondo luogo, è possibile chiedere a DbSpatialServices senza chiave. Viene usato per risolvere il "provider spaziale globale" usato per la creazione di tipi DbGeography o DbGeometry autonomi.  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>Func<System. Data. Entity. Infrastructure. IDbExecutionStrategy\>  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: una factory per creare un servizio che consente a un provider di implementare nuovi tentativi o un altro comportamento quando le query e i comandi vengono eseguiti sul database. Se non viene fornita alcuna implementazione, EF eseguirà semplicemente i comandi e propagherà le eccezioni generate. Per SQL Server questo servizio viene usato per fornire un criterio di ripetizione dei tentativi particolarmente utile quando viene eseguito su server di database basati su cloud, ad esempio SQL Azure.  

**Key**: oggetto ExecutionStrategyKey contenente il nome invariante del provider e, facoltativamente, un nome del server per il quale verrà utilizzata la strategia di esecuzione.  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func<DbConnection, String, System. Data. Entity. Migrations. History. HistoryContext\>  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: Factory che consente a un provider di configurare il mapping di HistoryContext alla `__MigrationHistory` tabella usata dalle migrazioni di EF. HistoryContext è un Code First DbContext e può essere configurato usando l'API Fluent normale per modificare elementi quali il nome della tabella e le specifiche di mapping delle colonne.  

**Chiave**: non usata; sarà null  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdatacommondbproviderfactory"></a>System. Data. Common. DbProviderFactory  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: provider ADO.NET da usare per un nome invariante del provider specificato.  

**Key**: stringa che contiene il nome invariante del provider ADO.NET  

>[!NOTE]
> Questo servizio non viene in genere modificato direttamente perché l'implementazione predefinita Usa la registrazione del provider ADO.NET normale. Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System. Data. Entity. Infrastructure. IProviderInvariantName  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: un servizio utilizzato per determinare un nome invariante del provider per un tipo di DbProviderFactory specificato. L'implementazione predefinita di questo servizio usa la registrazione del provider ADO.NET. Ciò significa che se il provider ADO.NET non è registrato in modo normale perché DbProviderFactory viene risolto da EF, sarà necessario anche risolvere questo servizio.  

**Key**: istanza di DbProviderFactory per la quale è richiesto un nome invariante.  

>[!NOTE]
> Per altri dettagli sui servizi correlati al provider in EF6, vedere la sezione [modello di provider EF6](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System. Data. Entity. Core. mapping. ViewGeneration. IViewAssemblyCache  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: una cache degli assembly che contengono visualizzazioni pregenerate. Una sostituzione viene in genere utilizzata per indicare a EF quali assembly contengono viste pregenerate senza eseguire alcuna individuazione.  

**Chiave**: non usata; sarà null  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System. Data. Entity. Infrastructure. Pluraling. IPluralizationService

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: un servizio usato da EF ai nomi plurali e singolari. Per impostazione predefinita, viene usato un servizio di pluralismo in inglese.  

**Chiave**: non usata; sarà null  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System. Data. Entity. Infrastructure. Intercettation. IDbInterceptor  

**Versione introdotta**: Ef 6.0.0

**Oggetti restituiti**: qualsiasi intercettore che deve essere registrato all'avvio dell'applicazione. Si noti che questi oggetti vengono richiesti usando la chiamata GetServices e tutti gli intercettori restituiti da qualsiasi resolver di dipendenza verranno registrati.

**Chiave**: non usata; sarà null.  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func<System. Data. Entity. DbContext, Action<String \> , System. Data. Entity. Infrastructure. Interceptor. DatabaseLogFormatter\>  

**Versione introdotta**: Ef 6.0.0  

**Oggetto restituito**: Factory che verrà usata per creare il formattatore di log del database che verrà usato quando il contesto. La proprietà database. log è impostata nel contesto specificato.  

**Chiave**: non usata; sarà null.  

## <a name="funcsystemdataentitydbcontext"></a>Func<System. Data. Entity. DbContext\>  

**Versione introdotta**: Ef 6.1.0  

**Oggetto restituito**: Factory che verrà usata per creare istanze di contesto per le migrazioni quando il contesto non dispone di un costruttore senza parametri accessibile.  

**Key**: oggetto Type per il tipo di DbContext derivato per il quale è necessaria una factory.  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>Func<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\>  

**Versione introdotta**: Ef 6.1.0  

**Oggetto restituito**: Factory che verrà utilizzata per creare serializzatori per la serializzazione di annotazioni personalizzate fortemente tipizzate in modo che possano essere serializzate e desterilizzate in XML per l'utilizzo in Migrazioni Code First.  

**Key**: il nome dell'annotazione da serializzare o deserializzare.  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>Func<System. Data. Entity. Infrastructure. TransactionHandler\>  

**Versione introdotta**: Ef 6.1.0  

**Oggetto restituito**: Factory che verrà usata per creare gestori per le transazioni, in modo che sia possibile applicare una gestione speciale per situazioni come la gestione degli errori di commit.  

**Key**: oggetto ExecutionStrategyKey che contiene il nome invariante del provider e, facoltativamente, un nome di server per il quale verrà utilizzato il gestore delle transazioni.  
