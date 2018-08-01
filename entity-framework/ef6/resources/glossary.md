---
title: Entity Framework glossario - Entity Framework 6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
caps.latest.revision: 3
ms.openlocfilehash: cbd61838afc23dfb37cee7c624c65476c5270099
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "39121460"
---
# <a name="entity-framework-glossary"></a><span data-ttu-id="2a5e6-102">Glossario di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2a5e6-102">Entity Framework Glossary</span></span>
## <a name="code-first"></a><span data-ttu-id="2a5e6-103">Code First</span><span class="sxs-lookup"><span data-stu-id="2a5e6-103">Code First</span></span>
<span data-ttu-id="2a5e6-104">Creazione di un modello di Entity Framework usando il codice.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-104">Creating an Entity Framework model using code.</span></span> <span data-ttu-id="2a5e6-105">Il modello può avere come destinazione e di database esistente o di un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-105">The model can target and existing database or a new database.</span></span>

## <a name="context"></a><span data-ttu-id="2a5e6-106">Contesto</span><span class="sxs-lookup"><span data-stu-id="2a5e6-106">Context</span></span>
<span data-ttu-id="2a5e6-107">Una classe che rappresenta una sessione con il database, consentendo di eseguire una query e salvare i dati.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-107">A class that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="2a5e6-108">Un contesto deriva dalla classe ObjectContext o DbContext.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-108">A context derives from the DbContext or ObjectContext class.</span></span>

## <a name="convention-code-first"></a><span data-ttu-id="2a5e6-109">Convenzione (Code First)</span><span class="sxs-lookup"><span data-stu-id="2a5e6-109">Convention (Code First)</span></span>
<span data-ttu-id="2a5e6-110">Una regola che usa Entity Framework per dedurre la forma del modello dalle classi.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-110">A rule that Entity Framework uses to infer the shape of you model from your classes.</span></span>

## <a name="database-first"></a><span data-ttu-id="2a5e6-111">Prima di tutto di database</span><span class="sxs-lookup"><span data-stu-id="2a5e6-111">Database First</span></span>
<span data-ttu-id="2a5e6-112">Creazione di un modello di Entity Framework, tramite la finestra di progettazione di Entity Framework, che è destinato a un database esistente.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-112">Creating an Entity Framework model, using the EF Designer, that targets an existing database.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="2a5e6-113">Caricamento eager</span><span class="sxs-lookup"><span data-stu-id="2a5e6-113">Eager loading</span></span>
<span data-ttu-id="2a5e6-114">Modello di caricamento dei dati correlati in una query per un tipo di entità carica anche entità correlate come parte della query.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-114">A pattern of loading related data where a query for one type of entity also loads related entities as part of the query.</span></span>

## <a name="ef-designer"></a><span data-ttu-id="2a5e6-115">Finestra di progettazione di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2a5e6-115">EF Designer</span></span>
<span data-ttu-id="2a5e6-116">Una finestra di progettazione visiva di Visual Studio che consente di creare un modello di Entity Framework usando le caselle e linee.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-116">A visual designer in Visual Studio that allows you to create an Entity Framework model using boxes and lines.</span></span>

## <a name="entity"></a><span data-ttu-id="2a5e6-117">Entità</span><span class="sxs-lookup"><span data-stu-id="2a5e6-117">Entity</span></span>
<span data-ttu-id="2a5e6-118">Classe o oggetto che rappresenta i dati dell'applicazione come clienti, prodotti e ordini.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-118">A class or object that represents application data such as customers, products, and orders.</span></span>

## <a name="entity-data-model"></a><span data-ttu-id="2a5e6-119">Entity Data Model</span><span class="sxs-lookup"><span data-stu-id="2a5e6-119">Entity Data Model</span></span>
<span data-ttu-id="2a5e6-120">Un modello che descrive le entità e le relazioni tra di essi.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-120">A model that describes entities and the relationships between them.</span></span> <span data-ttu-id="2a5e6-121">Entity Framework utilizza EDM per descrivere il modello concettuale in cui i programmi per sviluppatori.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-121">EF uses EDM to describe the conceptual model against which the developer programs.</span></span> <span data-ttu-id="2a5e6-122">EDM si basa sul modello entità-relazione introdotto dal ripristino di emergenza. Peter Chen.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-122">EDM builds on the Entity Relationship model introduced by Dr. Peter Chen.</span></span> <span data-ttu-id="2a5e6-123">Il modello EDM è stato originariamente sviluppato con l'obiettivo primario di divenire common data model in una suite di tecnologie per sviluppatori e il server da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-123">The EDM was originally developed with the primary goal of becoming the common data model across a suite of developer and server technologies from Microsoft.</span></span> <span data-ttu-id="2a5e6-124">Modello EDM viene usato anche come parte del protocollo OData.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-124">EDM is also used as part of the OData protocol.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="2a5e6-125">Caricamento esplicito</span><span class="sxs-lookup"><span data-stu-id="2a5e6-125">Explicit loading</span></span>
<span data-ttu-id="2a5e6-126">Modello di caricamento dei dati correlati in cui gli oggetti correlati vengono caricati chiamando un'API.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-126">A pattern of loading related data where related objects are loaded by calling an API.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="2a5e6-127">API Fluent</span><span class="sxs-lookup"><span data-stu-id="2a5e6-127">Fluent API</span></span>
<span data-ttu-id="2a5e6-128">Un'API che può essere usata per configurare un modello Code First.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-128">An API that can be used to configure a Code First model.</span></span>

## <a name="foreign-key-association"></a><span data-ttu-id="2a5e6-129">Associazione di chiavi esterne</span><span class="sxs-lookup"><span data-stu-id="2a5e6-129">Foreign key association</span></span>
<span data-ttu-id="2a5e6-130">Un'associazione tra entità in cui una proprietà che rappresenta la chiave esterna è incluso nella classe dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-130">An association between entities where a property that represents the foreign key is included in the class of the dependent entity.</span></span> <span data-ttu-id="2a5e6-131">Ad esempio, prodotto contiene una proprietà CategoryId.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-131">For example, Product contains a CategoryId property.</span></span>

## <a name="identifying-relationship"></a><span data-ttu-id="2a5e6-132">Relazione di identificazione</span><span class="sxs-lookup"><span data-stu-id="2a5e6-132">Identifying relationship</span></span>
<span data-ttu-id="2a5e6-133">Relazione in cui la chiave primaria dell'entità principale fa parte della chiave primaria dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-133">A relationship where the primary key of the principal entity is part of the primary key of the dependent entity.</span></span> <span data-ttu-id="2a5e6-134">In questo tipo di relazione, l'entità dipendente non può esistere senza l'entità principale.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-134">In this kind of relationship, the dependent entity cannot exist without the principal entity.</span></span>

## <a name="independent-association"></a><span data-ttu-id="2a5e6-135">Associazione indipendente</span><span class="sxs-lookup"><span data-stu-id="2a5e6-135">Independent association</span></span>
<span data-ttu-id="2a5e6-136">Un'associazione tra entità in cui è presente alcuna proprietà che rappresenta la chiave esterna nella classe dell'entità dipendente.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-136">An association between entities where there is no property representing the foreign key in the class of the dependent entity.</span></span> <span data-ttu-id="2a5e6-137">Ad esempio, una classe di prodotto contiene una relazione per categoria, ma nessuna proprietà CategoryId.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-137">For example, a Product class contains a relationship to Category but no CategoryId property.</span></span> <span data-ttu-id="2a5e6-138">Entity Framework rileva lo stato dell'associazione indipendentemente dallo stato delle entità finali dell'associazione di due.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-138">Entity Framework tracks the state of the association independently of the state of the entities at the two association ends.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="2a5e6-139">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="2a5e6-139">Lazy loading</span></span>
<span data-ttu-id="2a5e6-140">Modello di caricamento dei dati correlati in cui gli oggetti correlati vengono caricati automaticamente quando si accede a una proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-140">A pattern of loading related data where related objects are automatically loaded when a navigation property is accessed.</span></span>

## <a name="model-first"></a><span data-ttu-id="2a5e6-141">A partire dal modello</span><span class="sxs-lookup"><span data-stu-id="2a5e6-141">Model First</span></span>
<span data-ttu-id="2a5e6-142">Creazione di un modello di Entity Framework, tramite la finestra di progettazione di Entity Framework, che viene quindi usato per creare un nuovo database.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-142">Creating an Entity Framework model, using the EF Designer, that is then used to create a new database.</span></span>

## <a name="navigation-property"></a><span data-ttu-id="2a5e6-143">Proprietà di navigazione</span><span class="sxs-lookup"><span data-stu-id="2a5e6-143">Navigation property</span></span>
<span data-ttu-id="2a5e6-144">Proprietà di un'entità a cui fa riferimento a un'altra entità.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-144">A property of an entity that references another entity.</span></span> <span data-ttu-id="2a5e6-145">Ad esempio, prodotto contiene una proprietà di navigazione di categoria e categoria contiene una proprietà di navigazione di prodotti.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-145">For example, Product contains a Category navigation property and Category contains a Products navigation property.</span></span>

## <a name="poco"></a><span data-ttu-id="2a5e6-146">POCO</span><span class="sxs-lookup"><span data-stu-id="2a5e6-146">POCO</span></span>
<span data-ttu-id="2a5e6-147">Acronimo di Plain-Old CLR Object.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-147">Acronym for Plain-Old CLR Object.</span></span> <span data-ttu-id="2a5e6-148">Classe utente semplice che non ha dipendenze con qualsiasi framework.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-148">A simple user class that has no dependencies with any framework.</span></span> <span data-ttu-id="2a5e6-149">Nel contesto di Entity Framework, una classe di entità che non deriva da EntityObject, implementa le interfacce o contiene tutti gli attributi definiti in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-149">In the context of EF, a an entity class that does not derive from EntityObject, implements any interfaces or carries any attributes defined in EF.</span></span> <span data-ttu-id="2a5e6-150">Tali classi di entità che vengono disaccoppiati da framework di persistenza sono anche detto "non riconoscono la persistenza".</span><span class="sxs-lookup"><span data-stu-id="2a5e6-150">Such entity classes that are decoupled from the persistence framework are also said to be "persistence ignorant".</span></span>  

## <a name="relationship-inverse"></a><span data-ttu-id="2a5e6-151">Inverso di relazione</span><span class="sxs-lookup"><span data-stu-id="2a5e6-151">Relationship inverse</span></span>
<span data-ttu-id="2a5e6-152">Estremo opposto di una relazione, ad esempio, di prodotto. Categoria e la categoria. Prodotto.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-152">The opposite end of a relationship, for example, product.Category and category.Product.</span></span>

## <a name="self-tracking-entity"></a><span data-ttu-id="2a5e6-153">Entità con rilevamento automatico</span><span class="sxs-lookup"><span data-stu-id="2a5e6-153">Self-tracking entity</span></span>
<span data-ttu-id="2a5e6-154">Un'entità compilata da un modello di generazione di codice che consente lo sviluppo a N livelli.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-154">An entity built from a code generation template that helps with N-Tier development.</span></span>

## <a name="table-per-concrete-type-tpc"></a><span data-ttu-id="2a5e6-155">Tipo di tabella per tipo concreto (TP)</span><span class="sxs-lookup"><span data-stu-id="2a5e6-155">Table-per-concrete type (TPC)</span></span>
<span data-ttu-id="2a5e6-156">Un metodo di mapping dell'ereditarietà in cui viene eseguito il mapping ciascun tipo non astratto nella gerarchia per separare tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-156">A method of mapping the inheritance where each non-abstract type in the hierarchy is mapped to separate table in the database.</span></span>

## <a name="table-per-hierarchy-tph"></a><span data-ttu-id="2a5e6-157">Tabella per gerarchia (TPH)</span><span class="sxs-lookup"><span data-stu-id="2a5e6-157">Table-per-hierarchy (TPH)</span></span>
<span data-ttu-id="2a5e6-158">Un metodo di mapping dell'ereditarietà in cui tutti i tipi nella gerarchia vengono mappati alla stessa tabella nel database.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-158">A method of mapping the inheritance where all types in the hierarchy are mapped to the same table in the database.</span></span> <span data-ttu-id="2a5e6-159">Discriminatore colonna/e viene usato per identificare il tipo di ogni riga è associato.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-159">A discriminator column(s) is used to identify what type each row is associated with.</span></span>

## <a name="table-per-type-tpt"></a><span data-ttu-id="2a5e6-160">Tabella per tipo TPT)</span><span class="sxs-lookup"><span data-stu-id="2a5e6-160">Table-per-type (TPT)</span></span>
<span data-ttu-id="2a5e6-161">Un metodo di mapping dell'ereditarietà in cui le proprietà comuni di tutti i tipi nella gerarchia vengono mappate alla stessa tabella nel database, ma le proprietà univoche per ogni tipo vengono eseguito il mapping a una tabella separata.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-161">A method of mapping the inheritance where the common properties of all types in the hierarchy are mapped to the same table in the database, but properties unique to each type are mapped to a separate table.</span></span>

## <a name="type-discovery"></a><span data-ttu-id="2a5e6-162">Individuazione del tipo</span><span class="sxs-lookup"><span data-stu-id="2a5e6-162">Type discovery</span></span>
<span data-ttu-id="2a5e6-163">Il processo di identificazione di tipi che devono far parte di un modello di Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2a5e6-163">The process of identifying the types that should be part of an Entity Framework model.</span></span>