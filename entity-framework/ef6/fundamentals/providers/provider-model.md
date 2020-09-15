---
title: Modello di provider Entity Framework 6-EF6
description: Il modello di provider Entity Framework 6 in Entity Framework 6
author: divega
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: 4fc45ba5fe916253be348182196be236729d685d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074016"
---
# <a name="the-entity-framework-6-provider-model"></a>Modello di provider Entity Framework 6

Il modello di provider Entity Framework consente l'utilizzo Entity Framework con tipi diversi di server di database. Ad esempio, è possibile collegare un provider per consentire l'uso di EF con Microsoft SQL Server, mentre un altro provider può essere collegato per consentire l'uso di EF con Microsoft SQL Server Compact edizione. I provider per EF6 di cui si è consapevoli sono reperibili nella pagina [provider di Entity Framework](xref:ef6/fundamentals/providers/index) .

Sono state necessarie alcune modifiche al modo in cui EF interagisce con i provider per consentire il rilascio di EF con una licenza open source. Queste modifiche richiedono la ricompilazione dei provider EF rispetto agli assembly EF6 insieme ai nuovi meccanismi per la registrazione del provider.

## <a name="rebuilding"></a>Ricostruzione

Con EF6, il codice principale che in precedenza faceva parte del .NET Framework viene ora fornito come assembly fuori banda (OOB). Per informazioni dettagliate su come compilare le applicazioni in EF6, vedere la pagina [aggiornamento delle applicazioni per EF6](xref:ef6/what-is-new/upgrading-to-ef6) . Sarà inoltre necessario ricompilare i provider utilizzando queste istruzioni.

## <a name="provider-types-overview"></a>Panoramica sui tipi di provider

Un provider EF è in realtà una raccolta di servizi specifici del provider definiti dai tipi CLR da cui questi servizi si estendono (per una classe base) o implementano (per un'interfaccia). Due di questi servizi sono fondamentali e necessari per il funzionamento di EF. Altre sono facoltative e devono essere implementate solo se sono necessarie funzionalità specifiche e/o se le implementazioni predefinite di questi servizi non funzionano per il server di database specifico di destinazione.

## <a name="fundamental-provider-types"></a>Tipi di provider fondamentali

### <a name="dbproviderfactory"></a>DbProviderFactory

EF dipende dalla presenza di un tipo derivato da [System. Data. Common. DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) per l'esecuzione di tutti gli accessi al database di basso livello. DbProviderFactory non fa parte di Entity Framework, ma è invece una classe nel .NET Framework che funge da punto di ingresso per i provider ADO.NET che possono essere usati da Entity Framework, other o/RMs o direttamente da un'applicazione per ottenere istanze di connessioni, comandi, parametri e altre astrazioni di ADO.NET in modo indipendente dal provider. Per ulteriori informazioni su DbProviderFactory, vedere la [documentazione MSDN](https://msdn.microsoft.com/library/a6cd7c08.aspx)relativa a ADO.NET.

### <a name="dbproviderservices"></a>DbProviderServices

EF dipende dalla presenza di un tipo derivato da DbProviderServices per fornire funzionalità aggiuntive necessarie per EF oltre alle funzionalità già fornite dal provider ADO.NET. Nelle versioni precedenti di EF la classe DbProviderServices fa parte della .NET Framework ed è stata trovata nello spazio dei nomi System. Data. Common. A partire da EF6 questa classe fa ora parte di EntityFramework.dll e si trova nello spazio dei nomi System. Data. Entity. Core. Common.

Altre informazioni sulle funzionalità di base di un'implementazione di DbProviderServices sono disponibili in [MSDN](https://msdn.microsoft.com/library/ee789835.aspx). Si noti, tuttavia, che al momento della stesura di queste informazioni non viene aggiornata per EF6, anche se la maggior parte dei concetti è ancora valida. Anche le implementazioni SQL Server e SQL Server Compact di DbProviderServices vengono archiviate nella [codebase Open Source](https://github.com/aspnet/EntityFramework6/) e possono fungere da riferimenti utili per altre implementazioni.

Nelle versioni precedenti di EF l'implementazione di DbProviderServices da usare è stata ottenuta direttamente da un provider ADO.NET. Questa operazione è stata eseguita eseguendo il cast di DbProviderFactory a IServiceProvider e chiamando il metodo GetService. Il provider EF è strettamente associato al DbProviderFactory. Questo accoppiamento impedisce che EF venga spostato fuori dalla .NET Framework e pertanto per EF6 questo accoppiamento stretto è stato rimosso e un'implementazione di DbProviderServices è stata registrata direttamente nel file di configurazione dell'applicazione o nella configurazione basata su codice, come descritto in dettaglio nella sezione relativa alla _registrazione di DbProviderServices_ riportata di seguito.

## <a name="additional-services"></a>Servizi aggiuntivi

Oltre ai servizi fondamentali descritti in precedenza, esistono anche molti altri servizi usati da Entity Framework che sono sempre o a volte specifici del provider. Le implementazioni specifiche del provider predefinite di questi servizi possono essere fornite da un'implementazione di DbProviderServices. Le applicazioni possono anche eseguire l'override delle implementazioni di questi servizi o fornire implementazioni quando un tipo DbProviderServices non fornisce un valore predefinito. Questa procedura viene descritta più dettagliatamente nella sezione _risoluzione dei servizi aggiuntivi_ riportata di seguito.

Di seguito sono elencati i tipi di servizio aggiuntivi che un provider può essere di interesse per un provider. Altre informazioni su ognuno di questi tipi di servizio sono disponibili nella documentazione dell'API.

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

Si tratta di un servizio facoltativo che consente a un provider di implementare nuovi tentativi o un altro comportamento quando le query e i comandi vengono eseguiti sul database. Se non viene fornita alcuna implementazione, EF eseguirà semplicemente i comandi e propagherà le eccezioni generate. Per SQL Server questo servizio viene usato per fornire un criterio di ripetizione dei tentativi particolarmente utile quando viene eseguito su server di database basati su cloud, ad esempio SQL Azure.

### <a name="idbconnectionfactory"></a>IDbConnectionFactory

Si tratta di un servizio facoltativo che consente a un provider di creare oggetti DbConnection per convenzione quando viene fornito solo un nome di database. Si noti che, sebbene questo servizio possa essere risolto da un'implementazione di DbProviderServices, è presente da EF 4,1 e può anche essere impostato in modo esplicito nel file di configurazione o nel codice. Il provider avrà la possibilità di risolvere questo servizio solo se è stato registrato come provider predefinito (vedere _il provider predefinito_ di seguito) e se una factory di connessione predefinita non è stata impostata altrove.

### <a name="dbspatialservices"></a>DbSpatialServices

Si tratta di servizi facoltativi che consentono a un provider di aggiungere il supporto per i tipi spaziali geography e Geometry. Per consentire a un'applicazione di usare EF con i tipi spaziali, è necessario fornire un'implementazione di questo servizio. DbSptialServices viene richiesta in due modi. Per prima cosa, i servizi spaziali specifici del provider vengono richiesti usando un oggetto DbProviderInfo, che contiene il nome invariante e il token del manifesto, come chiave. In secondo luogo, è possibile chiedere a DbSpatialServices senza chiave. Viene usato per risolvere il "provider spaziale globale" usato per la creazione di tipi DbGeography o DbGeometry autonomi.

### <a name="migrationsqlgenerator"></a>MigrationSqlGenerator

Si tratta di un servizio facoltativo che consente di usare le migrazioni di Entity Framework per la generazione di SQL usata per la creazione e la modifica di schemi di database per Code First. Per supportare le migrazioni, è necessaria un'implementazione. Se viene fornita un'implementazione di, questa verrà utilizzata anche quando i database vengono creati utilizzando inizializzatori di database o il metodo database. Create.

### <a name="funcdbconnection-string-historycontextfactory"></a>Func<DbConnection, String, HistoryContextFactory>

Si tratta di un servizio facoltativo che consente a un provider di configurare il mapping di HistoryContext alla `__MigrationHistory` tabella usata dalle migrazioni di EF. HistoryContext è un Code First DbContext e può essere configurato usando l'API Fluent normale per modificare elementi quali il nome della tabella e le specifiche di mapping delle colonne. L'implementazione predefinita di questo servizio restituita da EF per tutti i provider può funzionare per un determinato server di database se tutti i mapping predefiniti di tabelle e colonne sono supportati da tale provider. In tal caso, il provider non deve fornire un'implementazione di questo servizio.

### <a name="idbproviderfactoryresolver"></a>IDbProviderFactoryResolver

Si tratta di un servizio facoltativo per ottenere il DbProviderFactory corretto da un oggetto DbConnection specificato. L'implementazione predefinita di questo servizio restituito da EF per tutti i provider è progettata per funzionare per tutti i provider. Tuttavia, quando si esegue in .NET 4, DbProviderFactory non è accessibile pubblicamente da uno se DbConnections. Quindi, EF usa alcune euristiche per cercare i provider registrati per trovare una corrispondenza. È possibile che per alcuni provider questi sistemi euristici non riusciranno e in tali situazioni il provider deve fornire una nuova implementazione.

## <a name="registering-dbproviderservices"></a>Registrazione di DbProviderServices

L'implementazione di DbProviderServices da usare può essere registrata nel file di configurazione dell'applicazione (app.config o web.config) o usando la configurazione basata su codice. In entrambi i casi, la registrazione utilizza come chiave il "nome invariante" del provider. In questo modo è possibile registrare e utilizzare più provider in un'unica applicazione. Il nome invariante usato per le registrazioni EF corrisponde al nome invariante usato per la registrazione del provider ADO.NET e per le stringhe di connessione. Ad esempio, per SQL Server viene usato il nome invariante "System. Data. SqlClient".

### <a name="config-file-registration"></a>Registrazione nel file di configurazione

Il tipo di DbProviderServices da usare è registrato come elemento provider nell'elenco provider della sezione entityFramework del file di configurazione dell'applicazione. Esempio:

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

La stringa di _tipo_ deve corrispondere al nome di tipo completo dell'assembly dell'implementazione di DbProviderServices da usare.

### <a name="code-based-registration"></a>Registrazione basata su codice

A partire da i provider EF6 possono essere registrati anche usando il codice. Questo consente l'uso di un provider EF senza alcuna modifica al file di configurazione dell'applicazione. Per usare la configurazione basata su codice, un'applicazione deve creare una classe DbConfiguration come descritto nella [documentazione sulla configurazione basata su codice](https://msdn.com/data/jj680699). Il costruttore della classe DbConfiguration deve quindi chiamare SetProviderServices per registrare il provider EF. Esempio:

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>Risoluzione di servizi aggiuntivi

Come indicato in precedenza nella sezione _Panoramica dei tipi di provider_ , è possibile usare una classe DbProviderServices anche per risolvere servizi aggiuntivi. Questo è possibile perché DbProviderServices implementa IDbDependencyResolver e ogni tipo di DbProviderServices registrato viene aggiunto come "resolver predefinito". Il meccanismo IDbDpendencyResolver viene descritto più dettagliatamente nella [risoluzione delle dipendenze](xref:ef6/fundamentals/configuring/dependency-resolution). Non è tuttavia necessario comprendere tutti i concetti in questa specifica per risolvere servizi aggiuntivi in un provider.

Il modo più comune per la risoluzione dei servizi aggiuntivi da parte di un provider consiste nel chiamare DbProviderServices. AddDependencyResolver per ogni servizio nel costruttore della classe DbProviderServices. Ad esempio, SqlProviderServices (il provider EF per SQL Server) presenta codice simile al seguente per l'inizializzazione:

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

Questo costruttore usa le classi helper seguenti:

*   SingletonDependencyResolver: fornisce un modo semplice per risolvere i servizi singleton, ovvero i servizi per i quali viene restituita la stessa istanza ogni volta che viene chiamato GetService. I servizi temporanei vengono spesso registrati come una factory singleton che verrà usata per creare istanze temporanee su richiesta.
*   ExecutionStrategyResolver: un resolver specifico per la restituzione di implementazioni di IExecutionStrategy.

Anziché usare DbProviderServices. AddDependencyResolver, è anche possibile eseguire l'override di DbProviderServices. GetService e risolvere direttamente i servizi aggiuntivi. Questo metodo verrà chiamato quando EF necessita di un servizio definito da un determinato tipo e, in alcuni casi, per una chiave specificata. Il metodo deve restituire il servizio se possibile oppure restituire null per rifiutare esplicitamente la restituzione del servizio e consentire invece a un'altra classe di risolverlo. Ad esempio, per risolvere la factory di connessione predefinita, il codice in GetService potrebbe avere un aspetto simile al seguente:

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>Ordine di registrazione

Quando più implementazioni di DbProviderServices vengono registrate nel file di configurazione di un'applicazione, verranno aggiunte come resolver secondari nell'ordine in cui sono elencate. Poiché i resolver vengono sempre aggiunti all'inizio della catena del sistema di risoluzione dei conflitti, questo significa che il provider alla fine dell'elenco potrà avere la possibilità di risolvere le dipendenze prima degli altri. Questa operazione può sembrare poco intuitiva, ma ha senso se si immagina di estrarre ogni provider dall'elenco e di impilarlo in base ai provider esistenti.

Questo ordinamento in genere non è rilevante perché la maggior parte dei servizi provider sono specifici del provider e hanno una chiave per nome invariante del provider. Tuttavia, per i servizi che non sono codificati dal nome invariante del provider o da altre chiavi specifiche del provider, il servizio verrà risolto in base a questo ordinamento. Se, ad esempio, non viene impostata in modo esplicito in un altro punto, la factory di connessione predefinita proverrà dal provider più in alto nella catena.

## <a name="additional-config-file-registrations"></a>Registrazioni aggiuntive per i file di configurazione

È possibile registrare in modo esplicito alcuni dei servizi provider aggiuntivi descritti sopra direttamente nel file di configurazione di un'applicazione. Quando questa operazione viene eseguita, verrà usata la registrazione nel file di configurazione anziché qualsiasi elemento restituito dal metodo GetService dell'implementazione di DbProviderServices.

### <a name="registering-the-default-connection-factory"></a>Registrazione della factory di connessione predefinita

A partire da EF5 il pacchetto NuGet EntityFramework ha registrato automaticamente la factory di connessione SQL Express o la factory di connessione del database locale nel file di configurazione.

Esempio:

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

Il _tipo_ è il nome del tipo qualificato dall'assembly per la factory di connessione predefinita, che deve implementare IDbConnectionFactory.

Quando si installa, è consigliabile che un pacchetto NuGet del provider imposti la factory di connessione predefinita in questo modo. Vedere _pacchetti NuGet per i provider di_ seguito.

## <a name="additional-ef6-provider-changes"></a>Modifiche aggiuntive del provider EF6

### <a name="spatial-provider-changes"></a>Modifiche del provider spaziale

I provider che supportano i tipi spaziali devono ora implementare alcuni metodi aggiuntivi sulle classi che derivano da DbSpatialDataReader:

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

Sono inoltre disponibili nuove versioni asincrone dei metodi esistenti che è consigliabile sottoporre a override come le implementazioni predefinite delegate ai metodi sincroni e pertanto non vengono eseguite in modo asincrono:

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>Supporto nativo per Enumerable. Contains

EF6 introduce un nuovo tipo di espressione, DbInExpression, che è stato aggiunto per risolvere i problemi di prestazioni relativi all'utilizzo di Enumerable. Contains nelle query LINQ. La classe DbProviderManifest dispone di un nuovo metodo virtuale, SupportsInExpression, che viene chiamato da EF per determinare se un provider gestisce il nuovo tipo di espressione. Per la compatibilità con le implementazioni del provider esistenti, il metodo restituisce false. Per trarre vantaggio da questo miglioramento, un provider EF6 può aggiungere codice per gestire DbInExpression ed eseguire l'override di SupportsInExpression per restituire true. È possibile creare un'istanza di DbInExpression chiamando il Metodo DbExpressionBuilder.In. Un'istanza di DbInExpression è costituita da un DbExpression, che in genere rappresenta una colonna della tabella, e da un elenco di DbConstantExpression per verificare la corrispondenza.

## <a name="nuget-packages-for-providers"></a>Pacchetti NuGet per i provider

Un modo per rendere disponibile un provider EF6 consiste nel rilasciarlo come pacchetto NuGet. L'uso di un pacchetto NuGet presenta i vantaggi seguenti:

*   È facile usare NuGet per aggiungere la registrazione del provider al file di configurazione dell'applicazione
*   È possibile apportare ulteriori modifiche al file di configurazione per impostare la factory di connessione predefinita in modo che le connessioni effettuate per convenzione usino il provider registrato
*   NuGet gestisce l'aggiunta di reindirizzamenti di binding in modo che il provider EF6 continui a funzionare anche dopo il rilascio di un nuovo pacchetto EF

Un esempio è il pacchetto EntityFramework. SqlServerCompact incluso nella [codebase Open Source](https://github.com/aspnet/entityframework6). Questo pacchetto fornisce un modello valido per la creazione di pacchetti NuGet del provider EF.

### <a name="powershell-commands"></a>Comandi di PowerShell

Quando viene installato il pacchetto NuGet EntityFramework, viene registrato un modulo di PowerShell che contiene due comandi che sono molto utili per i pacchetti del provider:

*   Add-EFProvider aggiunge una nuova entità per il provider nel file di configurazione del progetto di destinazione e verifica che sia alla fine dell'elenco dei provider registrati.
*   Add-EFDefaultConnectionFactory aggiunge o aggiorna la registrazione defaultConnectionFactory nel file di configurazione del progetto di destinazione.

Entrambi questi comandi si occupano di aggiungere una sezione entityFramework al file di configurazione e di aggiungere una raccolta Providers, se necessario.

È previsto che questi comandi vengano chiamati dallo script install.ps1 NuGet. Ad esempio, install.ps1 per il provider SQL Compact è simile a quanto segue:

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

Altre informazioni su questi comandi possono essere ottenute usando Get-Help nella finestra della console di gestione pacchetti.

## <a name="wrapping-providers"></a>Wrapping di provider

Un provider di wrapping è un provider EF e/o ADO.NET che esegue il wrapping di un provider esistente per estenderlo con altre funzionalità, ad esempio la profilatura o la traccia. I provider di wrapping possono essere registrati in modo normale, ma spesso è più comodo configurare il provider di wrapping in fase di esecuzione intercettando la risoluzione dei servizi correlati al provider. Per eseguire questa operazione, è possibile usare l'evento statico OnLockingConfiguration nella classe DbConfiguration.

OnLockingConfiguration viene chiamato dopo che Entity Framework ha determinato la posizione in cui verrà ottenuta la configurazione di EF per il dominio dell'applicazione, ma prima che venga bloccata per l'uso. All'avvio dell'app (prima che venga usato EF) l'app deve registrare un gestore eventi per questo evento. Si sta valutando l'aggiunta del supporto per la registrazione di questo gestore nel file di configurazione, ma questa operazione non è ancora supportata. Il gestore eventi deve quindi effettuare una chiamata a ReplaceService per ogni servizio di cui è necessario eseguire il wrapper.  

Ad esempio, per eseguire il wrapping di IDbConnectionFactory e DbProviderService, è necessario registrare un gestore simile al seguente:

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

Il servizio che è stato risolto e a questo punto dovrebbe essere incluso con la chiave usata per risolvere il servizio vengono passati al gestore. Il gestore può quindi eseguire il wrapping di questo servizio e sostituire il servizio restituito con la versione di cui è stato eseguito il wrapping.

## <a name="resolving-a-dbproviderfactory-with-ef"></a>Risoluzione di un DbProviderFactory con EF

DbProviderFactory è uno dei tipi di provider fondamentali richiesti da EF, come descritto nella sezione _Panoramica dei tipi di provider_ . Come già indicato, non si tratta di un tipo EF e la registrazione non è in genere parte della configurazione di Entity Framework, ma è invece la registrazione del provider ADO.NET normale nel file di machine.config e/o nel file di configurazione dell'applicazione.

Nonostante questo EF usi ancora il meccanismo di risoluzione delle dipendenze normale quando si cerca un DbProviderFactory da usare. Il sistema di risoluzione predefinito usa la registrazione ADO.NET normale nei file di configurazione, quindi questo è in genere trasparente. Tuttavia, a causa del normale meccanismo di risoluzione delle dipendenze, significa che è possibile usare un IDbDependencyResolver per risolvere un DbProviderFactory anche quando non è stata eseguita la registrazione normale di ADO.NET.

La risoluzione di DbProviderFactory in questo modo presenta diverse implicazioni:

*   Un'applicazione che usa la configurazione basata su codice può aggiungere chiamate nella classe DbConfiguration per registrare il DbProviderFactory appropriato. Questa operazione è particolarmente utile per le applicazioni che non vogliono (o non possono) usare qualsiasi configurazione basata su file.
*   Il servizio può essere sottoposto a wrapping o sostituito usando ReplaceService, come descritto nella sezione relativa al _wrapping dei provider_ precedente
*   In teoria, un'implementazione di DbProviderServices potrebbe risolvere un DbProviderFactory.

Il punto importante da considerare per eseguire queste operazioni è che influiscono solo sulla ricerca di DbProviderFactory da parte di EF. Un altro codice non EF potrebbe ancora prevedere la registrazione del provider ADO.NET in modo normale e potrebbe non riuscire se la registrazione non viene trovata. Per questo motivo, è in genere preferibile che un DbProviderFactory venga registrato nel modo normale di ADO.NET.

### <a name="related-services"></a>Servizi correlati

Se EF viene usato per risolvere un DbProviderFactory, deve anche risolvere i servizi IProviderInvariantName e IDbProviderFactoryResolver.

IProviderInvariantName è un servizio utilizzato per determinare un nome invariante del provider per un determinato tipo di DbProviderFactory. L'implementazione predefinita di questo servizio usa la registrazione del provider ADO.NET. Ciò significa che se il provider ADO.NET non è registrato in modo normale perché DbProviderFactory viene risolto da EF, sarà necessario anche risolvere questo servizio. Si noti che un resolver per questo servizio viene aggiunto automaticamente quando si usa il metodo DbConfiguration. SetProviderFactory.

Come descritto nella sezione _Panoramica dei tipi di provider_ , IDbProviderFactoryResolver viene usato per ottenere il DbProviderFactory corretto da un oggetto DbConnection specificato. L'implementazione predefinita di questo servizio durante l'esecuzione in .NET 4 usa la registrazione del provider ADO.NET. Ciò significa che se il provider ADO.NET non è registrato in modo normale perché DbProviderFactory viene risolto da EF, sarà necessario anche risolvere questo servizio.
