---
title: Aggiornamento da EF Core 1,0 RC2 a RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526771"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a>Aggiornamento da EF Core 1,0 RC2 a RTM

Questo articolo fornisce indicazioni per lo trasferimento di un'applicazione compilata con i pacchetti RC2 a 1.0.0 RTM.

## <a name="package-versions"></a>Versioni del pacchetto

I nomi dei pacchetti di primo livello che in genere si installano in un'applicazione non sono stati modificati tra RC2 e RTM.

**È necessario aggiornare i pacchetti installati alle versioni RTM:**

* I pacchetti di runtime (ad esempio, `Microsoft.EntityFrameworkCore.SqlServer` ) sono stati modificati da `1.0.0-rc2-final` a `1.0.0` .

* Il `Microsoft.EntityFrameworkCore.Tools` pacchetto è stato modificato da `1.0.0-preview1-final` a `1.0.0-preview2-final` . Si noti che gli strumenti sono ancora in versione non definitiva.

## <a name="existing-migrations-may-need-maxlength-added"></a>È possibile che sia necessario aggiungere maxLength alle migrazioni esistenti

In RC2 la definizione di colonna in una migrazione era simile a `table.Column<string>(nullable: true)` e la lunghezza della colonna veniva cercata in alcuni metadati archiviati nel codice sottostante alla migrazione. Nella versione RTM la lunghezza è ora inclusa nel codice con impalcature `table.Column<string>(maxLength: 450, nullable: true)` .

Per tutte le migrazioni esistenti con impalcature precedenti all'uso di RTM non sarà `maxLength` specificato l'argomento. Ciò significa che verrà utilizzata la lunghezza massima supportata dal database ( `nvarchar(max)` in SQL Server). Questa operazione può essere corretta per alcune colonne, ma è necessario aggiornare le colonne che fanno parte di una chiave, una chiave esterna o un indice per includere una lunghezza massima. Per convenzione, 450 è la lunghezza massima utilizzata per chiavi, chiavi esterne e colonne indicizzate. Se nel modello è stata configurata in modo esplicito una lunghezza, è necessario utilizzare tale lunghezza.

### <a name="aspnet-identity"></a>Identità ASP.NET

Questa modifica ha effetto sui progetti che usano ASP.NET Identity e sono stati creati da un modello di progetto precedente alla versione RTM. Il modello di progetto include una migrazione utilizzata per creare il database. Questa migrazione deve essere modificata per specificare la lunghezza massima di `256` per le colonne seguenti.

* **AspNetRoles**
  * Nome
  * NormalizedName
* **AspNetUsers**
  * E-mail
  * NormalizedEmail
  * NormalizedUserName
  * UserName

Se la modifica iniziale viene applicata a un database, l'esito negativo della modifica comporterà l'eccezione seguente.

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a>.NET Core: rimuovere "Imports" in project.json

Se la destinazione è .NET Core con RC2, è necessario aggiungere `imports` project.jscome soluzione alternativa temporanea per alcune delle dipendenze di EF core che non supportano .NET standard. È ora possibile rimuovere questi elementi.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> A partire dalla versione 1,0 RTM, il [.NET Core SDK](https://www.microsoft.com/net/download/core) non supporta più `project.json` o lo sviluppo di applicazioni .NET Core con Visual Studio 2015. È consigliabile [eseguire la migrazione da project.json a csproj](/dotnet/articles/core/migration/). Se si usa Visual Studio, è consigliabile eseguire l'aggiornamento a [Visual studio 2017](https://www.visualstudio.com/downloads/).

## <a name="uwp-add-binding-redirects"></a>UWP: aggiungere reindirizzamenti di binding

Il tentativo di eseguire i comandi EF nei progetti piattaforma UWP (Universal Windows Platform) (UWP) comporta l'errore seguente:

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

È necessario aggiungere manualmente i reindirizzamenti di binding al progetto UWP. Creare un file denominato `App.config` nella cartella radice del progetto e aggiungere reindirizzamenti alle versioni degli assembly corrette.

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
