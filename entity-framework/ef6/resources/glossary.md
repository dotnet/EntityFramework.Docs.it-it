---
title: Glossario Entity Framework-EF6
description: Glossario di Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: 19d5e9e3a480337c2bcb93be5f989cc622b67dad
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620195"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="f197b-103">Glossario Entity Framework</span><span class="sxs-lookup"><span data-stu-id="f197b-103">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="f197b-104">Code First</span><span class="sxs-lookup"><span data-stu-id="f197b-104">Code First</span></span>
<span data-ttu-id="f197b-105">Creazione di un modello di Entity Framework utilizzando il codice.</span><span class="sxs-lookup"><span data-stu-id="f197b-105">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="f197b-106">Il modello può essere destinato a un database esistente o a un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="f197b-106">The model can target an existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="f197b-107">Contesto</span><span class="sxs-lookup"><span data-stu-id="f197b-107">Context</span></span>
<span data-ttu-id="f197b-108">Classe che rappresenta una sessione con il database, che consente di eseguire query e salvare i dati.</span><span class="sxs-lookup"><span data-stu-id="f197b-108">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="f197b-109">Un contesto deriva dalla classe DbContext o ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="f197b-109">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="f197b-110">Convenzione (Code First)</span><span class="sxs-lookup"><span data-stu-id="f197b-110">Convention (Code First)</span></span>
<span data-ttu-id="f197b-111">Regola che Entity Framework utilizza per dedurre la forma del modello dalle classi.</span><span class="sxs-lookup"><span data-stu-id="f197b-111">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="f197b-112">Database First</span><span class="sxs-lookup"><span data-stu-id="f197b-112">Database First</span></span>
<span data-ttu-id="f197b-113">Creazione di un modello di Entity Framework, usando la finestra di progettazione EF, destinata a un database esistente.</span><span class="sxs-lookup"><span data-stu-id="f197b-113">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="f197b-114">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="f197b-114">Eager loading</span></span>
<span data-ttu-id="f197b-115">Modello di caricamento di dati correlati in cui una query per un tipo di entità carica anche le entità correlate come parte della query.</span><span class="sxs-lookup"><span data-stu-id="f197b-115">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="f197b-116">EF Designer</span><span class="sxs-lookup"><span data-stu-id="f197b-116">EF Designer</span></span>
<span data-ttu-id="f197b-117">Finestra di progettazione visiva in Visual Studio che consente di creare un modello di Entity Framework usando caselle e linee.</span><span class="sxs-lookup"><span data-stu-id="f197b-117">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="f197b-118">Entità</span><span class="sxs-lookup"><span data-stu-id="f197b-118">Entity</span></span>
<span data-ttu-id="f197b-119">Classe o oggetto che rappresenta i dati dell'applicazione come clienti, prodotti e ordini.</span><span class="sxs-lookup"><span data-stu-id="f197b-119">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="f197b-120">Entity Data Model</span><span class="sxs-lookup"><span data-stu-id="f197b-120">Entity Data Model</span></span>
<span data-ttu-id="f197b-121">Modello che descrive le entità e le relazioni tra di esse.</span><span class="sxs-lookup"><span data-stu-id="f197b-121">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="f197b-122">EF utilizza EDM per descrivere il modello concettuale su cui si basano i programmi per sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="f197b-122">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="f197b-123">EDM si basa sul modello Entity Relationship introdotto da Dr. Peter Chen.</span><span class="sxs-lookup"><span data-stu-id="f197b-123">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="f197b-124">EDM è stato originariamente sviluppato con l'obiettivo principale di diventare il modello di dati comune in una suite di tecnologie per sviluppatori e server Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f197b-124">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="f197b-125">EDM viene utilizzato anche come parte del protocollo OData.</span><span class="sxs-lookup"><span data-stu-id="f197b-125">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="f197b-126">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="f197b-126">Explicit loading</span></span>
<span data-ttu-id="f197b-127">Modello di caricamento di dati correlati in cui gli oggetti correlati vengono caricati chiamando un'API.</span><span class="sxs-lookup"><span data-stu-id="f197b-127">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="f197b-128">API Fluent</span><span class="sxs-lookup"><span data-stu-id="f197b-128">Fluent API</span></span>
<span data-ttu-id="f197b-129">API che può essere utilizzata per configurare un modello di Code First.</span><span class="sxs-lookup"><span data-stu-id="f197b-129">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="f197b-130">Associazione di chiavi esterne</span><span class="sxs-lookup"><span data-stu-id="f197b-130">Foreign key association</span></span>
<span data-ttu-id="f197b-131">Associazione tra entità in cui una proprietà che rappresenta la chiave esterna è inclusa nella classe dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="f197b-131">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="f197b-132">Il prodotto, ad esempio, contiene una proprietà CategoryId.</span><span class="sxs-lookup"><span data-stu-id="f197b-132">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="f197b-133">Relazione di identificazione</span><span class="sxs-lookup"><span data-stu-id="f197b-133">Identifying relationship</span></span>
<span data-ttu-id="f197b-134">Relazione in cui la chiave primaria dell'entità principale fa parte della chiave primaria dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="f197b-134">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="f197b-135">In questo tipo di relazione l'entità dipendente non può esistere senza l'entità principale.</span><span class="sxs-lookup"><span data-stu-id="f197b-135">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="f197b-136">Associazione indipendente</span><span class="sxs-lookup"><span data-stu-id="f197b-136">Independent association</span></span>
<span data-ttu-id="f197b-137">Associazione tra entità in cui non è presente alcuna proprietà che rappresenta la chiave esterna nella classe dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="f197b-137">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="f197b-138">Una classe prodotto, ad esempio, contiene una relazione con una categoria, ma nessuna proprietà CategoryId.</span><span class="sxs-lookup"><span data-stu-id="f197b-138">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="f197b-139">Entity Framework tiene traccia dello stato dell'associazione indipendentemente dallo stato delle entità alle due estremità dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="f197b-139">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="f197b-140">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="f197b-140">Lazy loading</span></span>
<span data-ttu-id="f197b-141">Modello di caricamento di dati correlati in cui gli oggetti correlati vengono caricati automaticamente quando si accede a una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="f197b-141">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="f197b-142">Model First</span><span class="sxs-lookup"><span data-stu-id="f197b-142">Model First</span></span>
<span data-ttu-id="f197b-143">Creazione di un modello di Entity Framework, usando la finestra di progettazione EF, che viene quindi usato per creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="f197b-143">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="f197b-144">Proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="f197b-144">Navigation property</span></span>
<span data-ttu-id="f197b-145">Proprietà di un'entità che fa riferimento a un'altra entità.</span><span class="sxs-lookup"><span data-stu-id="f197b-145">A property of an entity that references another entity.</span></span> <span data-ttu-id="f197b-146">Il prodotto, ad esempio, contiene una proprietà di navigazione di categoria e la categoria contiene una proprietà di navigazione Products.</span><span class="sxs-lookup"><span data-stu-id="f197b-146">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="f197b-147">POCO</span><span class="sxs-lookup"><span data-stu-id="f197b-147">POCO</span></span>
<span data-ttu-id="f197b-148">Acronimo dell'oggetto CLR normale.</span><span class="sxs-lookup"><span data-stu-id="f197b-148">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="f197b-149">Una semplice classe utente che non ha dipendenze con alcun Framework.</span><span class="sxs-lookup"><span data-stu-id="f197b-149">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="f197b-150">Nel contesto di EF, una classe di entità che non deriva da EntityObject, implementa qualsiasi interfaccia o contiene attributi definiti in EF.</span><span class="sxs-lookup"><span data-stu-id="f197b-150">In the context of EF, an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="f197b-151">Anche le classi di entità separate dal framework di persistenza sono definite "persistenza ignorata".</span><span class="sxs-lookup"><span data-stu-id="f197b-151">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="f197b-152">Relazione inversa</span><span class="sxs-lookup"><span data-stu-id="f197b-152">Relationship inverse</span></span>
<span data-ttu-id="f197b-153">Estremità opposta di una relazione, ad esempio Product. Categoria e categoria. Prodotto.</span><span class="sxs-lookup"><span data-stu-id="f197b-153">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="f197b-154">Entità con rilevamento automatico</span><span class="sxs-lookup"><span data-stu-id="f197b-154">Self-tracking entity</span></span>
<span data-ttu-id="f197b-155">Entità compilata da un modello di generazione del codice che facilita lo sviluppo a più livelli.</span><span class="sxs-lookup"><span data-stu-id="f197b-155">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="f197b-156">Tipo tabella per calcestruzzo (TPC)</span><span class="sxs-lookup"><span data-stu-id="f197b-156">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="f197b-157">Metodo di mapping dell'ereditarietà in cui ogni tipo non astratto nella gerarchia viene mappato a una tabella separata nel database.</span><span class="sxs-lookup"><span data-stu-id="f197b-157">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="f197b-158">Tabella per gerarchia (TPH)</span><span class="sxs-lookup"><span data-stu-id="f197b-158">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="f197b-159">Metodo di mapping dell'ereditarietà in cui viene eseguito il mapping di tutti i tipi nella gerarchia alla stessa tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="f197b-159">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="f197b-160">Per identificare il tipo a cui è associata ogni riga, viene utilizzata una o più colonne discriminatore.</span><span class="sxs-lookup"><span data-stu-id="f197b-160">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="f197b-161">Tabella per tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="f197b-161">Table-per-type (TPT)</span></span>
<span data-ttu-id="f197b-162">Metodo di mapping dell'ereditarietà in cui viene eseguito il mapping delle proprietà comuni di tutti i tipi nella gerarchia alla stessa tabella nel database, ma le proprietà univoche per ogni tipo vengono mappate a una tabella separata.</span><span class="sxs-lookup"><span data-stu-id="f197b-162">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="f197b-163">Individuazione del tipo</span><span class="sxs-lookup"><span data-stu-id="f197b-163">Type discovery</span></span>
<span data-ttu-id="f197b-164">Processo di identificazione dei tipi che devono far parte di un modello di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f197b-164">The process of identifying the types that should be part of an Entity Framework model.</span></span>
