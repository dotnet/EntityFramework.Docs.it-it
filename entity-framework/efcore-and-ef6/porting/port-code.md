---
title: Porting da EF6 a EF Core-porting di un modello di Code-Based-EF
description: Informazioni specifiche sul porting di un'applicazione modello basata su codice Entity Framework 6 per Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 819c3bc0eba140c023cdcd5038a4cd63c300ed4c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064224"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="c4647-103">Porting di un modello di Code-Based EF6 per EF Core</span><span class="sxs-lookup"><span data-stu-id="c4647-103">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="c4647-104">Se sono state lette tutte le avvertenze e si è pronti per la porta, di seguito sono riportate alcune linee guida utili per iniziare.</span><span class="sxs-lookup"><span data-stu-id="c4647-104">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="c4647-105">Installare EF Core pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="c4647-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="c4647-106">Per usare EF Core, installare il pacchetto NuGet per il provider di database che si vuole usare.</span><span class="sxs-lookup"><span data-stu-id="c4647-106">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="c4647-107">Ad esempio, quando la destinazione è SQL Server, è necessario installare `Microsoft.EntityFrameworkCore.SqlServer` .</span><span class="sxs-lookup"><span data-stu-id="c4647-107">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="c4647-108">Per informazioni dettagliate, vedere [provider di database](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="c4647-108">See [Database Providers](xref:core/providers/index) for details.</span></span>

<span data-ttu-id="c4647-109">Se si prevede di usare le migrazioni, è necessario installare anche il `Microsoft.EntityFrameworkCore.Tools` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="c4647-109">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="c4647-110">È consigliabile lasciare installato il pacchetto NuGet EF6 (EntityFramework), perché EF Core e EF6 possono essere utilizzati side-by-side nella stessa applicazione.</span><span class="sxs-lookup"><span data-stu-id="c4647-110">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="c4647-111">Tuttavia, se non si intende usare EF6 in tutte le aree dell'applicazione, la disinstallazione del pacchetto consente di ottenere errori di compilazione su frammenti di codice che richiedono attenzione.</span><span class="sxs-lookup"><span data-stu-id="c4647-111">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="c4647-112">Scambia spazi dei nomi</span><span class="sxs-lookup"><span data-stu-id="c4647-112">Swap namespaces</span></span>

<span data-ttu-id="c4647-113">La maggior parte delle API usate in EF6 si trova nello `System.Data.Entity` spazio dei nomi e negli spazi dei nomi secondari correlati.</span><span class="sxs-lookup"><span data-stu-id="c4647-113">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="c4647-114">La prima modifica del codice prevede lo scambio nello `Microsoft.EntityFrameworkCore` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c4647-114">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="c4647-115">Si inizia in genere con il file di codice del contesto derivato, quindi si esegue questa operazione, risolvendo gli errori di compilazione man mano che si verificano.</span><span class="sxs-lookup"><span data-stu-id="c4647-115">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="c4647-116">Configurazione del contesto (connessione e così via)</span><span class="sxs-lookup"><span data-stu-id="c4647-116">Context configuration (connection etc.)</span></span>

<span data-ttu-id="c4647-117">Come descritto in [assicurarsi che EF Core funzionerà per l'applicazione](xref:efcore-and-ef6/porting/index), EF core ha meno magie per il rilevamento del database a cui connettersi.</span><span class="sxs-lookup"><span data-stu-id="c4647-117">As described in [Ensure EF Core Will Work for Your Application](xref:efcore-and-ef6/porting/index), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="c4647-118">Sarà necessario eseguire l'override del `OnConfiguring` metodo nel contesto derivato e utilizzare l'API specifica del provider di database per configurare la connessione al database.</span><span class="sxs-lookup"><span data-stu-id="c4647-118">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="c4647-119">La maggior parte delle applicazioni EF6 archivia la stringa di connessione nel file delle applicazioni `App/Web.config` .</span><span class="sxs-lookup"><span data-stu-id="c4647-119">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="c4647-120">In EF Core la stringa di connessione viene letta usando l' `ConfigurationManager` API.</span><span class="sxs-lookup"><span data-stu-id="c4647-120">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="c4647-121">Per poter usare questa API, potrebbe essere necessario aggiungere un riferimento all' `System.Configuration` assembly del Framework.</span><span class="sxs-lookup"><span data-stu-id="c4647-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

```csharp
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

## <a name="update-your-code"></a><span data-ttu-id="c4647-122">Aggiornare il codice</span><span class="sxs-lookup"><span data-stu-id="c4647-122">Update your code</span></span>

<span data-ttu-id="c4647-123">A questo punto, è necessario risolvere gli errori di compilazione ed esaminare il codice per verificare se le modifiche apportate al comportamento incidono sull'utente.</span><span class="sxs-lookup"><span data-stu-id="c4647-123">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="c4647-124">Migrazioni esistenti</span><span class="sxs-lookup"><span data-stu-id="c4647-124">Existing migrations</span></span>

<span data-ttu-id="c4647-125">Non esiste un modo davvero possibile per trasferire le migrazioni di EF6 esistenti in EF Core.</span><span class="sxs-lookup"><span data-stu-id="c4647-125">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="c4647-126">Se possibile, è preferibile presupporre che tutte le migrazioni precedenti da EF6 siano state applicate al database e quindi avviare la migrazione dello schema da tale punto utilizzando EF Core.</span><span class="sxs-lookup"><span data-stu-id="c4647-126">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="c4647-127">A tale scopo, è necessario utilizzare il `Add-Migration` comando per aggiungere una migrazione dopo che il modello viene trasferito a EF core.</span><span class="sxs-lookup"><span data-stu-id="c4647-127">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="c4647-128">Si rimuoverà quindi tutto il codice dai `Up` `Down` metodi e della migrazione con impalcature.</span><span class="sxs-lookup"><span data-stu-id="c4647-128">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="c4647-129">Le migrazioni successive vengono confrontate con il modello quando la migrazione iniziale è stata sottoposta a impalcatura.</span><span class="sxs-lookup"><span data-stu-id="c4647-129">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="c4647-130">Testare la porta</span><span class="sxs-lookup"><span data-stu-id="c4647-130">Test the port</span></span>

<span data-ttu-id="c4647-131">Solo perché l'applicazione viene compilata, non significa che la porta è stata eseguita correttamente per EF Core.</span><span class="sxs-lookup"><span data-stu-id="c4647-131">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="c4647-132">È necessario testare tutte le aree dell'applicazione per assicurarsi che nessuna delle modifiche del comportamento abbia avuto un impatto negativo sull'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c4647-132">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
