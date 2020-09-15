---
title: Conversione da EF6 a EF Core - EF
description: Informazioni generali sul trasferimento di un'applicazione da Entity Framework 6 a Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 826b58bd-77b0-4bbc-bfcd-24d1ed3a8f38
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 132934df2ef7929372c4a092635c5c97227983f9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619675"
---
# <a name="porting-from-ef6-to-ef-core"></a><span data-ttu-id="af8ac-103">Conversione da EF6 a EF Core</span><span class="sxs-lookup"><span data-stu-id="af8ac-103">Porting from EF6 to EF Core</span></span>

<span data-ttu-id="af8ac-104">A seguito delle modifiche sostanziali in EF Core, non è consigliabile tentare di spostare un'applicazione da EF6 a EF Core se non in presenza di un valido motivo per apportare la modifica.</span><span class="sxs-lookup"><span data-stu-id="af8ac-104">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span>
<span data-ttu-id="af8ac-105">Il trasferimento da EF6 a EF Core deve essere visto come una conversione più che come un aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="af8ac-105">You should view the move from EF6 to EF Core as a port rather than an upgrade.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="af8ac-106">Prima di iniziare il processo di trasferimento, è importante verificare che EF Core soddisfi i requisiti di accesso ai dati per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="af8ac-106">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="af8ac-107">Funzionalità mancanti</span><span class="sxs-lookup"><span data-stu-id="af8ac-107">Missing features</span></span>

<span data-ttu-id="af8ac-108">Verificare che EF Core disponga di tutte le funzionalità necessarie per l'uso nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="af8ac-108">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="af8ac-109">Vedere [Confronto delle funzionalità](xref:efcore-and-ef6/index) per un confronto dettagliato tra il set di funzionalità di EF Core e il set di EF6.</span><span class="sxs-lookup"><span data-stu-id="af8ac-109">See [Feature Comparison](xref:efcore-and-ef6/index) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="af8ac-110">Se mancano alcune funzionalità necessarie, assicurarsi che sia possibile compensare la mancanza di queste funzionalità prima di eseguire il trasferimento a EF Core.</span><span class="sxs-lookup"><span data-stu-id="af8ac-110">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="af8ac-111">Modifiche del comportamento</span><span class="sxs-lookup"><span data-stu-id="af8ac-111">Behavior changes</span></span>

<span data-ttu-id="af8ac-112">Questo è un elenco non esaustivo delle modifiche del comportamento tra EF6 ed EF Core.</span><span class="sxs-lookup"><span data-stu-id="af8ac-112">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="af8ac-113">È importante tenere presenti queste considerazioni quando si trasferisce l'applicazione, in quanto possono cambiare il comportamento dell'applicazione, ma non vengono visualizzate come errori di compilazione dopo il passaggio a EF Core.</span><span class="sxs-lookup"><span data-stu-id="af8ac-113">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="af8ac-114">Comportamento di DbSet.Add/Attach e del grafico</span><span class="sxs-lookup"><span data-stu-id="af8ac-114">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="af8ac-115">Quando in EF6 si chiama `DbSet.Add()` su un'entità viene eseguita una ricerca ricorsiva di tutte le entità a cui si fa riferimento nelle proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="af8ac-115">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="af8ac-116">Anche tutte le entità trovate e non ancora rilevate dal contesto vengono contrassegnate come aggiunte.</span><span class="sxs-lookup"><span data-stu-id="af8ac-116">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="af8ac-117">`DbSet.Attach()` si comporta allo stesso modo, ad eccezione del fatto che tutte le entità sono contrassegnate come non modificate.</span><span class="sxs-lookup"><span data-stu-id="af8ac-117">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="af8ac-118">**EF Core esegue una ricerca ricorsiva simile, ma con alcune regole leggermente diverse.**</span><span class="sxs-lookup"><span data-stu-id="af8ac-118">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="af8ac-119">L'entità radice è sempre nello stato richiesto (aggiunto per `DbSet.Add` e non modificato per `DbSet.Attach`).</span><span class="sxs-lookup"><span data-stu-id="af8ac-119">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="af8ac-120">**Per le entità rilevate durante la ricerca ricorsiva delle proprietà di navigazione:**</span><span class="sxs-lookup"><span data-stu-id="af8ac-120">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="af8ac-121">**Se la chiave primaria dell'entità è generata dall'archivio**</span><span class="sxs-lookup"><span data-stu-id="af8ac-121">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="af8ac-122">Se la chiave primaria non è impostata su un valore, lo stato viene impostato su aggiunto.</span><span class="sxs-lookup"><span data-stu-id="af8ac-122">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="af8ac-123">Il valore della chiave primaria viene considerato come "non impostato" se riceve il valore CLR predefinito per il tipo di proprietà, ad esempio `0` per `int`, `null` per `string` e così via.</span><span class="sxs-lookup"><span data-stu-id="af8ac-123">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="af8ac-124">Se la chiave primaria è impostata su un valore, lo stato è impostato su non modificato.</span><span class="sxs-lookup"><span data-stu-id="af8ac-124">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="af8ac-125">Se la chiave primaria non è generata dal database, l'entità viene impostata con lo stesso stato della radice.</span><span class="sxs-lookup"><span data-stu-id="af8ac-125">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="af8ac-126">Inizializzazione del database Code First</span><span class="sxs-lookup"><span data-stu-id="af8ac-126">Code First database initialization</span></span>

<span data-ttu-id="af8ac-127">**EF6 si basa su varie regole magic che esegue per selezionare la connessione al database e inizializzare il database. Alcune di queste regole sono:**</span><span class="sxs-lookup"><span data-stu-id="af8ac-127">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="af8ac-128">Se non viene eseguita alcuna configurazione, EF6 seleziona un database in SQL Express o in Local DB.</span><span class="sxs-lookup"><span data-stu-id="af8ac-128">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="af8ac-129">Se nel file `App/Web.config` delle applicazioni è disponibile una stringa di connessione con lo stesso nome del contesto, viene usata questa connessione.</span><span class="sxs-lookup"><span data-stu-id="af8ac-129">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="af8ac-130">Se il database non esiste, viene creato.</span><span class="sxs-lookup"><span data-stu-id="af8ac-130">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="af8ac-131">Se nel database non esiste nessuna delle tabelle del modello, viene aggiunto al database lo schema del modello corrente.</span><span class="sxs-lookup"><span data-stu-id="af8ac-131">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="af8ac-132">Se le migrazioni sono attivate, vengono usate per creare il database.</span><span class="sxs-lookup"><span data-stu-id="af8ac-132">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="af8ac-133">Se il database esiste ed EF6 ha creato lo schema in precedenza, viene verificata la compatibilità dello schema con il modello corrente.</span><span class="sxs-lookup"><span data-stu-id="af8ac-133">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="af8ac-134">Se il modello è stato modificato dopo la creazione dello schema, viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="af8ac-134">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="af8ac-135">**EF Core non esegue nessuna di queste regole magic.**</span><span class="sxs-lookup"><span data-stu-id="af8ac-135">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="af8ac-136">La connessione al database deve essere configurata in modo esplicito nel codice.</span><span class="sxs-lookup"><span data-stu-id="af8ac-136">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="af8ac-137">Non viene eseguita alcuna inizializzazione.</span><span class="sxs-lookup"><span data-stu-id="af8ac-137">No initialization is performed.</span></span> <span data-ttu-id="af8ac-138">È necessario usare `DbContext.Database.Migrate()` per applicare le migrazioni (o `DbContext.Database.EnsureCreated()` e `EnsureDeleted()` per creare/eliminare il database senza usare le migrazioni).</span><span class="sxs-lookup"><span data-stu-id="af8ac-138">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="af8ac-139">Convenzione di denominazione della tabella Code First</span><span class="sxs-lookup"><span data-stu-id="af8ac-139">Code First table naming convention</span></span>

<span data-ttu-id="af8ac-140">EF6 elabora il nome della classe di entità in un servizio di pluralizzazione per calcolare il nome predefinito della tabella alla quale è stata mappata l'entità.</span><span class="sxs-lookup"><span data-stu-id="af8ac-140">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="af8ac-141">EF Core usa il nome della proprietà `DbSet` in cui è esposta l'entità nel contesto derivato.</span><span class="sxs-lookup"><span data-stu-id="af8ac-141">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="af8ac-142">Se l'entità non dispone di una proprietà `DbSet` viene usato il nome della classe.</span><span class="sxs-lookup"><span data-stu-id="af8ac-142">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
