---
title: Stringhe di connessione-EF Core
description: Gestione delle stringhe di connessione in ambienti diversi con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 5b37daa9ba2869ee58c1b95ad9cbaf6b2b491391
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617775"
---
# <a name="connection-strings"></a>Stringhe di connessione

La maggior parte dei provider di database richiede una forma di stringa di connessione per la connessione al database. In alcuni casi la stringa di connessione contiene informazioni riservate che devono essere protette. Potrebbe anche essere necessario modificare la stringa di connessione mentre si sposta l'applicazione tra ambienti, ad esempio sviluppo, test e produzione.

## <a name="winforms--wpf-applications"></a>WinForms & applicazioni WPF

Le applicazioni WinForms, WPF e ASP.NET 4 hanno un modello di stringa di connessione provato e testato. La stringa di connessione deve essere aggiunta al file di App.config dell'applicazione (Web.config se si usa ASP.NET). Se la stringa di connessione contiene informazioni riservate, ad esempio nome utente e password, è possibile proteggere il contenuto del file di configurazione utilizzando lo [strumento Gestione segreta](/aspnet/core/security/app-secrets#secret-manager).

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
> L' `providerName` impostazione non è necessaria per EF Core stringhe di connessione archiviate nel App.config perché il provider di database è configurato tramite codice.

È quindi possibile leggere la stringa di connessione usando l' `ConfigurationManager` API nel metodo del `OnConfiguring` contesto. Per poter usare questa API, potrebbe essere necessario aggiungere un riferimento all' `System.Configuration` assembly del Framework.

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

## <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Le stringhe di connessione in un'applicazione UWP sono in genere una connessione SQLite che specifica solo un nome file locale. In genere non contengono informazioni riservate e non devono essere modificate durante la distribuzione di un'applicazione. Di conseguenza, queste stringhe di connessione sono in genere più belle da rimanere nel codice, come illustrato di seguito. Se si vuole spostarli fuori dal codice, UWP supporta il concetto di impostazioni. per informazioni dettagliate, vedere la [sezione Impostazioni app della documentazione di UWP](/windows/uwp/app-settings/store-and-retrieve-app-data) .

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

## <a name="aspnet-core"></a>ASP.NET Core

In ASP.NET Core il sistema di configurazione è molto flessibile e la stringa di connessione può essere archiviata in `appsettings.json` , una variabile di ambiente, l'archivio del segreto utente o un'altra origine della configurazione. Per ulteriori informazioni, vedere la sezione relativa alla [configurazione della documentazione di ASP.NET Core](/aspnet/core/fundamentals/configuration) .

Ad esempio, è possibile usare lo [strumento di gestione dei segreti](/aspnet/core/security/app-secrets#secret-manager) per archiviare la password del database e quindi, nell'impalcatura, usare una stringa di connessione costituita semplicemente da `Name=<database-alias>` .

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

Oppure nell'esempio seguente viene illustrata la stringa di connessione archiviata in `appsettings.json` .

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

Il contesto viene in genere configurato in `Startup.cs` con la stringa di connessione letta dalla configurazione. Si noti che il `GetConnectionString()` metodo cerca un valore di configurazione la cui chiave è `ConnectionStrings:<connection string name>` . È necessario importare lo spazio dei nomi [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) per usare questo metodo di estensione.

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
