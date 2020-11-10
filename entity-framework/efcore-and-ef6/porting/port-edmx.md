---
title: Porting da EF6 a EF Core-porting di un modello di EDMX-Based-EF
description: Informazioni specifiche sul porting di un'applicazione modello basata su EDMX Entity Framework 6 per Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 9c1b308318c9bf0325ece0b60ace646becc8de39
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429221"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="43be6-103">Porting di un modello di EDMX-Based EF6 per EF Core</span><span class="sxs-lookup"><span data-stu-id="43be6-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="43be6-104">EF Core non supporta il formato di file EDMX per i modelli.</span><span class="sxs-lookup"><span data-stu-id="43be6-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="43be6-105">L'opzione migliore per trasferire questi modelli consiste nel generare un nuovo modello basato sul codice dal database per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="43be6-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="43be6-106">Installare EF Core pacchetti NuGet</span><span class="sxs-lookup"><span data-stu-id="43be6-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="43be6-107">Installare il pacchetto NuGet `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="43be6-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="43be6-108">Rigenera il modello</span><span class="sxs-lookup"><span data-stu-id="43be6-108">Regenerate the model</span></span>

<span data-ttu-id="43be6-109">È ora possibile usare la funzionalità di reverse engineering per creare un modello basato sul database esistente.</span><span class="sxs-lookup"><span data-stu-id="43be6-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="43be6-110">Eseguire il comando seguente nella console di gestione pacchetti (strumenti-> gestione pacchetti NuGet-> console di gestione pacchetti).</span><span class="sxs-lookup"><span data-stu-id="43be6-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="43be6-111">Vedere [console di gestione pacchetti (Visual Studio)](xref:core/cli/powershell) per le opzioni di comando per l'impalcatura di un subset di tabelle e così via.</span><span class="sxs-lookup"><span data-stu-id="43be6-111">See [Package Manager Console (Visual Studio)](xref:core/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

```powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="43be6-112">Ad esempio, di seguito è riportato il comando per eseguire l'impalcatura di un modello dal database blogging nell'istanza di SQL Server database locale.</span><span class="sxs-lookup"><span data-stu-id="43be6-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="43be6-113">Rimuovi modello EF6</span><span class="sxs-lookup"><span data-stu-id="43be6-113">Remove EF6 model</span></span>

<span data-ttu-id="43be6-114">A questo punto è necessario rimuovere il modello EF6 dall'applicazione.</span><span class="sxs-lookup"><span data-stu-id="43be6-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="43be6-115">È consigliabile lasciare installato il pacchetto NuGet EF6 (EntityFramework), perché EF Core e EF6 possono essere utilizzati side-by-side nella stessa applicazione.</span><span class="sxs-lookup"><span data-stu-id="43be6-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="43be6-116">Tuttavia, se non si intende usare EF6 in tutte le aree dell'applicazione, la disinstallazione del pacchetto consente di ottenere errori di compilazione su frammenti di codice che richiedono attenzione.</span><span class="sxs-lookup"><span data-stu-id="43be6-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="43be6-117">Aggiornare il codice</span><span class="sxs-lookup"><span data-stu-id="43be6-117">Update your code</span></span>

<span data-ttu-id="43be6-118">A questo punto, si tratta di risolvere gli errori di compilazione e di esaminare il codice per verificare se il comportamento cambia tra EF6 e EF Core avrà un effetto.</span><span class="sxs-lookup"><span data-stu-id="43be6-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="43be6-119">Testare la porta</span><span class="sxs-lookup"><span data-stu-id="43be6-119">Test the port</span></span>

<span data-ttu-id="43be6-120">Solo perché l'applicazione viene compilata, non significa che la porta è stata eseguita correttamente per EF Core.</span><span class="sxs-lookup"><span data-stu-id="43be6-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="43be6-121">È necessario testare tutte le aree dell'applicazione per assicurarsi che nessuna delle modifiche del comportamento abbia avuto un impatto negativo sull'applicazione.</span><span class="sxs-lookup"><span data-stu-id="43be6-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
