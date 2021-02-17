---
title: Configurazione basata su codice-EF6
description: Configurazione basata su codice in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: b16cbcef85708730dcc6b82a38635cc60cb2206a
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543172"
---
# <a name="code-based-configuration"></a>Configurazione basata su codice
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

La configurazione di un'applicazione Entity Framework può essere specificata in un file di configurazione (app.config/web.config) o tramite codice. Quest'ultima è nota come configurazione basata su codice.  

La configurazione in un file di configurazione è descritta in un [articolo separato](xref:ef6/fundamentals/configuring/config-file). Il file di configurazione ha la precedenza sulla configurazione basata su codice. In altre parole, se un'opzione di configurazione è impostata nel codice e nel file di configurazione, viene utilizzata l'impostazione nel file di configurazione.  

## <a name="using-dbconfiguration"></a>Uso di `DbConfiguration`

La configurazione basata su codice in EF6 e versioni successive viene eseguita creando una sottoclasse di `System.Data.Entity.Config.DbConfiguration` . Quando si crea una sottoclasse, è necessario seguire le linee guida seguenti `DbConfiguration` :  

- Creare una sola `DbConfiguration` classe per l'applicazione. Questa classe specifica le impostazioni a livello di dominio applicazione.  
- Inserire la `DbConfiguration` classe nello stesso assembly della `DbContext` classe. (Per modificare questa operazione, vedere la sezione in cui si sta *muovendo `DbConfiguration`* ).  
- Assegnare alla `DbConfiguration` classe un costruttore pubblico senza parametri.  
- Impostare le opzioni di configurazione chiamando `DbConfiguration` metodi protetti dall'interno del costruttore.  

Seguendo queste linee guida è possibile individuare e utilizzare automaticamente la propria configurazione mediante gli strumenti necessari per accedere al modello e quando viene eseguita l'applicazione.  

## <a name="example"></a>Esempio  

Una classe derivata da `DbConfiguration` potrebbe essere simile alla seguente:  

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

## <a name="moving-dbconfiguration"></a>Movimento `DbConfiguration`  

In alcuni casi non è possibile inserire la `DbConfiguration` classe nello stesso assembly della `DbContext` classe. Ad esempio, è possibile avere due `DbContext` classi ognuna in assembly diversi. Sono disponibili due opzioni per la gestione di questo.  

La prima opzione consiste nell'utilizzare il file di configurazione per specificare l' `DbConfiguration` istanza da utilizzare. A tale scopo, impostare l'attributo codeConfigurationType della sezione entityFramework. Ad esempio:  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

Il valore di codeConfigurationType deve essere il nome completo dell'assembly e dello spazio dei nomi della `DbConfiguration` classe.  

La seconda opzione consiste nel posizionare la `DbConfigurationTypeAttribute` classe del contesto. Ad esempio:  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

Il valore passato all'attributo può essere il `DbConfiguration` tipo, come illustrato sopra, oppure la stringa del nome del tipo completo di assembly e spazio dei nomi. Ad esempio:  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>Impostazione `DbConfiguration` esplicita  

Ci sono alcune situazioni in cui potrebbe essere necessario eseguire la configurazione prima di `DbContext` utilizzare qualsiasi tipo. Di seguito sono riportati alcuni esempi:  

- Utilizzo `DbModelBuilder` di per la compilazione di un modello senza un contesto  
- Utilizzando un altro codice di Framework/utilità che utilizza un oggetto in `DbContext` cui tale contesto viene utilizzato prima che venga utilizzato il contesto dell'applicazione  

In questi casi EF non è in grado di individuare automaticamente la configurazione ed è invece necessario eseguire una delle operazioni seguenti:  

- Impostare il `DbConfiguration` tipo nel file di configurazione, come descritto nella *`DbConfiguration`* sezione precedente.
- Chiamare il metodo statico `DbConfiguration` . Metodo di configurazione durante l'avvio dell'applicazione  

## <a name="overriding-dbconfiguration"></a>Override `DbConfiguration`  

In alcune situazioni è necessario eseguire l'override del set di configurazione in `DbConfiguration` . Questa operazione non viene in genere eseguita dagli sviluppatori di applicazioni, bensì da provider di terze parti e plug-in che non possono usare una `DbConfiguration` classe derivata.  

A tale proposito, EntityFramework consente la registrazione di un gestore eventi in grado di modificare la configurazione esistente appena prima del blocco.  Fornisce inoltre un metodo Sugar specifico per la sostituzione di tutti i servizi restituiti dal localizzatore di servizi EF. Questo è il modo in cui è concepito per l'uso:  

- All'avvio dell'app (prima che venga usato EF) il plug-in o il provider deve registrare il metodo del gestore eventi per questo evento. Si noti che questa operazione deve verificarsi prima che l'applicazione usi EF.  
- Il gestore eventi effettua una chiamata a ReplaceService per ogni servizio che deve essere sostituito.  

Ad esempio, per sostituire `IDbConnectionFactory` ed `DbProviderService` è necessario registrare un gestore simile al seguente:  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

Nel codice precedente `MyProviderServices` e `MyConnectionFactory` rappresentano le implementazioni del servizio.  

È anche possibile aggiungere altri gestori di dipendenza per ottenere lo stesso effetto.  

Si noti che è anche possibile `DbProviderFactory` eseguire il wrapping in questo modo, ma in questo modo si influirà solo su Entity Framework e non sull'uso di Entity Framework `DbProviderFactory` . Per questo motivo è probabile che si voglia continuare a eseguire `DbProviderFactory` il wrapping come in precedenza.  

È inoltre necessario tenere presente i servizi eseguiti esternamente all'applicazione, ad esempio quando si eseguono migrazioni dalla console di gestione pacchetti. Quando si esegue la migrazione dalla console, si tenterà di trovare il `DbConfiguration` . Tuttavia, indipendentemente dal fatto che ottenga il servizio di cui è stato eseguito il Wrapped, dipende dalla posizione in cui è stato registrato il gestore eventi. Se viene registrato come parte della costruzione del, `DbConfiguration` il codice deve essere eseguito e il servizio dovrebbe essere incapsulato. In genere questo non è il caso e questo significa che gli strumenti non ottengono il servizio di cui è stato eseguito il Wrapped.  
