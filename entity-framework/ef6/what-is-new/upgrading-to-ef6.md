---
title: L'aggiornamento a Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
caps.latest.revision: 3
ms.openlocfilehash: d22f0d686e6b8e3922d37245386af7723bf08ba1
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122781"
---
# <a name="upgrading-to-entity-framework-6"></a><span data-ttu-id="f6fb3-102">L'aggiornamento a Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="f6fb3-102">Upgrading to Entity Framework 6</span></span>

<span data-ttu-id="f6fb3-103">Nelle versioni precedenti di Entity Framework il codice è stato suddiviso tra le librerie di base (principalmente System) fornite come parte di .NET Framework e librerie (OOB) di out-of-band (principalmente EntityFramework) fornite in un pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-103">In previous versions of EF the code was split between core libraries (primarily System.Data.Entity.dll) shipped as part of the .NET Framework and out-of-band (OOB) libraries (primarily EntityFramework.dll) shipped in a NuGet package.</span></span> <span data-ttu-id="f6fb3-104">EF6 richiede il codice dalle librerie di base e lo incorpora nelle librerie di OOB.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-104">EF6 takes the code from the core libraries and incorporates it into the OOB libraries.</span></span> <span data-ttu-id="f6fb3-105">Questo è stato necessario per consentire a Entity Framework essere reso open source e per poter essere in grado di evolvere a un ritmo diverso da .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-105">This was necessary in order to allow EF to be made open source and for it to be able to evolve at a different pace from .NET Framework.</span></span> <span data-ttu-id="f6fb3-106">La conseguenza di ciò è che le applicazioni dovranno essere ricompilati utilizzando i tipi di spostata.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-106">The consequence of this is that applications will need to be rebuilt against the moved types.</span></span>

<span data-ttu-id="f6fb3-107">Questo dovrebbe essere semplice per le applicazioni che usano DbContext come fornito in Entity Framework 4.1 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-107">This should be straightforward for applications that make use of DbContext as shipped in EF 4.1 and later.</span></span> <span data-ttu-id="f6fb3-108">È necessario per le applicazioni che usano ObjectContext un po' più lavoro ma non ancora difficile da eseguire.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-108">A little more work is required for applications that make use of ObjectContext but it still isn’t hard to do.</span></span>

<span data-ttu-id="f6fb3-109">Ecco un elenco di controllo delle operazioni da eseguire per aggiornare un'applicazione esistente a EF6.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-109">Here is a checklist of the things you need to do to upgrade an existing application to EF6.</span></span>

## <a name="1-install-the-ef6-nuget-package"></a><span data-ttu-id="f6fb3-110">1. Installare il pacchetto NuGet di Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="f6fb3-110">1. Install the EF6 NuGet package</span></span>

<span data-ttu-id="f6fb3-111">È necessario eseguire l'aggiornamento per il nuovo runtime di Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-111">You need to upgrade to the new Entity Framework 6 runtime.</span></span>

1. <span data-ttu-id="f6fb3-112">Pulsante destro del mouse sul progetto, quindi selezionare **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="f6fb3-112">Right-click on your project and select **Manage NuGet Packages...**</span></span>  
2. <span data-ttu-id="f6fb3-113">Sotto il **Online** scheda selezionare **EntityFramework** e fare clic su **installare**</span><span class="sxs-lookup"><span data-stu-id="f6fb3-113">Under the **Online** tab select **EntityFramework** and click **Install**</span></span>  
   > [!NOTE]
   > <span data-ttu-id="f6fb3-114">Se una versione precedente dell'installazione del pacchetto EntityFramework NuGet si eseguirà l'aggiornamento a EF6.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-114">If a previous version of the EntityFramework NuGet package was installed this will upgrade it to EF6.</span></span>

<span data-ttu-id="f6fb3-115">In alternativa, è possibile eseguire il comando seguente dalla Console di gestione pacchetti:</span><span class="sxs-lookup"><span data-stu-id="f6fb3-115">Alternatively, you can run the following command from Package Manager Console:</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a><span data-ttu-id="f6fb3-116">2. Assicurarsi che vengano rimossi i riferimenti ad assembly a System</span><span class="sxs-lookup"><span data-stu-id="f6fb3-116">2. Ensure that assembly references to System.Data.Entity.dll are removed</span></span>

<span data-ttu-id="f6fb3-117">Installazione del pacchetto NuGet di Entity Framework 6 deve rimuovere automaticamente qualsiasi riferimento a Data. Entity dal progetto per l'utente.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-117">Installing the EF6 NuGet package should automatically remove any references to System.Data.Entity from your project for you.</span></span>

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a><span data-ttu-id="f6fb3-118">3. Scambia i modelli di progettazione (EDMX di Entity Framework) per usare la generazione di codice di Entity Framework 6.x</span><span class="sxs-lookup"><span data-stu-id="f6fb3-118">3. Swap any EF Designer (EDMX) models to use EF 6.x code generation</span></span>

<span data-ttu-id="f6fb3-119">Se si dispone di tutti i modelli creati con la finestra di progettazione di Entity Framework, è necessario aggiornare i modelli di generazione di codice per generare codice compatibile con Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-119">If you have any models created with the EF Designer, you will need to update the code generation templates to generate EF6 compatible code.</span></span>

> [!NOTE]
> <span data-ttu-id="f6fb3-120">Sono disponibili attualmente soltanto i modelli del generatore di DbContext di Entity Framework 6.x per Visual Studio 2012 e 2013.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-120">There are currently only EF 6.x DbContext Generator templates available for Visual Studio 2012 and 2013.</span></span>

1. <span data-ttu-id="f6fb3-121">Eliminare i modelli di generazione del codice esistente.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-121">Delete existing code-generation templates.</span></span> <span data-ttu-id="f6fb3-122">Questi file verranno in genere è denominati  **\<edmx_file_name\>tt** e  **\<edmx_file_name\>. Context.tt** ed essere annidato sotto il file edmx in Esplora soluzioni.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-122">These files will typically be named **\<edmx_file_name\>.tt** and **\<edmx_file_name\>.Context.tt** and be nested under your edmx file in Solution Explorer.</span></span> <span data-ttu-id="f6fb3-123">È possibile selezionare i modelli in Esplora soluzioni e premere il **CANC** chiave per eliminarli.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-123">You can select the templates in Solution Explorer and press the **Del** key to delete them.</span></span>  
   > [!NOTE]
   > <span data-ttu-id="f6fb3-124">Nei progetti di sito Web i modelli verranno non essere annidati sotto il file edmx, ma elencati insieme a esso in Esplora soluzioni.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-124">In Web Site projects the templates will not be nested under your edmx file, but listed alongside it in Solution Explorer.</span></span>  

   > [!NOTE]
   > <span data-ttu-id="f6fb3-125">Nei progetti Visual Basic.NET è necessario abilitare 'Mostra tutti i file' per poter visualizzare i file di modello annidato.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-125">In VB.NET projects you will need to enable 'Show All Files' to be able to see the nested template files.</span></span>
2. <span data-ttu-id="f6fb3-126">Aggiungere modello di generazione codice appropriato di EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-126">Add the appropriate EF 6.x code generation template.</span></span> <span data-ttu-id="f6fb3-127">Aprire il modello nella finestra di progettazione di Entity Framework, pulsante destro del mouse su area di progettazione e seleziona **Aggiungi elemento di generazione di codice...**</span><span class="sxs-lookup"><span data-stu-id="f6fb3-127">Open your model in the EF Designer, right-click on the design surface and select **Add Code Generation Item...**</span></span>
    - <span data-ttu-id="f6fb3-128">Se si usa l'API DbContext (scelta consigliata) quindi **EF 6.x DbContext Generator** saranno disponibili nella **dati** scheda.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-128">If you are using the DbContext API (recommended) then **EF 6.x DbContext Generator** will be available under the **Data** tab.</span></span>  
      > [!NOTE]
      > <span data-ttu-id="f6fb3-129">Se si usa Visual Studio 2012, è necessario installare gli strumenti di Entity Framework 6 con questo modello.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-129">If you are using Visual Studio 2012, you will need to install the EF 6 Tools to have this template.</span></span> <span data-ttu-id="f6fb3-130">Visualizzare [ottenere Entity Framework](~/ef6/fundamentals/install.md) per informazioni dettagliate.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-130">See [Get Entity Framework](~/ef6/fundamentals/install.md) for details.</span></span>  

    - <span data-ttu-id="f6fb3-131">Se si usa l'API ObjectContext, dovrai selezionare i **Online** scheda e cercare **EF 6.x EntityObject Generator**.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-131">If you are using the ObjectContext API then you will need to select the **Online** tab and search for **EF 6.x EntityObject Generator**.</span></span>  
3. <span data-ttu-id="f6fb3-132">Se tutte le personalizzazioni sono applicati per i modelli di generazione di codice che è necessario applicare nuovamente per i modelli aggiornati.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-132">If you applied any customizations to the code generation templates you will need to re-apply them to the updated templates.</span></span>

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a><span data-ttu-id="f6fb3-133">4. Aggiornare gli spazi dei nomi per qualsiasi tipo EF core in uso</span><span class="sxs-lookup"><span data-stu-id="f6fb3-133">4. Update namespaces for any core EF types being used</span></span>

<span data-ttu-id="f6fb3-134">Gli spazi dei nomi per i tipi DbContext e Code First non sono stati modificati.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-134">The namespaces for DbContext and Code First types have not changed.</span></span> <span data-ttu-id="f6fb3-135">Ciò significa che per molte applicazioni che usano Entity Framework 4.1 o versioni successive non è necessario apportare alcuna modifica.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-135">This means for many applications that use EF 4.1 or later you will not need to change anything.</span></span>

<span data-ttu-id="f6fb3-136">I tipi, ad esempio ObjectContext disponibili in precedenza System sono stati spostati nuovi spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-136">Types like ObjectContext that were previously in System.Data.Entity.dll have been moved to new namespaces.</span></span> <span data-ttu-id="f6fb3-137">Ciò significa che potrebbe essere necessario aggiornare il *usando* oppure *importazione* direttive per la compilazione EF6.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-137">This means you may need to update your *using* or *Import* directives to build against EF6.</span></span>

<span data-ttu-id="f6fb3-138">La regola generale per lo spazio dei nomi è che qualsiasi tipo in System.Data.* viene spostata nel System.Data.Entity.Core.*.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-138">The general rule for namespace changes is that any type in System.Data.* is moved to System.Data.Entity.Core.*.</span></span> <span data-ttu-id="f6fb3-139">In altre parole, sufficiente inserire **Entity.Core.**</span><span class="sxs-lookup"><span data-stu-id="f6fb3-139">In other words, just insert **Entity.Core.**</span></span> <span data-ttu-id="f6fb3-140">Dopo aver System. Data.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-140">after System.Data.</span></span> <span data-ttu-id="f6fb3-141">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="f6fb3-141">For example:</span></span>

- <span data-ttu-id="f6fb3-142">System.Data.EntityException = > System. Data. **Entity.Core.** EntityException</span><span class="sxs-lookup"><span data-stu-id="f6fb3-142">System.Data.EntityException => System.Data.**Entity.Core.** EntityException</span></span>  
- <span data-ttu-id="f6fb3-143">System.Data.Objects.ObjectContext = > System. Data. **Entity.Core.** Objects.ObjectContext</span><span class="sxs-lookup"><span data-stu-id="f6fb3-143">System.Data.Objects.ObjectContext => System.Data.**Entity.Core.** Objects.ObjectContext</span></span>  
- <span data-ttu-id="f6fb3-144">System.Data.Objects.DataClasses.RelationshipManager = > System. Data. **Entity.Core.** Objects.DataClasses.RelationshipManager</span><span class="sxs-lookup"><span data-stu-id="f6fb3-144">System.Data.Objects.DataClasses.RelationshipManager => System.Data.**Entity.Core.** Objects.DataClasses.RelationshipManager</span></span>  

<span data-ttu-id="f6fb3-145">Questi tipi sono nel *Core* gli spazi dei nomi perché non vengono usati direttamente per la maggior parte delle applicazioni basate su DbContext.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-145">These types are in the *Core* namespaces because they are not used directly for most DbContext-based applications.</span></span> <span data-ttu-id="f6fb3-146">Alcuni tipi che fanno parte della System sono ancora usati comunemente e direttamente per le applicazioni basate su DbContext e pertanto non sono stati spostati nel *Core* gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-146">Some types that were part of System.Data.Entity.dll are still used commonly and directly for DbContext-based applications and so have not been moved into the *Core* namespaces.</span></span> <span data-ttu-id="f6fb3-147">Questi sono:</span><span class="sxs-lookup"><span data-stu-id="f6fb3-147">These are:</span></span>

- <span data-ttu-id="f6fb3-148">System.Data.EntityState = > System. Data. **Entità.** EntityState</span><span class="sxs-lookup"><span data-stu-id="f6fb3-148">System.Data.EntityState => System.Data.**Entity.** EntityState</span></span>  
- <span data-ttu-id="f6fb3-149">System.Data.Objects.DataClasses.EdmFunctionAttribute = > System. Data. **Entity.DbFunctionAttribute**</span><span class="sxs-lookup"><span data-stu-id="f6fb3-149">System.Data.Objects.DataClasses.EdmFunctionAttribute => System.Data.**Entity.DbFunctionAttribute**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="f6fb3-150">Questa classe è stata rinominata; una classe con il vecchio nome esiste già e funziona, ma ora contrassegnato come obsoleto.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-150">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.</span></span>  
- <span data-ttu-id="f6fb3-151">System.Data.Objects.EntityFunctions = > System. Data. **Entity.DbFunctions**</span><span class="sxs-lookup"><span data-stu-id="f6fb3-151">System.Data.Objects.EntityFunctions => System.Data.**Entity.DbFunctions**</span></span>  
  > [!NOTE]
  > <span data-ttu-id="f6fb3-152">Questa classe è stata rinominata; una classe con il vecchio nome esiste già e funziona, ma ora contrassegnato come obsoleto.)</span><span class="sxs-lookup"><span data-stu-id="f6fb3-152">This class has been renamed; a class with the old name still exists and works, but it now marked as obsolete.)</span></span>  
- <span data-ttu-id="f6fb3-153">Classi spaziale (ad esempio, DbGeography, DbGeometry) sono stati spostati dalla System.Data.Spatial = > System. Data. **Entità.** Spaziali</span><span class="sxs-lookup"><span data-stu-id="f6fb3-153">Spatial classes (for example, DbGeography, DbGeometry) have moved from System.Data.Spatial => System.Data.**Entity.** Spatial</span></span>

> [!NOTE]
> <span data-ttu-id="f6fb3-154">Alcuni tipi nello spazio dei nomi System. Data sono in System che non è un assembly di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-154">Some types in the System.Data namespace are in System.Data.dll which is not an EF assembly.</span></span> <span data-ttu-id="f6fb3-155">Questi tipi non è sono spostato e pertanto non subiscono gli spazi dei nomi.</span><span class="sxs-lookup"><span data-stu-id="f6fb3-155">These types have not moved and so their namespaces remain unchanged.</span></span>