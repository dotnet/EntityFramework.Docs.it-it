---
title: Configurazione basata su codice-EF6
description: Configurazione basata su codice in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 67bb7ebd620c90ebe80983cc5baa6cab032907f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070731"
---
# <a name="code-based-configuration"></a>Configurazione basata su codice
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

La configurazione di un'applicazione Entity Framework può essere specificata in un file di configurazione (app.config/web.config) o tramite codice. Quest'ultima è nota come configurazione basata su codice.  

La configurazione in un file di configurazione è descritta in un [articolo separato](xref:ef6/fundamentals/configuring/config-file). Il file di configurazione ha la precedenza sulla configurazione basata su codice. In altre parole, se un'opzione di configurazione è impostata nel codice e nel file di configurazione, viene utilizzata l'impostazione nel file di configurazione.  

## <a name="using-dbconfiguration"></a>Uso di DbConfiguration  

La configurazione basata su codice in EF6 e versioni successive viene eseguita creando una sottoclasse di System.Data.Entity.Config. DbConfiguration. Quando si crea una sottoclasse di DbConfiguration, è necessario seguire le linee guida seguenti:  

- Creare una sola classe DbConfiguration per l'applicazione. Questa classe specifica le impostazioni a livello di dominio applicazione.  
- Inserire la classe DbConfiguration nello stesso assembly della classe DbContext. Per modificare questa sezione, vedere la sezione relativa allo stato di *DbConfiguration* .  
- Assegnare alla classe DbConfiguration un costruttore pubblico senza parametri.  
- Impostare le opzioni di configurazione chiamando i metodi DbConfiguration protetti dall'interno del costruttore.  

Seguendo queste linee guida è possibile individuare e utilizzare automaticamente la propria configurazione mediante gli strumenti necessari per accedere al modello e quando viene eseguita l'applicazione.  

## <a name="example"></a>Esempio  

Una classe derivata da DbConfiguration potrebbe essere simile alla seguente:  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

Questa classe configura EF per l'uso della strategia di esecuzione SQL Azure: per ritentare automaticamente le operazioni di database non riuscite e per usare il database locale per i database creati per convenzione da Code First.  

## <a name="moving-dbconfiguration"></a>Trasferimento di DbConfiguration  

In alcuni casi non è possibile inserire la classe DbConfiguration nello stesso assembly della classe DbContext. Ad esempio, è possibile avere due classi DbContext in assembly diversi. Sono disponibili due opzioni per la gestione di questo.  

La prima opzione consiste nell'usare il file di configurazione per specificare l'istanza di DbConfiguration da usare. A tale scopo, impostare l'attributo codeConfigurationType della sezione entityFramework. Esempio:  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

Il valore di codeConfigurationType deve essere il nome completo dell'assembly e dello spazio dei nomi della classe DbConfiguration.  

La seconda opzione consiste nell'inserire DbConfigurationTypeAttribute nella classe del contesto. Esempio:  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

Il valore passato all'attributo può essere il tipo DbConfiguration, come illustrato sopra, oppure la stringa del nome del tipo completo di assembly e spazio dei nomi. Esempio:  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>Impostazione esplicita di DbConfiguration  

In alcune situazioni è possibile che sia necessaria la configurazione prima di utilizzare qualsiasi tipo di DbContext. Di seguito sono riportati alcuni esempi:  

- Utilizzo di DbModelBuilder per compilare un modello senza un contesto  
- Utilizzando un altro codice di Framework/utilità che utilizza un DbContext in cui tale contesto viene utilizzato prima che venga utilizzato il contesto dell'applicazione  

In questi casi EF non è in grado di individuare automaticamente la configurazione ed è invece necessario eseguire una delle operazioni seguenti:  

- Impostare il tipo DbConfiguration nel file di configurazione, come descritto nella sezione relativa al *passaggio di DbConfiguration* precedente
- Chiamare il metodo statico DbConfiguration. seconfigurazione durante l'avvio dell'applicazione  

## <a name="overriding-dbconfiguration"></a>Override di DbConfiguration  

In alcune situazioni è necessario eseguire l'override del set di configurazione in DbConfiguration. Questa operazione non viene in genere eseguita dagli sviluppatori di applicazioni, bensì da provider di terze parti e plug-in che non possono usare una classe DbConfiguration derivata.  

A tale proposito, EntityFramework consente la registrazione di un gestore eventi in grado di modificare la configurazione esistente appena prima del blocco.  Fornisce inoltre un metodo Sugar specifico per la sostituzione di tutti i servizi restituiti dal localizzatore di servizi EF. Questo è il modo in cui è concepito per l'uso:  

- All'avvio dell'app (prima che venga usato EF) il plug-in o il provider deve registrare il metodo del gestore eventi per questo evento. Si noti che questa operazione deve verificarsi prima che l'applicazione usi EF.  
- Il gestore eventi effettua una chiamata a ReplaceService per ogni servizio che deve essere sostituito.  

Per sostituire IDbConnectionFactory e DbProviderService, ad esempio, è necessario registrare un gestore simile al seguente:  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

Nel codice precedente MyProviderServices e MyConnectionFactory rappresentano le implementazioni del servizio.  

È anche possibile aggiungere altri gestori di dipendenza per ottenere lo stesso effetto.  

Si noti che è anche possibile eseguire il wrapping di DbProviderFactory in questo modo, ma in questo modo si influirà solo su Entity Framework e non sugli usi di DbProviderFactory al di fuori di EF. Per questo motivo è probabile che si voglia continuare a eseguire il wrapping di DbProviderFactory in precedenza.  

È inoltre necessario tenere presente i servizi eseguiti esternamente all'applicazione, ad esempio quando si eseguono migrazioni dalla console di gestione pacchetti. Quando si esegue la migrazione dalla console, si tenterà di trovare la DbConfiguration. Tuttavia, indipendentemente dal fatto che ottenga il servizio di cui è stato eseguito il Wrapped, dipende dalla posizione in cui è stato registrato il gestore eventi. Se viene registrato come parte della costruzione del DbConfiguration, il codice deve essere eseguito e il servizio dovrebbe essere incapsulato. In genere questo non è il caso e questo significa che gli strumenti non ottengono il servizio di cui è stato eseguito il Wrapped.  
