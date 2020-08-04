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
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="07538-102">Aggiornamento da EF Core 1,0 RC2 a RTM</span><span class="sxs-lookup"><span data-stu-id="07538-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="07538-103">Questo articolo fornisce indicazioni per lo trasferimento di un'applicazione compilata con i pacchetti RC2 a 1.0.0 RTM.</span><span class="sxs-lookup"><span data-stu-id="07538-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="07538-104">Versioni del pacchetto</span><span class="sxs-lookup"><span data-stu-id="07538-104">Package Versions</span></span>

<span data-ttu-id="07538-105">I nomi dei pacchetti di primo livello che in genere si installano in un'applicazione non sono stati modificati tra RC2 e RTM.</span><span class="sxs-lookup"><span data-stu-id="07538-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="07538-106">**È necessario aggiornare i pacchetti installati alle versioni RTM:**</span><span class="sxs-lookup"><span data-stu-id="07538-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="07538-107">I pacchetti di runtime (ad esempio, `Microsoft.EntityFrameworkCore.SqlServer` ) sono stati modificati da `1.0.0-rc2-final` a `1.0.0` .</span><span class="sxs-lookup"><span data-stu-id="07538-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="07538-108">Il `Microsoft.EntityFrameworkCore.Tools` pacchetto è stato modificato da `1.0.0-preview1-final` a `1.0.0-preview2-final` .</span><span class="sxs-lookup"><span data-stu-id="07538-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="07538-109">Si noti che gli strumenti sono ancora in versione non definitiva.</span><span class="sxs-lookup"><span data-stu-id="07538-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="07538-110">È possibile che sia necessario aggiungere maxLength alle migrazioni esistenti</span><span class="sxs-lookup"><span data-stu-id="07538-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="07538-111">In RC2 la definizione di colonna in una migrazione era simile a `table.Column<string>(nullable: true)` e la lunghezza della colonna veniva cercata in alcuni metadati archiviati nel codice sottostante alla migrazione.</span><span class="sxs-lookup"><span data-stu-id="07538-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="07538-112">Nella versione RTM la lunghezza è ora inclusa nel codice con impalcature `table.Column<string>(maxLength: 450, nullable: true)` .</span><span class="sxs-lookup"><span data-stu-id="07538-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="07538-113">Per tutte le migrazioni esistenti con impalcature precedenti all'uso di RTM non sarà `maxLength` specificato l'argomento.</span><span class="sxs-lookup"><span data-stu-id="07538-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="07538-114">Ciò significa che verrà utilizzata la lunghezza massima supportata dal database ( `nvarchar(max)` in SQL Server).</span><span class="sxs-lookup"><span data-stu-id="07538-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="07538-115">Questa operazione può essere corretta per alcune colonne, ma è necessario aggiornare le colonne che fanno parte di una chiave, una chiave esterna o un indice per includere una lunghezza massima.</span><span class="sxs-lookup"><span data-stu-id="07538-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="07538-116">Per convenzione, 450 è la lunghezza massima utilizzata per chiavi, chiavi esterne e colonne indicizzate.</span><span class="sxs-lookup"><span data-stu-id="07538-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="07538-117">Se nel modello è stata configurata in modo esplicito una lunghezza, è necessario utilizzare tale lunghezza.</span><span class="sxs-lookup"><span data-stu-id="07538-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="07538-118">Identità ASP.NET</span><span class="sxs-lookup"><span data-stu-id="07538-118">ASP.NET Identity</span></span>

<span data-ttu-id="07538-119">Questa modifica ha effetto sui progetti che usano ASP.NET Identity e sono stati creati da un modello di progetto precedente alla versione RTM.</span><span class="sxs-lookup"><span data-stu-id="07538-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="07538-120">Il modello di progetto include una migrazione utilizzata per creare il database.</span><span class="sxs-lookup"><span data-stu-id="07538-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="07538-121">Questa migrazione deve essere modificata per specificare la lunghezza massima di `256` per le colonne seguenti.</span><span class="sxs-lookup"><span data-stu-id="07538-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="07538-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="07538-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="07538-123">Nome</span><span class="sxs-lookup"><span data-stu-id="07538-123">Name</span></span>
  * <span data-ttu-id="07538-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="07538-124">NormalizedName</span></span>
* <span data-ttu-id="07538-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="07538-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="07538-126">E-mail</span><span class="sxs-lookup"><span data-stu-id="07538-126">Email</span></span>
  * <span data-ttu-id="07538-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="07538-127">NormalizedEmail</span></span>
  * <span data-ttu-id="07538-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="07538-128">NormalizedUserName</span></span>
  * <span data-ttu-id="07538-129">UserName</span><span class="sxs-lookup"><span data-stu-id="07538-129">UserName</span></span>

<span data-ttu-id="07538-130">Se la modifica iniziale viene applicata a un database, l'esito negativo della modifica comporterà l'eccezione seguente.</span><span class="sxs-lookup"><span data-stu-id="07538-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="07538-131">.NET Core: rimuovere "Imports" in project.json</span><span class="sxs-lookup"><span data-stu-id="07538-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="07538-132">Se la destinazione è .NET Core con RC2, è necessario aggiungere `imports` project.jscome soluzione alternativa temporanea per alcune delle dipendenze di EF core che non supportano .NET standard.</span><span class="sxs-lookup"><span data-stu-id="07538-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="07538-133">È ora possibile rimuovere questi elementi.</span><span class="sxs-lookup"><span data-stu-id="07538-133">These can now be removed.</span></span>

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
> <span data-ttu-id="07538-134">A partire dalla versione 1,0 RTM, il [.NET Core SDK](https://www.microsoft.com/net/download/core) non supporta più `project.json` o lo sviluppo di applicazioni .NET Core con Visual Studio 2015.</span><span class="sxs-lookup"><span data-stu-id="07538-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="07538-135">È consigliabile [eseguire la migrazione da project.json a csproj](/dotnet/articles/core/migration/).</span><span class="sxs-lookup"><span data-stu-id="07538-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="07538-136">Se si usa Visual Studio, è consigliabile eseguire l'aggiornamento a [Visual studio 2017](https://www.visualstudio.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="07538-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="07538-137">UWP: aggiungere reindirizzamenti di binding</span><span class="sxs-lookup"><span data-stu-id="07538-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="07538-138">Il tentativo di eseguire i comandi EF nei progetti piattaforma UWP (Universal Windows Platform) (UWP) comporta l'errore seguente:</span><span class="sxs-lookup"><span data-stu-id="07538-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="07538-139">È necessario aggiungere manualmente i reindirizzamenti di binding al progetto UWP.</span><span class="sxs-lookup"><span data-stu-id="07538-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="07538-140">Creare un file denominato `App.config` nella cartella radice del progetto e aggiungere reindirizzamenti alle versioni degli assembly corrette.</span><span class="sxs-lookup"><span data-stu-id="07538-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

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
