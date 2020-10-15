---
title: Migrazioni in ambienti team-EF Core
description: Procedure consigliate per la gestione delle migrazioni e la risoluzione dei conflitti negli ambienti team con Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062503"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="16ebf-103">Migrazioni in ambienti team</span><span class="sxs-lookup"><span data-stu-id="16ebf-103">Migrations in Team Environments</span></span>

<span data-ttu-id="16ebf-104">Quando si utilizzano migrazioni in ambienti team, prestare particolare attenzione al file di snapshot del modello.</span><span class="sxs-lookup"><span data-stu-id="16ebf-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="16ebf-105">Questo file può indicare se la migrazione del proprio team si unisce in modo corretto o se è necessario risolvere un conflitto creando nuovamente la migrazione prima di condividerla.</span><span class="sxs-lookup"><span data-stu-id="16ebf-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="16ebf-106">Unione</span><span class="sxs-lookup"><span data-stu-id="16ebf-106">Merging</span></span>

<span data-ttu-id="16ebf-107">Quando si esegue il merge delle migrazioni dai colleghi, è possibile che si verifichino conflitti nel file di snapshot del modello.</span><span class="sxs-lookup"><span data-stu-id="16ebf-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="16ebf-108">Se entrambe le modifiche non sono correlate, l'Unione è semplice e le due migrazioni possono coesistere.</span><span class="sxs-lookup"><span data-stu-id="16ebf-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="16ebf-109">Ad esempio, è possibile che si verifichi un conflitto di merge nella configurazione del tipo di entità Customer simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="16ebf-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="16ebf-110">Poiché entrambe queste proprietà devono esistere nel modello finale, completare il merge aggiungendo entrambe le proprietà.</span><span class="sxs-lookup"><span data-stu-id="16ebf-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="16ebf-111">In molti casi, il sistema di controllo della versione può unire automaticamente tali modifiche.</span><span class="sxs-lookup"><span data-stu-id="16ebf-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="16ebf-112">In questi casi, la migrazione e la migrazione del proprio team sono indipendenti tra loro.</span><span class="sxs-lookup"><span data-stu-id="16ebf-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="16ebf-113">Poiché una di esse potrebbe essere applicata per prima, non è necessario apportare altre modifiche alla migrazione prima di condividerla con il team.</span><span class="sxs-lookup"><span data-stu-id="16ebf-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="16ebf-114">Risoluzione dei conflitti</span><span class="sxs-lookup"><span data-stu-id="16ebf-114">Resolving conflicts</span></span>

<span data-ttu-id="16ebf-115">In alcuni casi si verifica un vero conflitto durante l'Unione del modello di snapshot del modello.</span><span class="sxs-lookup"><span data-stu-id="16ebf-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="16ebf-116">È possibile, ad esempio, che l'utente e il compagno di squadra abbiano rinominato la stessa proprietà.</span><span class="sxs-lookup"><span data-stu-id="16ebf-116">For example, you and your teammate may each have renamed the same property.</span></span>

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="16ebf-117">Se si verifica questo tipo di conflitto, risolverlo creando di nuovo la migrazione.</span><span class="sxs-lookup"><span data-stu-id="16ebf-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="16ebf-118">Seguire questa procedura:</span><span class="sxs-lookup"><span data-stu-id="16ebf-118">Follow these steps:</span></span>

1. <span data-ttu-id="16ebf-119">Interrompere l'Unione e il rollback nella directory di lavoro prima del merge</span><span class="sxs-lookup"><span data-stu-id="16ebf-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="16ebf-120">Rimuovere la migrazione (mantenendo però le modifiche del modello)</span><span class="sxs-lookup"><span data-stu-id="16ebf-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="16ebf-121">Unisci le modifiche dei colleghi nella directory di lavoro</span><span class="sxs-lookup"><span data-stu-id="16ebf-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="16ebf-122">Aggiungere nuovamente la migrazione</span><span class="sxs-lookup"><span data-stu-id="16ebf-122">Re-add your migration</span></span>

<span data-ttu-id="16ebf-123">Al termine di questa operazione, le due migrazioni possono essere applicate nell'ordine corretto.</span><span class="sxs-lookup"><span data-stu-id="16ebf-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="16ebf-124">La migrazione viene applicata per prima, rinominando la colonna in *alias*, successivamente la migrazione la rinomina a *nome utente*.</span><span class="sxs-lookup"><span data-stu-id="16ebf-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="16ebf-125">La migrazione può essere condivisa in modo sicuro con il resto del team.</span><span class="sxs-lookup"><span data-stu-id="16ebf-125">Your migration can safely be shared with the rest of the team.</span></span>
