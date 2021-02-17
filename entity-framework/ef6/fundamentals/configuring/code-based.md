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
# <a name="code-based-configuration"></a><span data-ttu-id="1200e-103">Configurazione basata su codice</span><span class="sxs-lookup"><span data-stu-id="1200e-103">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="1200e-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="1200e-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1200e-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="1200e-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1200e-106">La configurazione di un'applicazione Entity Framework può essere specificata in un file di configurazione (app.config/web.config) o tramite codice.</span><span class="sxs-lookup"><span data-stu-id="1200e-106">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="1200e-107">Quest'ultima è nota come configurazione basata su codice.</span><span class="sxs-lookup"><span data-stu-id="1200e-107">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="1200e-108">La configurazione in un file di configurazione è descritta in un [articolo separato](xref:ef6/fundamentals/configuring/config-file).</span><span class="sxs-lookup"><span data-stu-id="1200e-108">Configuration in a config file is described in a [separate article](xref:ef6/fundamentals/configuring/config-file).</span></span> <span data-ttu-id="1200e-109">Il file di configurazione ha la precedenza sulla configurazione basata su codice.</span><span class="sxs-lookup"><span data-stu-id="1200e-109">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="1200e-110">In altre parole, se un'opzione di configurazione è impostata nel codice e nel file di configurazione, viene utilizzata l'impostazione nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="1200e-110">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="1200e-111">Uso di `DbConfiguration`</span><span class="sxs-lookup"><span data-stu-id="1200e-111">Using `DbConfiguration`</span></span>

<span data-ttu-id="1200e-112">La configurazione basata su codice in EF6 e versioni successive viene eseguita creando una sottoclasse di `System.Data.Entity.Config.DbConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="1200e-112">Code-based configuration in EF6 and above is achieved by creating a subclass of `System.Data.Entity.Config.DbConfiguration`.</span></span> <span data-ttu-id="1200e-113">Quando si crea una sottoclasse, è necessario seguire le linee guida seguenti `DbConfiguration` :</span><span class="sxs-lookup"><span data-stu-id="1200e-113">The following guidelines should be followed when subclassing `DbConfiguration`:</span></span>  

- <span data-ttu-id="1200e-114">Creare una sola `DbConfiguration` classe per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1200e-114">Create only one `DbConfiguration` class for your application.</span></span> <span data-ttu-id="1200e-115">Questa classe specifica le impostazioni a livello di dominio applicazione.</span><span class="sxs-lookup"><span data-stu-id="1200e-115">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="1200e-116">Inserire la `DbConfiguration` classe nello stesso assembly della `DbContext` classe.</span><span class="sxs-lookup"><span data-stu-id="1200e-116">Place your `DbConfiguration` class in the same assembly as your `DbContext` class.</span></span> <span data-ttu-id="1200e-117">(Per modificare questa operazione, vedere la sezione in cui si sta *muovendo `DbConfiguration`* ).</span><span class="sxs-lookup"><span data-stu-id="1200e-117">(See the *Moving `DbConfiguration`* section if you want to change this.)</span></span>  
- <span data-ttu-id="1200e-118">Assegnare alla `DbConfiguration` classe un costruttore pubblico senza parametri.</span><span class="sxs-lookup"><span data-stu-id="1200e-118">Give your `DbConfiguration` class a public parameterless constructor.</span></span>  
- <span data-ttu-id="1200e-119">Impostare le opzioni di configurazione chiamando `DbConfiguration` metodi protetti dall'interno del costruttore.</span><span class="sxs-lookup"><span data-stu-id="1200e-119">Set configuration options by calling protected `DbConfiguration` methods from within this constructor.</span></span>  

<span data-ttu-id="1200e-120">Seguendo queste linee guida è possibile individuare e utilizzare automaticamente la propria configurazione mediante gli strumenti necessari per accedere al modello e quando viene eseguita l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="1200e-120">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="1200e-121">Esempio</span><span class="sxs-lookup"><span data-stu-id="1200e-121">Example</span></span>  

<span data-ttu-id="1200e-122">Una classe derivata da `DbConfiguration` potrebbe essere simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="1200e-122">A class derived from `DbConfiguration` might look like this:</span></span>  

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

<span data-ttu-id="1200e-123">Questa classe configura EF per l'uso della strategia di esecuzione SQL Azure: per ritentare automaticamente le operazioni di database non riuscite e per usare il database locale per i database creati per convenzione da Code First.</span><span class="sxs-lookup"><span data-stu-id="1200e-123">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="1200e-124">Movimento `DbConfiguration`</span><span class="sxs-lookup"><span data-stu-id="1200e-124">Moving `DbConfiguration`</span></span>  

<span data-ttu-id="1200e-125">In alcuni casi non è possibile inserire la `DbConfiguration` classe nello stesso assembly della `DbContext` classe.</span><span class="sxs-lookup"><span data-stu-id="1200e-125">There are cases where it is not possible to place your `DbConfiguration` class in the same assembly as your `DbContext` class.</span></span> <span data-ttu-id="1200e-126">Ad esempio, è possibile avere due `DbContext` classi ognuna in assembly diversi.</span><span class="sxs-lookup"><span data-stu-id="1200e-126">For example, you may have two `DbContext` classes each in different assemblies.</span></span> <span data-ttu-id="1200e-127">Sono disponibili due opzioni per la gestione di questo.</span><span class="sxs-lookup"><span data-stu-id="1200e-127">There are two options for handling this.</span></span>  

<span data-ttu-id="1200e-128">La prima opzione consiste nell'utilizzare il file di configurazione per specificare l' `DbConfiguration` istanza da utilizzare.</span><span class="sxs-lookup"><span data-stu-id="1200e-128">The first option is to use the config file to specify the `DbConfiguration` instance to use.</span></span> <span data-ttu-id="1200e-129">A tale scopo, impostare l'attributo codeConfigurationType della sezione entityFramework.</span><span class="sxs-lookup"><span data-stu-id="1200e-129">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="1200e-130">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1200e-130">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="1200e-131">Il valore di codeConfigurationType deve essere il nome completo dell'assembly e dello spazio dei nomi della `DbConfiguration` classe.</span><span class="sxs-lookup"><span data-stu-id="1200e-131">The value of codeConfigurationType must be the assembly and namespace qualified name of your `DbConfiguration` class.</span></span>  

<span data-ttu-id="1200e-132">La seconda opzione consiste nel posizionare la `DbConfigurationTypeAttribute` classe del contesto.</span><span class="sxs-lookup"><span data-stu-id="1200e-132">The second option is to place `DbConfigurationTypeAttribute` on your context class.</span></span> <span data-ttu-id="1200e-133">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1200e-133">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="1200e-134">Il valore passato all'attributo può essere il `DbConfiguration` tipo, come illustrato sopra, oppure la stringa del nome del tipo completo di assembly e spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="1200e-134">The value passed to the attribute can either be your `DbConfiguration` type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="1200e-135">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1200e-135">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="1200e-136">Impostazione `DbConfiguration` esplicita</span><span class="sxs-lookup"><span data-stu-id="1200e-136">Setting `DbConfiguration` explicitly</span></span>  

<span data-ttu-id="1200e-137">Ci sono alcune situazioni in cui potrebbe essere necessario eseguire la configurazione prima di `DbContext` utilizzare qualsiasi tipo.</span><span class="sxs-lookup"><span data-stu-id="1200e-137">There are some situations where configuration may be needed before any `DbContext` type has been used.</span></span> <span data-ttu-id="1200e-138">Di seguito sono riportati alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="1200e-138">Examples of this include:</span></span>  

- <span data-ttu-id="1200e-139">Utilizzo `DbModelBuilder` di per la compilazione di un modello senza un contesto</span><span class="sxs-lookup"><span data-stu-id="1200e-139">Using `DbModelBuilder` to build a model without a context</span></span>  
- <span data-ttu-id="1200e-140">Utilizzando un altro codice di Framework/utilità che utilizza un oggetto in `DbContext` cui tale contesto viene utilizzato prima che venga utilizzato il contesto dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="1200e-140">Using some other framework/utility code that utilizes a `DbContext` where that context is used before your application context is used</span></span>  

<span data-ttu-id="1200e-141">In questi casi EF non è in grado di individuare automaticamente la configurazione ed è invece necessario eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1200e-141">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="1200e-142">Impostare il `DbConfiguration` tipo nel file di configurazione, come descritto nella *`DbConfiguration`* sezione precedente.</span><span class="sxs-lookup"><span data-stu-id="1200e-142">Set the `DbConfiguration` type in the config file, as described in the *Moving `DbConfiguration`* section above</span></span>
- <span data-ttu-id="1200e-143">Chiamare il metodo statico `DbConfiguration` . Metodo di configurazione durante l'avvio dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="1200e-143">Call the static `DbConfiguration`.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="1200e-144">Override `DbConfiguration`</span><span class="sxs-lookup"><span data-stu-id="1200e-144">Overriding `DbConfiguration`</span></span>  

<span data-ttu-id="1200e-145">In alcune situazioni è necessario eseguire l'override del set di configurazione in `DbConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="1200e-145">There are some situations where you need to override the configuration set in the `DbConfiguration`.</span></span> <span data-ttu-id="1200e-146">Questa operazione non viene in genere eseguita dagli sviluppatori di applicazioni, bensì da provider di terze parti e plug-in che non possono usare una `DbConfiguration` classe derivata.</span><span class="sxs-lookup"><span data-stu-id="1200e-146">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived `DbConfiguration` class.</span></span>  

<span data-ttu-id="1200e-147">A tale proposito, EntityFramework consente la registrazione di un gestore eventi in grado di modificare la configurazione esistente appena prima del blocco.</span><span class="sxs-lookup"><span data-stu-id="1200e-147">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="1200e-148">Fornisce inoltre un metodo Sugar specifico per la sostituzione di tutti i servizi restituiti dal localizzatore di servizi EF.</span><span class="sxs-lookup"><span data-stu-id="1200e-148">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="1200e-149">Questo è il modo in cui è concepito per l'uso:</span><span class="sxs-lookup"><span data-stu-id="1200e-149">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="1200e-150">All'avvio dell'app (prima che venga usato EF) il plug-in o il provider deve registrare il metodo del gestore eventi per questo evento.</span><span class="sxs-lookup"><span data-stu-id="1200e-150">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="1200e-151">Si noti che questa operazione deve verificarsi prima che l'applicazione usi EF.</span><span class="sxs-lookup"><span data-stu-id="1200e-151">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="1200e-152">Il gestore eventi effettua una chiamata a ReplaceService per ogni servizio che deve essere sostituito.</span><span class="sxs-lookup"><span data-stu-id="1200e-152">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="1200e-153">Ad esempio, per sostituire `IDbConnectionFactory` ed `DbProviderService` è necessario registrare un gestore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="1200e-153">For example, to replace `IDbConnectionFactory` and `DbProviderService` you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="1200e-154">Nel codice precedente `MyProviderServices` e `MyConnectionFactory` rappresentano le implementazioni del servizio.</span><span class="sxs-lookup"><span data-stu-id="1200e-154">In the code above, `MyProviderServices` and `MyConnectionFactory` represent your implementations of the service.</span></span>  

<span data-ttu-id="1200e-155">È anche possibile aggiungere altri gestori di dipendenza per ottenere lo stesso effetto.</span><span class="sxs-lookup"><span data-stu-id="1200e-155">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="1200e-156">Si noti che è anche possibile `DbProviderFactory` eseguire il wrapping in questo modo, ma in questo modo si influirà solo su Entity Framework e non sull'uso di Entity Framework `DbProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="1200e-156">Note that you could also wrap `DbProviderFactory` in this way, but doing so will only affect EF and not uses of the `DbProviderFactory` outside of EF.</span></span> <span data-ttu-id="1200e-157">Per questo motivo è probabile che si voglia continuare a eseguire `DbProviderFactory` il wrapping come in precedenza.</span><span class="sxs-lookup"><span data-stu-id="1200e-157">For this reason you’ll probably want to continue to wrap `DbProviderFactory` as you have before.</span></span>  

<span data-ttu-id="1200e-158">È inoltre necessario tenere presente i servizi eseguiti esternamente all'applicazione, ad esempio quando si eseguono migrazioni dalla console di gestione pacchetti.</span><span class="sxs-lookup"><span data-stu-id="1200e-158">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="1200e-159">Quando si esegue la migrazione dalla console, si tenterà di trovare il `DbConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="1200e-159">When you run migrate from the console it will attempt to find your `DbConfiguration`.</span></span> <span data-ttu-id="1200e-160">Tuttavia, indipendentemente dal fatto che ottenga il servizio di cui è stato eseguito il Wrapped, dipende dalla posizione in cui è stato registrato il gestore eventi.</span><span class="sxs-lookup"><span data-stu-id="1200e-160">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="1200e-161">Se viene registrato come parte della costruzione del, `DbConfiguration` il codice deve essere eseguito e il servizio dovrebbe essere incapsulato.</span><span class="sxs-lookup"><span data-stu-id="1200e-161">If it is registered as part of the construction of your `DbConfiguration` then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="1200e-162">In genere questo non è il caso e questo significa che gli strumenti non ottengono il servizio di cui è stato eseguito il Wrapped.</span><span class="sxs-lookup"><span data-stu-id="1200e-162">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
