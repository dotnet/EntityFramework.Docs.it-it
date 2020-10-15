---
title: Selezione di Entity Framework versione runtime per i modelli di progettazione EF-EF6
description: Selezione di Entity Framework versione runtime per i modelli di progettazione EF in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: dcd06ed9585028b3a318d867b03980254979a32f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066096"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="d4ab5-103">Selezione di Entity Framework versione runtime per i modelli di progettazione EF</span><span class="sxs-lookup"><span data-stu-id="d4ab5-103">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="d4ab5-104">**Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="d4ab5-105">Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="d4ab5-106">A partire da EF6, la schermata seguente è stata aggiunta alla finestra di progettazione EF per consentire di selezionare la versione del runtime di destinazione durante la creazione di un modello.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-106">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="d4ab5-107">La schermata verrà visualizzata quando la versione più recente di Entity Framework non è ancora installata nel progetto.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-107">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="d4ab5-108">Se la versione più recente è già installata, verrà usata per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-108">If the latest version is already installed it will just be used by default.</span></span>

![Seleziona versione runtime](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a><span data-ttu-id="d4ab5-110">Destinazione di EF6. x</span><span class="sxs-lookup"><span data-stu-id="d4ab5-110">Targeting EF6.x</span></span>

<span data-ttu-id="d4ab5-111">È possibile scegliere EF6 dalla schermata ' scegliere la versione ' per aggiungere il runtime di EF6 al progetto.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-111">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="d4ab5-112">Una volta aggiunto EF6, questa schermata verrà arrestata nel progetto corrente.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-112">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="d4ab5-113">EF6 verrà disabilitato se è già installata una versione precedente di EF (poiché non è possibile fare riferimento a più versioni del runtime dallo stesso progetto).</span><span class="sxs-lookup"><span data-stu-id="d4ab5-113">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="d4ab5-114">Se l'opzione EF6 non è abilitata, seguire questa procedura per aggiornare il progetto a EF6:</span><span class="sxs-lookup"><span data-stu-id="d4ab5-114">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="d4ab5-115">Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet...**</span><span class="sxs-lookup"><span data-stu-id="d4ab5-115">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="d4ab5-116">Seleziona **aggiornamenti**</span><span class="sxs-lookup"><span data-stu-id="d4ab5-116">Select **Updates**</span></span>
3.  <span data-ttu-id="d4ab5-117">Selezionare **EntityFramework** (assicurarsi che sia in corso l'aggiornamento alla versione desiderata)</span><span class="sxs-lookup"><span data-stu-id="d4ab5-117">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="d4ab5-118">Fare clic su **Update** (Aggiorna).</span><span class="sxs-lookup"><span data-stu-id="d4ab5-118">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="d4ab5-119">Destinazione di EF5. x</span><span class="sxs-lookup"><span data-stu-id="d4ab5-119">Targeting EF5.x</span></span>

<span data-ttu-id="d4ab5-120">È possibile scegliere EF5 dalla schermata ' scegliere la versione ' per aggiungere il runtime di EF5 al progetto.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-120">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="d4ab5-121">Una volta aggiunto EF5, viene visualizzata la schermata con l'opzione EF6 disabilitata.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-121">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="d4ab5-122">Se è già installata una versione EF4. x del runtime, si noterà che la versione di EF è elencata nella schermata invece che in EF5.</span><span class="sxs-lookup"><span data-stu-id="d4ab5-122">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="d4ab5-123">In questa situazione è possibile eseguire l'aggiornamento a EF5 attenendosi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="d4ab5-123">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="d4ab5-124">Selezione **strumenti- &gt; Gestione pacchetti libreria- &gt; console di gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="d4ab5-124">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="d4ab5-125">Eseguire **Install-Package EntityFramework-Version 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="d4ab5-125">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="d4ab5-126">Destinazione di EF4. x</span><span class="sxs-lookup"><span data-stu-id="d4ab5-126">Targeting EF4.x</span></span>

<span data-ttu-id="d4ab5-127">È possibile installare il runtime EF4. x nel progetto attenendosi alla procedura seguente:</span><span class="sxs-lookup"><span data-stu-id="d4ab5-127">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="d4ab5-128">Selezione **strumenti- &gt; Gestione pacchetti libreria- &gt; console di gestione pacchetti**</span><span class="sxs-lookup"><span data-stu-id="d4ab5-128">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="d4ab5-129">Eseguire **Install-Package EntityFramework-Version 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="d4ab5-129">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
