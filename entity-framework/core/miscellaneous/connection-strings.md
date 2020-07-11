---
title: Stringhe di connessione-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e955e93723fc371170641b0b3209cca014ef1c26
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238151"
---
# <a name="connection-strings"></a><span data-ttu-id="8712a-102">Stringhe di connessione</span><span class="sxs-lookup"><span data-stu-id="8712a-102">Connection Strings</span></span>

<span data-ttu-id="8712a-103">La maggior parte dei provider di database richiede una forma di stringa di connessione per la connessione al database.</span><span class="sxs-lookup"><span data-stu-id="8712a-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="8712a-104">In alcuni casi la stringa di connessione contiene informazioni riservate che devono essere protette.</span><span class="sxs-lookup"><span data-stu-id="8712a-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="8712a-105">Potrebbe anche essere necessario modificare la stringa di connessione mentre si sposta l'applicazione tra ambienti, ad esempio sviluppo, test e produzione.</span><span class="sxs-lookup"><span data-stu-id="8712a-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="8712a-106">WinForms & applicazioni WPF</span><span class="sxs-lookup"><span data-stu-id="8712a-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="8712a-107">Le applicazioni WinForms, WPF e ASP.NET 4 hanno un modello di stringa di connessione provato e testato.</span><span class="sxs-lookup"><span data-stu-id="8712a-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="8712a-108">La stringa di connessione deve essere aggiunta al file di App.config dell'applicazione (Web.config se si usa ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="8712a-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="8712a-109">Se la stringa di connessione contiene informazioni riservate, ad esempio nome utente e password, è possibile proteggere il contenuto del file di configurazione utilizzando lo [strumento Gestione segreta](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="8712a-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> <span data-ttu-id="8712a-110">L' `providerName` impostazione non è necessaria per EF Core stringhe di connessione archiviate nel App.config perché il provider di database è configurato tramite codice.</span><span class="sxs-lookup"><span data-stu-id="8712a-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="8712a-111">È quindi possibile leggere la stringa di connessione usando l' `ConfigurationManager` API nel metodo del `OnConfiguring` contesto.</span><span class="sxs-lookup"><span data-stu-id="8712a-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="8712a-112">Per poter usare questa API, potrebbe essere necessario aggiungere un riferimento all' `System.Configuration` assembly del Framework.</span><span class="sxs-lookup"><span data-stu-id="8712a-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="8712a-113">Piattaforma UWP (Universal Windows Platform)</span><span class="sxs-lookup"><span data-stu-id="8712a-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="8712a-114">Le stringhe di connessione in un'applicazione UWP sono in genere una connessione SQLite che specifica solo un nome file locale.</span><span class="sxs-lookup"><span data-stu-id="8712a-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="8712a-115">In genere non contengono informazioni riservate e non devono essere modificate durante la distribuzione di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="8712a-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="8712a-116">Di conseguenza, queste stringhe di connessione sono in genere più belle da rimanere nel codice, come illustrato di seguito.</span><span class="sxs-lookup"><span data-stu-id="8712a-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="8712a-117">Se si vuole spostarli fuori dal codice, UWP supporta il concetto di impostazioni. per informazioni dettagliate, vedere la [sezione Impostazioni app della documentazione di UWP](/windows/uwp/app-settings/store-and-retrieve-app-data) .</span><span class="sxs-lookup"><span data-stu-id="8712a-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a><span data-ttu-id="8712a-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8712a-118">ASP.NET Core</span></span>

<span data-ttu-id="8712a-119">In ASP.NET Core il sistema di configurazione è molto flessibile e la stringa di connessione può essere archiviata in `appsettings.json` , una variabile di ambiente, l'archivio del segreto utente o un'altra origine della configurazione.</span><span class="sxs-lookup"><span data-stu-id="8712a-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="8712a-120">Per ulteriori informazioni, vedere la sezione relativa alla [configurazione della documentazione di ASP.NET Core](/aspnet/core/fundamentals/configuration) .</span><span class="sxs-lookup"><span data-stu-id="8712a-120">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="8712a-121">Ad esempio, è possibile usare lo [strumento di gestione dei segreti](/aspnet/core/security/app-secrets#secret-manager) per archiviare la password del database e quindi, nell'impalcatura, usare una stringa di connessione costituita semplicemente da `Name=<database-alias>` .</span><span class="sxs-lookup"><span data-stu-id="8712a-121">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="8712a-122">Oppure nell'esempio seguente viene illustrata la stringa di connessione archiviata in `appsettings.json` .</span><span class="sxs-lookup"><span data-stu-id="8712a-122">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="8712a-123">Il contesto viene in genere configurato in `Startup.cs` con la stringa di connessione letta dalla configurazione.</span><span class="sxs-lookup"><span data-stu-id="8712a-123">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="8712a-124">Si noti che il `GetConnectionString()` metodo cerca un valore di configurazione la cui chiave è `ConnectionStrings:<connection string name>` .</span><span class="sxs-lookup"><span data-stu-id="8712a-124">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="8712a-125">È necessario importare lo spazio dei nomi [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) per usare questo metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="8712a-125">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
