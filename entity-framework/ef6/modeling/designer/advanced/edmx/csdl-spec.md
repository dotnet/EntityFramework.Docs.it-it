---
title: Specifica CSDL-EF6
description: Specifica CSDL in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: a4abfcd6438ca92fff4448844e0812060b1ae029
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070406"
---
# <a name="csdl-specification"></a><span data-ttu-id="ee242-103">Specifica CSDL</span><span class="sxs-lookup"><span data-stu-id="ee242-103">CSDL Specification</span></span>
<span data-ttu-id="ee242-104">Conceptual Schema Definition Language (CSDL) è un linguaggio basato su XML che descrive le entità, le relazioni e le funzioni che costituiscono un modello concettuale di un'applicazione basata sui dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="ee242-105">Questo modello concettuale può essere utilizzato dal Entity Framework o WCF Data Services.</span><span class="sxs-lookup"><span data-stu-id="ee242-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="ee242-106">I metadati descritti con CSDL vengono utilizzati dalla Entity Framework per eseguire il mapping di entità e relazioni definite in un modello concettuale a un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="ee242-107">Per ulteriori informazioni, vedere [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) e [specifica MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span><span class="sxs-lookup"><span data-stu-id="ee242-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="ee242-108">CSDL è l'implementazione Entity Framework dell'Entity Data Model.</span><span class="sxs-lookup"><span data-stu-id="ee242-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="ee242-109">In un'applicazione Entity Framework, i metadati del modello concettuale vengono caricati da un file con estensione CSDL (scritto in CSDL) in un'istanza di System. Data. Metadata. Edm. EdmItemCollection ed è possibile accedervi usando i metodi della classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="ee242-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="ee242-110">Entity Framework usa i metadati del modello concettuale per tradurre le query sul modello concettuale in comandi specifici dell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="ee242-111">La finestra di progettazione EF archivia le informazioni sul modello concettuale in un file con estensione edmx in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="ee242-112">In fase di compilazione, la finestra di progettazione EF utilizza le informazioni in un file con estensione edmx per creare il file con estensione csdl necessario per Entity Framework in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="ee242-113">Le versioni di CSDL si differenziano tra loro per gli spazi dei nomi XML.</span><span class="sxs-lookup"><span data-stu-id="ee242-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="ee242-114">Versione CSDL</span><span class="sxs-lookup"><span data-stu-id="ee242-114">CSDL Version</span></span> | <span data-ttu-id="ee242-115">Spazio dei nomi XML</span><span class="sxs-lookup"><span data-stu-id="ee242-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="ee242-116">CSDL V1</span><span class="sxs-lookup"><span data-stu-id="ee242-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="ee242-117">CSDL V2</span><span class="sxs-lookup"><span data-stu-id="ee242-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="ee242-118">CSDL V3</span><span class="sxs-lookup"><span data-stu-id="ee242-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="ee242-119">Elemento Association (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-119">Association Element (CSDL)</span></span>

<span data-ttu-id="ee242-120">Un elemento **Association** definisce una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="ee242-121">Un'associazione deve specificare i tipi di entità coinvolti nella relazione e il possibile numero di tipi di entità a ogni entità finale della relazione, che è noto come molteplicità.</span><span class="sxs-lookup"><span data-stu-id="ee242-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="ee242-122">La molteplicità di un'entità finale di un'associazione può avere un valore pari a uno (1), zero o uno (0.. 1) o molti ( \* ).</span><span class="sxs-lookup"><span data-stu-id="ee242-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="ee242-123">Queste informazioni vengono specificate in due elementi End figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="ee242-124">Le istanze del tipo di entità in corrispondenza di un'entità finale di un'associazione sono accessibili attraverso proprietà di navigazione o chiavi esterne se sono esposte in un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="ee242-125">In un'applicazione, un'istanza di un'associazione rappresenta un'associazione specifica tra istanze di tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="ee242-126">Le istanze dell'associazione sono raggruppate logicamente in un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="ee242-127">Un elemento **Association** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-128">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-129">End (esattamente 2 elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="ee242-130">ReferentialConstraint (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="ee242-131">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-132">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-132">Applicable Attributes</span></span>

<span data-ttu-id="ee242-133">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="ee242-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="ee242-134">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-134">Attribute Name</span></span> | <span data-ttu-id="ee242-135">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-135">Is Required</span></span> | <span data-ttu-id="ee242-136">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="ee242-137">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-137">**Name**</span></span>       | <span data-ttu-id="ee242-138">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-138">Yes</span></span>         | <span data-ttu-id="ee242-139">Nome dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-140">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="ee242-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="ee242-141">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-142">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-143">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-143">Example</span></span>

<span data-ttu-id="ee242-144">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** quando le chiavi esterne non sono state esposte sui tipi di entità **Customer** e **Order** .</span><span class="sxs-lookup"><span data-stu-id="ee242-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="ee242-145">I valori di **molteplicità** per ogni **estremità** dell'associazione indicano che molti **ordini** possono essere associati a un **cliente**, ma solo un **cliente** può essere associato a un **ordine**.</span><span class="sxs-lookup"><span data-stu-id="ee242-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="ee242-146">Inoltre, l'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati se il **cliente** viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="ee242-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="ee242-147">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** quando le chiavi esterne sono state esposte sui tipi di entità **Customer** e **Order** .</span><span class="sxs-lookup"><span data-stu-id="ee242-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="ee242-148">Con le chiavi esterne esposte, la relazione tra le entità viene gestita con un elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="ee242-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="ee242-149">Un elemento AssociationSetMapping corrispondente non è necessario per eseguire il mapping di questa associazione all'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="ee242-150">Elemento AssociationSet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="ee242-151">L'elemento **associativo** in Conceptual Schema Definition Language (CSDL) è un contenitore logico per le istanze di associazione dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="ee242-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="ee242-152">Un set di associazioni fornisce una definizione per il raggruppamento di istanze di associazioni in modo che possano essere mappate a un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="ee242-153">L'elemento **associationname** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-154">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="ee242-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ee242-155">End (esattamente due elementi richiesti)</span><span class="sxs-lookup"><span data-stu-id="ee242-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="ee242-156">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="ee242-157">L'attributo **Association** specifica il tipo di associazione contenuto in un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="ee242-158">I set di entità che costituiscono le entità finali di un set di associazioni vengono specificati con esattamente due elementi **end** figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-159">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-159">Applicable Attributes</span></span>

<span data-ttu-id="ee242-160">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="ee242-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="ee242-161">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-161">Attribute Name</span></span>  | <span data-ttu-id="ee242-162">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-162">Is Required</span></span> | <span data-ttu-id="ee242-163">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-164">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-164">**Name**</span></span>        | <span data-ttu-id="ee242-165">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-165">Yes</span></span>         | <span data-ttu-id="ee242-166">Nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-166">The name of the entity set.</span></span> <span data-ttu-id="ee242-167">Il valore dell'attributo **Name** non può corrispondere al valore dell'attributo **Association** .</span><span class="sxs-lookup"><span data-stu-id="ee242-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="ee242-168">**Associazione**</span><span class="sxs-lookup"><span data-stu-id="ee242-168">**Association**</span></span> | <span data-ttu-id="ee242-169">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-169">Yes</span></span>         | <span data-ttu-id="ee242-170">Il nome completo dell'associazione le cui istanze sono contenute nel set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="ee242-171">L'associazione deve essere nello stesso spazio dei nomi del set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-172">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="ee242-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="ee242-173">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-174">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-175">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-175">Example</span></span>

<span data-ttu-id="ee242-176">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con due elementi di **associazione** :</span><span class="sxs-lookup"><span data-stu-id="ee242-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="ee242-177">Elemento CollectionType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="ee242-178">L'elemento **CollectionType** in Conceptual Schema Definition Language (CSDL) specifica che un parametro di funzione o un tipo restituito della funzione è una raccolta.</span><span class="sxs-lookup"><span data-stu-id="ee242-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="ee242-179">L'elemento **CollectionType** può essere un elemento figlio dell'elemento Parameter o dell'elemento ReturnType (Function).</span><span class="sxs-lookup"><span data-stu-id="ee242-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="ee242-180">Il tipo di raccolta può essere specificato tramite l'attributo **Type** o uno degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="ee242-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="ee242-181">**CollectionType**</span></span>
-   <span data-ttu-id="ee242-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="ee242-182">ReferenceType</span></span>
-   <span data-ttu-id="ee242-183">RowType</span><span class="sxs-lookup"><span data-stu-id="ee242-183">RowType</span></span>
-   <span data-ttu-id="ee242-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="ee242-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-185">Un modello non convaliderà se il tipo di una raccolta viene specificato sia con l'attributo di **tipo** che con un elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-186">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-186">Applicable Attributes</span></span>

<span data-ttu-id="ee242-187">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="ee242-188">Si noti che gli attributi **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**e **Collation** sono applicabili solo alle raccolte di **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="ee242-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="ee242-189">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-189">Attribute Name</span></span>                                                          | <span data-ttu-id="ee242-190">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-190">Is Required</span></span> | <span data-ttu-id="ee242-191">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-192">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-192">**Type**</span></span>                                                                | <span data-ttu-id="ee242-193">No</span><span class="sxs-lookup"><span data-stu-id="ee242-193">No</span></span>          | <span data-ttu-id="ee242-194">Tipo della raccolta.</span><span class="sxs-lookup"><span data-stu-id="ee242-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="ee242-195">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="ee242-195">**Nullable**</span></span>                                                            | <span data-ttu-id="ee242-196">No</span><span class="sxs-lookup"><span data-stu-id="ee242-196">No</span></span>          | <span data-ttu-id="ee242-197">**True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="ee242-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="ee242-198">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="ee242-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="ee242-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="ee242-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="ee242-200">No</span><span class="sxs-lookup"><span data-stu-id="ee242-200">No</span></span>          | <span data-ttu-id="ee242-201">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="ee242-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="ee242-203">No</span><span class="sxs-lookup"><span data-stu-id="ee242-203">No</span></span>          | <span data-ttu-id="ee242-204">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="ee242-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="ee242-206">No</span><span class="sxs-lookup"><span data-stu-id="ee242-206">No</span></span>          | <span data-ttu-id="ee242-207">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="ee242-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="ee242-208">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-208">**Precision**</span></span>                                                           | <span data-ttu-id="ee242-209">No</span><span class="sxs-lookup"><span data-stu-id="ee242-209">No</span></span>          | <span data-ttu-id="ee242-210">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="ee242-211">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-211">**Scale**</span></span>                                                               | <span data-ttu-id="ee242-212">No</span><span class="sxs-lookup"><span data-stu-id="ee242-212">No</span></span>          | <span data-ttu-id="ee242-213">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="ee242-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-214">**SRID**</span></span>                                                                | <span data-ttu-id="ee242-215">No</span><span class="sxs-lookup"><span data-stu-id="ee242-215">No</span></span>          | <span data-ttu-id="ee242-216">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ee242-217">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="ee242-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="ee242-218">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="ee242-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="ee242-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ee242-219">**Unicode**</span></span>                                                             | <span data-ttu-id="ee242-220">No</span><span class="sxs-lookup"><span data-stu-id="ee242-220">No</span></span>          | <span data-ttu-id="ee242-221">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="ee242-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="ee242-222">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="ee242-222">**Collation**</span></span>                                                           | <span data-ttu-id="ee242-223">No</span><span class="sxs-lookup"><span data-stu-id="ee242-223">No</span></span>          | <span data-ttu-id="ee242-224">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="ee242-225">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="ee242-226">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-227">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-228">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-228">Example</span></span>

<span data-ttu-id="ee242-229">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di tipi di entità **Person** (come specificato con l'attributo **elementType** ).</span><span class="sxs-lookup"><span data-stu-id="ee242-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="ee242-230">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="ee242-231">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza l'elemento **CollectionType** per specificare che la funzione accetta come parametro una raccolta di tipi di entità **Department** .</span><span class="sxs-lookup"><span data-stu-id="ee242-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="ee242-232">Elemento ComplexType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="ee242-233">Un elemento **complexType** definisce una struttura di dati composta da proprietà **EDMSimpleType** o altri tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="ee242-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="ee242-234">Un tipo complesso può essere una proprietà di un tipo di entità o un altro tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="ee242-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="ee242-235">Un tipo complesso è simile a un tipo di entità in quanto il tipo complesso definisce i dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="ee242-236">Tuttavia esistono alcune differenze importanti tra i tipi complessi e i tipi di entità:</span><span class="sxs-lookup"><span data-stu-id="ee242-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="ee242-237">I tipi complessi non dispongono di identità o chiavi e pertanto non possono esistere indipendentemente.</span><span class="sxs-lookup"><span data-stu-id="ee242-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="ee242-238">I tipi complessi possono esistere solo come proprietà di tipi di entità o gli altri tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="ee242-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="ee242-239">I tipi complessi non possono far parte di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="ee242-240">Le entità finali di un'associazione non possono essere tipi complessi, di conseguenza non è possibile definire proprietà di navigazione per i tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="ee242-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="ee242-241">Una proprietà di tipo complesso non può avere un valore null, sebbene ogni proprietà scalare di un tipo complesso possa essere impostata su Null.</span><span class="sxs-lookup"><span data-stu-id="ee242-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="ee242-242">Un elemento **complexType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-243">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-244">Property (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="ee242-245">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="ee242-246">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **complexType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="ee242-247">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="ee242-248">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-248">Is Required</span></span> | <span data-ttu-id="ee242-249">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-250">Nome</span><span class="sxs-lookup"><span data-stu-id="ee242-250">Name</span></span>                                                                                                           | <span data-ttu-id="ee242-251">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-251">Yes</span></span>         | <span data-ttu-id="ee242-252">Nome del tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="ee242-252">The name of the complex type.</span></span> <span data-ttu-id="ee242-253">Il nome di un tipo complesso non può essere uguale a quello di un altro tipo complesso, di un tipo di entità o di un'associazione che si trova entro l'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="ee242-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="ee242-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="ee242-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="ee242-255">No</span><span class="sxs-lookup"><span data-stu-id="ee242-255">No</span></span>          | <span data-ttu-id="ee242-256">Nome di un altro tipo complesso che è il tipo di base del tipo complesso definito.</span><span class="sxs-lookup"><span data-stu-id="ee242-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="ee242-257">> questo attributo non è applicabile in CSDL V1.</span><span class="sxs-lookup"><span data-stu-id="ee242-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="ee242-258">L'ereditarietà per i tipi complessi non è supportata in quella versione.</span><span class="sxs-lookup"><span data-stu-id="ee242-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="ee242-259">Contenuto</span><span class="sxs-lookup"><span data-stu-id="ee242-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="ee242-260">No</span><span class="sxs-lookup"><span data-stu-id="ee242-260">No</span></span>          | <span data-ttu-id="ee242-261">**True** o **false** (valore predefinito) a seconda che il tipo complesso sia un tipo astratto.</span><span class="sxs-lookup"><span data-stu-id="ee242-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="ee242-262">> questo attributo non è applicabile in CSDL V1.</span><span class="sxs-lookup"><span data-stu-id="ee242-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="ee242-263">I tipi complessi in quella versione non possono essere tipi astratti.</span><span class="sxs-lookup"><span data-stu-id="ee242-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="ee242-264">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **complexType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="ee242-265">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-266">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-267">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-267">Example</span></span>

<span data-ttu-id="ee242-268">Nell'esempio seguente viene illustrato un tipo complesso, **Address**, con le proprietà **EDMSimpleType** **StreetAddress**, **City**, **StateOrProvince**, **Country**e **PostalCode**.</span><span class="sxs-lookup"><span data-stu-id="ee242-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="ee242-269">Per definire l' **Indirizzo** del tipo complesso (sopra) come proprietà di un tipo di entità, è necessario dichiarare il tipo di proprietà nella definizione del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="ee242-270">Nell'esempio seguente viene illustrata la proprietà **Address** come tipo complesso in un tipo di entità (**Publisher**):</span><span class="sxs-lookup"><span data-stu-id="ee242-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="ee242-271">Elemento DefiningExpression (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="ee242-272">L'elemento **DefiningExpression** in Conceptual Schema Definition Language (CSDL) contiene un'espressione Entity SQL che definisce una funzione nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="ee242-273">Ai fini della convalida, un elemento **DefiningExpression** può contenere contenuto arbitrario.</span><span class="sxs-lookup"><span data-stu-id="ee242-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="ee242-274">Tuttavia, Entity Framework genererà un'eccezione in fase di esecuzione se un elemento **DefiningExpression** non contiene Entity SQL validi.</span><span class="sxs-lookup"><span data-stu-id="ee242-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-275">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-275">Applicable Attributes</span></span>

<span data-ttu-id="ee242-276">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **DefiningExpression** .</span><span class="sxs-lookup"><span data-stu-id="ee242-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="ee242-277">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-278">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-279">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-279">Example</span></span>

<span data-ttu-id="ee242-280">Nell'esempio seguente viene utilizzato un elemento **DefiningExpression** per definire una funzione che restituisce il numero di anni da quando un libro è stato pubblicato.</span><span class="sxs-lookup"><span data-stu-id="ee242-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="ee242-281">Il contenuto dell'elemento **DefiningExpression** è scritto in Entity SQL.</span><span class="sxs-lookup"><span data-stu-id="ee242-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="ee242-282">Elemento Dependent (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="ee242-283">L'elemento **dipendente** in Conceptual Schema Definition Language (CSDL) è un elemento figlio dell'elemento ReferentialConstraint e definisce l'entità finale dipendente di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="ee242-284">Un elemento **ReferentialConstraint** definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="ee242-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="ee242-285">Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="ee242-286">Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="ee242-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="ee242-287">Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="ee242-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="ee242-288">Gli elementi **PropertyRef** vengono utilizzati per specificare quali chiavi fanno riferimento all'entità finale principale.</span><span class="sxs-lookup"><span data-stu-id="ee242-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="ee242-289">L'elemento **dipendente** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-290">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="ee242-291">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-292">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-292">Applicable Attributes</span></span>

<span data-ttu-id="ee242-293">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="ee242-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="ee242-294">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-294">Attribute Name</span></span> | <span data-ttu-id="ee242-295">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-295">Is Required</span></span> | <span data-ttu-id="ee242-296">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="ee242-297">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="ee242-297">**Role**</span></span>       | <span data-ttu-id="ee242-298">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-298">Yes</span></span>         | <span data-ttu-id="ee242-299">Nome del tipo di entità in un'entità finale dipendente dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-300">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="ee242-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="ee242-301">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-302">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-303">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-303">Example</span></span>

<span data-ttu-id="ee242-304">Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** usato come parte della definizione dell'associazione **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="ee242-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="ee242-305">La proprietà **publisherID** del tipo di entità **book** costituisce l'entità finale dipendente del vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="ee242-306">Elemento Documentation (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="ee242-307">L'elemento **Documentation** in Conceptual Schema Definition Language (CSDL) può essere utilizzato per fornire informazioni su un oggetto definito in un elemento padre.</span><span class="sxs-lookup"><span data-stu-id="ee242-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="ee242-308">In un file con estensione edmx, quando l'elemento **Documentation** è un elemento figlio di un elemento che viene visualizzato come oggetto nell'area di progettazione di Entity Framework Designer, ad esempio un'entità, un'associazione o una proprietà, il contenuto dell'elemento **Documentation** verrà visualizzato nella finestra **proprietà** di Visual Studio per l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="ee242-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="ee242-309">L'elemento **Documentation** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-310">**Riepilogo**: breve descrizione dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="ee242-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="ee242-311">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="ee242-311">(zero or one element)</span></span>
-   <span data-ttu-id="ee242-312">**LongDescription**: Descrizione completa dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="ee242-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="ee242-313">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="ee242-313">(zero or one element)</span></span>
-   <span data-ttu-id="ee242-314">Elementi Annotation.</span><span class="sxs-lookup"><span data-stu-id="ee242-314">Annotation elements.</span></span> <span data-ttu-id="ee242-315">Zero o più elementi.</span><span class="sxs-lookup"><span data-stu-id="ee242-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-316">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-316">Applicable Attributes</span></span>

<span data-ttu-id="ee242-317">All'elemento **Documentation** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="ee242-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="ee242-318">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-319">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-320">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-320">Example</span></span>

<span data-ttu-id="ee242-321">Nell'esempio seguente viene illustrato l'elemento **Documentation** come elemento figlio di un elemento EntityType.</span><span class="sxs-lookup"><span data-stu-id="ee242-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="ee242-322">Se il frammento di codice seguente si trova nel contenuto CSDL di un file con estensione edmx, il contenuto degli elementi **Summary** e **LongDescription** verrà visualizzato nella finestra **proprietà** di Visual Studio quando si fa clic sul `Customer` tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="ee242-323">Elemento End (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-323">End Element (CSDL)</span></span>

<span data-ttu-id="ee242-324">L'elemento **end** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento Association o dell'elemento associationname.</span><span class="sxs-lookup"><span data-stu-id="ee242-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="ee242-325">In ogni caso, il ruolo dell'elemento **end** è diverso e gli attributi applicabili sono diversi.</span><span class="sxs-lookup"><span data-stu-id="ee242-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="ee242-326">Elemento End come figlio dell'elemento Association</span><span class="sxs-lookup"><span data-stu-id="ee242-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="ee242-327">Un elemento **end** (come figlio dell'elemento **Association** ) identifica il tipo di entità in un'entità finale di un'associazione e il numero di istanze del tipo di entità che possono esistere a tale estremità di un'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="ee242-328">Le entità finali dell'associazione sono definite come parte di un'associazione; un'associazione deve disporre esattamente di due entità finali.</span><span class="sxs-lookup"><span data-stu-id="ee242-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="ee242-329">Le istanze del tipo di entità in corrispondenza di un'entità finale di un'associazione sono accessibili attraverso proprietà di navigazione o chiavi esterne se sono esposte in un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="ee242-330">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-331">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-332">OnDelete (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="ee242-333">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="ee242-334">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-334">Applicable Attributes</span></span>

<span data-ttu-id="ee242-335">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **finale** quando è figlio di un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="ee242-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="ee242-336">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-336">Attribute Name</span></span>   | <span data-ttu-id="ee242-337">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-337">Is Required</span></span> | <span data-ttu-id="ee242-338">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-339">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-339">**Type**</span></span>         | <span data-ttu-id="ee242-340">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-340">Yes</span></span>         | <span data-ttu-id="ee242-341">Nome del tipo di entità in una entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="ee242-342">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="ee242-342">**Role**</span></span>         | <span data-ttu-id="ee242-343">No</span><span class="sxs-lookup"><span data-stu-id="ee242-343">No</span></span>          | <span data-ttu-id="ee242-344">Nome per l'entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-344">A name for the association end.</span></span> <span data-ttu-id="ee242-345">Se non è fornito alcun nome, verrà utilizzato il nome del tipo di entità nell'entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="ee242-346">**Molteplicità**</span><span class="sxs-lookup"><span data-stu-id="ee242-346">**Multiplicity**</span></span> | <span data-ttu-id="ee242-347">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-347">Yes</span></span>         | <span data-ttu-id="ee242-348">**1**, **0.. 1**o a **\*** seconda del numero di istanze del tipo di entità che possono trovarsi alla fine dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="ee242-349">**1** indica che nell'entità finale dell'associazione esiste esattamente un'istanza del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="ee242-350">**0.. 1** indica che nell'entità finale dell'associazione sono presenti zero o un'istanza del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="ee242-351">**\*** indica che nell'entità finale dell'associazione sono presenti zero, una o più istanze del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-352">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="ee242-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="ee242-353">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-354">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ee242-355">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-355">Example</span></span>

<span data-ttu-id="ee242-356">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="ee242-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="ee242-357">I valori di **molteplicità** per ogni **estremità** dell'associazione indicano che molti **ordini** possono essere associati a un **cliente**, ma solo un **cliente** può essere associato a un **ordine**.</span><span class="sxs-lookup"><span data-stu-id="ee242-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="ee242-358">Inoltre, l'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati se il **cliente** viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="ee242-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="ee242-359">Elemento End come figlio dell'elemento AssociationSet</span><span class="sxs-lookup"><span data-stu-id="ee242-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="ee242-360">L'elemento **end** specifica un'estremità di un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="ee242-361">L'elemento **associationname** deve contenere due elementi **end** .</span><span class="sxs-lookup"><span data-stu-id="ee242-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="ee242-362">Le informazioni contenute in un elemento **end** vengono utilizzate per eseguire il mapping di un set di associazioni a un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="ee242-363">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-364">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-365">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-366">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="ee242-367">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="ee242-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ee242-368">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-368">Applicable Attributes</span></span>

<span data-ttu-id="ee242-369">Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **finale** quando è figlio di un elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="ee242-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="ee242-370">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-370">Attribute Name</span></span> | <span data-ttu-id="ee242-371">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-371">Is Required</span></span> | <span data-ttu-id="ee242-372">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="ee242-373">**EntitySet**</span></span>  | <span data-ttu-id="ee242-374">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-374">Yes</span></span>         | <span data-ttu-id="ee242-375">Nome dell'elemento **EntitySet** che definisce un'entità finale dell'elemento di **associazione** padre.</span><span class="sxs-lookup"><span data-stu-id="ee242-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="ee242-376">L'elemento **EntitySet** deve essere definito nello stesso contenitore di entità dell'elemento di **associazione** padre.</span><span class="sxs-lookup"><span data-stu-id="ee242-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="ee242-377">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="ee242-377">**Role**</span></span>       | <span data-ttu-id="ee242-378">No</span><span class="sxs-lookup"><span data-stu-id="ee242-378">No</span></span>          | <span data-ttu-id="ee242-379">Nome dell'entità finale del set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-379">The name of the association set end.</span></span> <span data-ttu-id="ee242-380">Se non si utilizza l'attributo **Role** , il nome dell'entità finale del set di associazioni sarà il nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ee242-381">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="ee242-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="ee242-382">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-383">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ee242-384">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-384">Example</span></span>

<span data-ttu-id="ee242-385">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con due elementi di **associazione** , ognuno con due elementi **end** :</span><span class="sxs-lookup"><span data-stu-id="ee242-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="ee242-386">Elemento EntityContainer (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="ee242-387">L'elemento **EntityContainer** in Conceptual Schema Definition Language (CSDL) è un contenitore logico per set di entità, set di associazioni e importazioni di funzioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="ee242-388">Un contenitore di entità del modello concettuale esegue il mapping a un contenitore di entità del modello di archiviazione attraverso l'elemento EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="ee242-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="ee242-389">Un contenitore di entità del modello di archiviazione descrive la struttura del database: i set di entità descrivono le tabelle, i set di associazioni descrivono i vincoli delle chiavi esterne e le importazioni di funzioni descrivono le stored procedure in un database.</span><span class="sxs-lookup"><span data-stu-id="ee242-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="ee242-390">Un elemento **EntityContainer** può avere uno o più elementi di documentazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="ee242-391">Se è presente un elemento di **documentazione** , deve precedere tutti gli elementi **EntitySet**, **associationname**e **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="ee242-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="ee242-392">Un elemento **EntityContainer** può avere zero o più degli elementi figlio seguenti (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="ee242-393">EntitySet</span></span>
-   <span data-ttu-id="ee242-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="ee242-394">AssociationSet</span></span>
-   <span data-ttu-id="ee242-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="ee242-395">FunctionImport</span></span>
-   <span data-ttu-id="ee242-396">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="ee242-396">Annotation elements</span></span>

<span data-ttu-id="ee242-397">È possibile estendere un elemento **EntityContainer** per includere il contenuto di un altro oggetto **EntityContainer** che si trova all'interno dello stesso spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ee242-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="ee242-398">Per includere il contenuto di un altro **EntityContainer**, nell'elemento **EntityContainer** di riferimento, impostare il valore dell'attributo **extends** sul nome dell'elemento **EntityContainer** che si desidera includere.</span><span class="sxs-lookup"><span data-stu-id="ee242-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="ee242-399">Tutti gli elementi figlio dell'elemento **EntityContainer** incluso verranno considerati come elementi figlio dell'elemento **EntityContainer** di riferimento.</span><span class="sxs-lookup"><span data-stu-id="ee242-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-400">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-400">Applicable Attributes</span></span>

<span data-ttu-id="ee242-401">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="ee242-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="ee242-402">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-402">Attribute Name</span></span> | <span data-ttu-id="ee242-403">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-403">Is Required</span></span> | <span data-ttu-id="ee242-404">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="ee242-405">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-405">**Name**</span></span>       | <span data-ttu-id="ee242-406">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-406">Yes</span></span>         | <span data-ttu-id="ee242-407">Nome del contenitore di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="ee242-408">**Estende**</span><span class="sxs-lookup"><span data-stu-id="ee242-408">**Extends**</span></span>    | <span data-ttu-id="ee242-409">No</span><span class="sxs-lookup"><span data-stu-id="ee242-409">No</span></span>          | <span data-ttu-id="ee242-410">Nome di un altro contenitore di entità all'interno dello stesso spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ee242-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-411">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="ee242-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="ee242-412">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-413">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-414">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-414">Example</span></span>

<span data-ttu-id="ee242-415">Nell'esempio seguente viene illustrato un elemento **EntityContainer** che definisce tre set di entità e due set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="ee242-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="ee242-416">Elemento EntitySet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="ee242-417">L'elemento **EntitySet** in Conceptual Schema Definition Language è un contenitore logico per le istanze di un tipo di entità e le istanze di qualsiasi tipo derivato da tale tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="ee242-418">La relazione tra un tipo di entità e un set di entità è analoga alla relazione tra una riga e una tabella in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="ee242-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="ee242-419">Analogamente a una riga, un tipo di entità definisce un set di dati correlati e, analogamente a una tabella, un set di entità contiene istanze di quella definizione.</span><span class="sxs-lookup"><span data-stu-id="ee242-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="ee242-420">Un set di entità fornisce un construct per il raggruppamento di istanze del tipo di entità in modo che se ne possa eseguire il mapping alle strutture dei dati correlati in un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="ee242-421">È possibile definire più set di entità per un particolare tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-422">EF designer non supporta i modelli concettuali che contengono Multiple Entity Sets per Type.</span><span class="sxs-lookup"><span data-stu-id="ee242-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="ee242-423">L'elemento **EntitySet** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-424">Elemento Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="ee242-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ee242-425">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-426">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-426">Applicable Attributes</span></span>

<span data-ttu-id="ee242-427">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="ee242-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="ee242-428">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-428">Attribute Name</span></span> | <span data-ttu-id="ee242-429">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-429">Is Required</span></span> | <span data-ttu-id="ee242-430">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-431">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-431">**Name**</span></span>       | <span data-ttu-id="ee242-432">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-432">Yes</span></span>         | <span data-ttu-id="ee242-433">Nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="ee242-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="ee242-434">**EntityType**</span></span> | <span data-ttu-id="ee242-435">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-435">Yes</span></span>         | <span data-ttu-id="ee242-436">Nome completo del tipo di entità per il quale il set di entità contiene delle istanze.</span><span class="sxs-lookup"><span data-stu-id="ee242-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-437">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="ee242-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="ee242-438">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-439">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-440">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-440">Example</span></span>

<span data-ttu-id="ee242-441">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con tre elementi **EntitySet** :</span><span class="sxs-lookup"><span data-stu-id="ee242-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

<span data-ttu-id="ee242-442">È possibile definire più set di entità per tipo (MEST).</span><span class="sxs-lookup"><span data-stu-id="ee242-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="ee242-443">Nell'esempio seguente viene definito un contenitore di entità con due set di entità per il tipo di entità **book** :</span><span class="sxs-lookup"><span data-stu-id="ee242-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="ee242-444">Elemento EntityType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="ee242-445">L'elemento **EntityType** rappresenta la struttura di un concetto di primo livello, ad esempio un cliente o un ordine, in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="ee242-446">Un tipo di entità è un modello per istanze di tipi di entità in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="ee242-447">Ogni modello contiene le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="ee242-448">Un nome univoco.</span><span class="sxs-lookup"><span data-stu-id="ee242-448">A unique name.</span></span> <span data-ttu-id="ee242-449">Obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="ee242-449">(Required.)</span></span>
-   <span data-ttu-id="ee242-450">Una chiave di entità che è definita da una o più proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="ee242-451">Obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="ee242-451">(Required.)</span></span>
-   <span data-ttu-id="ee242-452">Proprietà per contenere dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-452">Properties for containing data.</span></span> <span data-ttu-id="ee242-453">Facoltativo.</span><span class="sxs-lookup"><span data-stu-id="ee242-453">(Optional.)</span></span>
-   <span data-ttu-id="ee242-454">Proprietà di navigazione che consentono di navigare da un'entità finale di un'associazione all'altra.</span><span class="sxs-lookup"><span data-stu-id="ee242-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="ee242-455">Facoltativo.</span><span class="sxs-lookup"><span data-stu-id="ee242-455">(Optional.)</span></span>

<span data-ttu-id="ee242-456">In un'applicazione, un'istanza di un tipo di entità rappresenta un oggetto specifico, quale ad esempio un cliente o un ordine specifico.</span><span class="sxs-lookup"><span data-stu-id="ee242-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="ee242-457">Ogni istanza di un tipo di entità deve avere una chiave di entità univoca all'interno di un set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="ee242-458">Due istanze di tipi di entità sono considerate uguali solo se sono dello stesso tipo e se i valori delle rispettive chiavi di entità sono uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="ee242-459">Un elemento **EntityType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-460">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-461">Key (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="ee242-462">Property (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="ee242-463">NavigationProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="ee242-464">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-465">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-465">Applicable Attributes</span></span>

<span data-ttu-id="ee242-466">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="ee242-467">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="ee242-468">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-468">Is Required</span></span> | <span data-ttu-id="ee242-469">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-470">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="ee242-471">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-471">Yes</span></span>         | <span data-ttu-id="ee242-472">Nome del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="ee242-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="ee242-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="ee242-474">No</span><span class="sxs-lookup"><span data-stu-id="ee242-474">No</span></span>          | <span data-ttu-id="ee242-475">Nome di un altro tipo di entità, che rappresenta il tipo di base del tipo di entità in corso di definizione.</span><span class="sxs-lookup"><span data-stu-id="ee242-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="ee242-476">**Contenuto**</span><span class="sxs-lookup"><span data-stu-id="ee242-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="ee242-477">No</span><span class="sxs-lookup"><span data-stu-id="ee242-477">No</span></span>          | <span data-ttu-id="ee242-478">**True** o **false**, a seconda che il tipo di entità sia un tipo astratto.</span><span class="sxs-lookup"><span data-stu-id="ee242-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="ee242-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="ee242-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="ee242-480">No</span><span class="sxs-lookup"><span data-stu-id="ee242-480">No</span></span>          | <span data-ttu-id="ee242-481">**True** o **false** a seconda che il tipo di entità sia un tipo di entità aperto.</span><span class="sxs-lookup"><span data-stu-id="ee242-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="ee242-482">> l'attributo **OpenType** è applicabile solo ai tipi di entità definiti nei modelli concettuali utilizzati con ADO.NET Data Services.</span><span class="sxs-lookup"><span data-stu-id="ee242-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="ee242-483">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="ee242-484">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-485">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-486">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-486">Example</span></span>

<span data-ttu-id="ee242-487">Nell'esempio seguente viene illustrato un elemento **EntityType** con tre elementi **Property** e due elementi **NavigationProperty** :</span><span class="sxs-lookup"><span data-stu-id="ee242-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="ee242-488">Elemento EnumType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="ee242-489">L'elemento **enumType** rappresenta un tipo enumerato.</span><span class="sxs-lookup"><span data-stu-id="ee242-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="ee242-490">Un elemento **enumType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-491">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-492">Member (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="ee242-493">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-494">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-494">Applicable Attributes</span></span>

<span data-ttu-id="ee242-495">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **enumType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="ee242-496">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-496">Attribute Name</span></span>     | <span data-ttu-id="ee242-497">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-497">Is Required</span></span> | <span data-ttu-id="ee242-498">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-499">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-499">**Name**</span></span>           | <span data-ttu-id="ee242-500">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-500">Yes</span></span>         | <span data-ttu-id="ee242-501">Nome del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="ee242-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="ee242-502">**IsFlags**</span></span>        | <span data-ttu-id="ee242-503">No</span><span class="sxs-lookup"><span data-stu-id="ee242-503">No</span></span>          | <span data-ttu-id="ee242-504">**True** o **false**, a seconda che il tipo enum possa essere utilizzato come set di flag.</span><span class="sxs-lookup"><span data-stu-id="ee242-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="ee242-505">Il valore predefinito è **false.**</span><span class="sxs-lookup"><span data-stu-id="ee242-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="ee242-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="ee242-506">**UnderlyingType**</span></span> | <span data-ttu-id="ee242-507">No</span><span class="sxs-lookup"><span data-stu-id="ee242-507">No</span></span>          | <span data-ttu-id="ee242-508">**EDM. byte**, **EDM. Int16**, **EDM. Int32**, **EDM. Int64** o **EDM. SByte** che definisce l'intervallo di valori del tipo.</span><span class="sxs-lookup"><span data-stu-id="ee242-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="ee242-509">Il tipo sottostante predefinito degli elementi di enumerazione è **EDM. Int32.**.</span><span class="sxs-lookup"><span data-stu-id="ee242-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-510">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **enumType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="ee242-511">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-512">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-513">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-513">Example</span></span>

<span data-ttu-id="ee242-514">Nell'esempio seguente viene illustrato un elemento **enumType** con tre elementi **member** :</span><span class="sxs-lookup"><span data-stu-id="ee242-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="ee242-515">Elemento Function (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-515">Function Element (CSDL)</span></span>

<span data-ttu-id="ee242-516">L'elemento **Function** in Conceptual Schema Definition Language (CSDL) viene utilizzato per definire o dichiarare funzioni nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="ee242-517">Un funzione viene definita utilizzando un elemento DefiningExpression.</span><span class="sxs-lookup"><span data-stu-id="ee242-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="ee242-518">Un elemento **Function** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-519">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-520">Parameter (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="ee242-521">DefiningExpression (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="ee242-522">ReturnType (funzione) (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="ee242-523">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="ee242-524">Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** (Function) o l'attributo **returnType** (vedere di seguito), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="ee242-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="ee242-525">I tipi restituiti possibili sono il edmSimpleType, il tipo di entità, il tipo complesso, il tipo di riga o il tipo di riferimento o una raccolta di uno di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="ee242-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-526">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-526">Applicable Attributes</span></span>

<span data-ttu-id="ee242-527">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Function** .</span><span class="sxs-lookup"><span data-stu-id="ee242-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="ee242-528">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-528">Attribute Name</span></span> | <span data-ttu-id="ee242-529">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-529">Is Required</span></span> | <span data-ttu-id="ee242-530">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="ee242-531">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-531">**Name**</span></span>       | <span data-ttu-id="ee242-532">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-532">Yes</span></span>         | <span data-ttu-id="ee242-533">Nome della funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-533">The name of the function.</span></span>          |
| <span data-ttu-id="ee242-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="ee242-534">**ReturnType**</span></span> | <span data-ttu-id="ee242-535">No</span><span class="sxs-lookup"><span data-stu-id="ee242-535">No</span></span>          | <span data-ttu-id="ee242-536">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-537">All'elemento **Function** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="ee242-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="ee242-538">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-539">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-540">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-540">Example</span></span>

<span data-ttu-id="ee242-541">Nell'esempio seguente viene utilizzato un elemento **Function** per definire una funzione che restituisce il numero di anni dall'assunzione di un insegnante.</span><span class="sxs-lookup"><span data-stu-id="ee242-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="ee242-542">Elemento FunctionImport (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="ee242-543">L'elemento **FunctionImport** in Conceptual Schema Definition Language (CSDL) rappresenta una funzione definita nell'origine dati, ma disponibile per gli oggetti tramite il modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="ee242-544">Ad esempio, è possibile utilizzare un elemento Function nel modello di archiviazione per rappresentare una stored procedure in un database.</span><span class="sxs-lookup"><span data-stu-id="ee242-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="ee242-545">Un elemento **FunctionImport** nel modello concettuale rappresenta la funzione corrispondente in un'applicazione Entity Framework e viene mappato alla funzione del modello di archiviazione utilizzando l'elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="ee242-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="ee242-546">Quando la funzione viene chiamata nell'applicazione, la stored procedure corrispondente viene eseguita nel database.</span><span class="sxs-lookup"><span data-stu-id="ee242-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="ee242-547">L'elemento **FunctionImport** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-548">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="ee242-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ee242-549">Parameter (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="ee242-550">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="ee242-551">ReturnType (FunctionImport) (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="ee242-552">È necessario definire un elemento **Parameter** per ogni parametro accettato dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="ee242-553">Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** (FunctionImport) o l'attributo **returnType** (vedere di seguito), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="ee242-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="ee242-554">Il valore del tipo restituito deve essere una raccolta di EdmSimpleType, EntityType o ComplexType.</span><span class="sxs-lookup"><span data-stu-id="ee242-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-555">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-555">Applicable Attributes</span></span>

<span data-ttu-id="ee242-556">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="ee242-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="ee242-557">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-557">Attribute Name</span></span>   | <span data-ttu-id="ee242-558">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-558">Is Required</span></span> | <span data-ttu-id="ee242-559">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-560">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-560">**Name**</span></span>         | <span data-ttu-id="ee242-561">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-561">Yes</span></span>         | <span data-ttu-id="ee242-562">Nome della funzione importata.</span><span class="sxs-lookup"><span data-stu-id="ee242-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="ee242-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="ee242-563">**ReturnType**</span></span>   | <span data-ttu-id="ee242-564">No</span><span class="sxs-lookup"><span data-stu-id="ee242-564">No</span></span>          | <span data-ttu-id="ee242-565">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-565">The type that the function returns.</span></span> <span data-ttu-id="ee242-566">Non utilizzare questo attributo se la funzione non restituisce un valore.</span><span class="sxs-lookup"><span data-stu-id="ee242-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="ee242-567">In caso contrario, il valore deve essere una raccolta di ComplexType, EntityType o EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="ee242-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="ee242-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="ee242-568">**EntitySet**</span></span>    | <span data-ttu-id="ee242-569">No</span><span class="sxs-lookup"><span data-stu-id="ee242-569">No</span></span>          | <span data-ttu-id="ee242-570">Se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve essere il set di entità a cui appartiene la raccolta.</span><span class="sxs-lookup"><span data-stu-id="ee242-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="ee242-571">In caso contrario, l'attributo **EntitySet** non deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="ee242-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="ee242-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="ee242-572">**IsComposable**</span></span> | <span data-ttu-id="ee242-573">No</span><span class="sxs-lookup"><span data-stu-id="ee242-573">No</span></span>          | <span data-ttu-id="ee242-574">Se il valore è impostato su true, la funzione è componibile (funzione con valori di tabella) e può essere utilizzata in una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="ee242-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="ee242-575">Il valore predefinito è **false**.</span><span class="sxs-lookup"><span data-stu-id="ee242-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="ee242-576">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="ee242-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="ee242-577">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-578">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-579">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-579">Example</span></span>

<span data-ttu-id="ee242-580">Nell'esempio seguente viene illustrato un elemento **FunctionImport** che accetta un parametro e restituisce una raccolta di tipi di entità:</span><span class="sxs-lookup"><span data-stu-id="ee242-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="ee242-581">Elemento Key (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-581">Key Element (CSDL)</span></span>

<span data-ttu-id="ee242-582">L'elemento **Key** è un elemento figlio dell'elemento EntityType e definisce una *chiave di entità* , ovvero una proprietà o un set di proprietà di un tipo di entità che determinano l'identità.</span><span class="sxs-lookup"><span data-stu-id="ee242-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="ee242-583">Le proprietà che costituiscono una chiave di entità vengono scelte in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="ee242-584">I valori delle proprietà della chiave di entità devono identificare in modo univoco in fase di esecuzione un'istanza del tipo di entità all'interno di un set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="ee242-585">Le proprietà che costituiscono una chiave di entità devono essere scelte per garantire univocità delle istanze in un set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="ee242-586">L'elemento **Key** definisce una chiave di entità facendo riferimento a una o più proprietà di un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="ee242-587">L'elemento **chiave** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="ee242-588">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="ee242-589">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-590">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-590">Applicable Attributes</span></span>

<span data-ttu-id="ee242-591">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **chiave** .</span><span class="sxs-lookup"><span data-stu-id="ee242-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="ee242-592">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-593">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-594">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-594">Example</span></span>

<span data-ttu-id="ee242-595">Nell'esempio seguente viene definito un tipo di entità denominato **book**.</span><span class="sxs-lookup"><span data-stu-id="ee242-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="ee242-596">La chiave di entità viene definita facendo riferimento alla proprietà **ISBN** del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="ee242-597">La proprietà **ISBN** è una scelta ottimale per la chiave di entità perché un numero di libro standard internazionale (ISBN) identifica in modo univoco un libro.</span><span class="sxs-lookup"><span data-stu-id="ee242-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="ee242-598">Nell'esempio seguente viene illustrato un tipo di entità (**autore**) che dispone di una chiave di entità costituita da due proprietà, **Name** e **Address**.</span><span class="sxs-lookup"><span data-stu-id="ee242-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

<span data-ttu-id="ee242-599">L'uso del **nome** e dell' **Indirizzo** per la chiave di entità è una scelta ragionevole, perché due autori con lo stesso nome non possono vivere allo stesso indirizzo.</span><span class="sxs-lookup"><span data-stu-id="ee242-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="ee242-600">Questa scelta per una chiave di entità non garantisce tuttavia in modo assoluto chiavi di entità univoche in un set di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="ee242-601">In questo caso, è consigliabile aggiungere una proprietà, ad esempio **autorizzazione**, che può essere usata per identificare in modo univoco un autore.</span><span class="sxs-lookup"><span data-stu-id="ee242-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="ee242-602">Elemento member (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-602">Member Element (CSDL)</span></span>

<span data-ttu-id="ee242-603">L'elemento **member** è un elemento figlio dell'elemento enumType e definisce un membro del tipo enumerato.</span><span class="sxs-lookup"><span data-stu-id="ee242-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-604">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-604">Applicable Attributes</span></span>

<span data-ttu-id="ee242-605">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="ee242-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="ee242-606">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-606">Attribute Name</span></span> | <span data-ttu-id="ee242-607">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-607">Is Required</span></span> | <span data-ttu-id="ee242-608">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-609">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-609">**Name**</span></span>       | <span data-ttu-id="ee242-610">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-610">Yes</span></span>         | <span data-ttu-id="ee242-611">Nome del membro.</span><span class="sxs-lookup"><span data-stu-id="ee242-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="ee242-612">**Valore**</span><span class="sxs-lookup"><span data-stu-id="ee242-612">**Value**</span></span>      | <span data-ttu-id="ee242-613">No</span><span class="sxs-lookup"><span data-stu-id="ee242-613">No</span></span>          | <span data-ttu-id="ee242-614">Valore del membro.</span><span class="sxs-lookup"><span data-stu-id="ee242-614">The value of the member.</span></span> <span data-ttu-id="ee242-615">Per impostazione predefinita, il primo membro ha il valore 0 e il valore di ogni enumeratore successivo viene incrementato di 1.</span><span class="sxs-lookup"><span data-stu-id="ee242-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="ee242-616">Potrebbero esistere più membri con gli stessi valori.</span><span class="sxs-lookup"><span data-stu-id="ee242-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-617">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="ee242-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="ee242-618">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-619">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-620">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-620">Example</span></span>

<span data-ttu-id="ee242-621">Nell'esempio seguente viene illustrato un elemento **enumType** con tre elementi **member** :</span><span class="sxs-lookup"><span data-stu-id="ee242-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="ee242-622">Elemento NavigationProperty (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="ee242-623">Un elemento **NavigationProperty** definisce una proprietà di navigazione che fornisce un riferimento all'altra entità finale di un'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="ee242-624">A differenza delle proprietà definite con l'elemento Property, le proprietà di navigazione non definiscono la forma e le caratteristiche dei dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="ee242-625">Forniscono un modo per navigare in un'associazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="ee242-626">Si noti che le proprietà di navigazione sono facoltative in entrambi tipi di entità nelle entità finali di un'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="ee242-627">Se si definisce una proprietà di navigazione su un tipo di entità nell'entità finale di un'associazione, non è necessario definire una proprietà di navigazione sul tipo di entità nell'altra entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="ee242-628">Il tipo di dati restituito da una proprietà di navigazione è determinato dalla molteplicità della relativa entità finale di associazione remota.</span><span class="sxs-lookup"><span data-stu-id="ee242-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="ee242-629">Si supponga, ad esempio, che una proprietà di navigazione, **OrdersNavProp**, esista in un tipo di entità **Customer** e si sposta in un'associazione uno-a-molti tra **Customer** e **Order**.</span><span class="sxs-lookup"><span data-stu-id="ee242-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="ee242-630">Poiché l'entità finale dell'associazione remota per la proprietà di navigazione dispone di molteplicità many ( \* ), il relativo tipo di dati è una raccolta (di **Order**).</span><span class="sxs-lookup"><span data-stu-id="ee242-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="ee242-631">Analogamente, se esiste una proprietà di navigazione, **CustomerNavProp nel**, nel tipo di entità **Order** , il relativo tipo di dati sarà **Customer** poiché la molteplicità di end remoto è uno (1).</span><span class="sxs-lookup"><span data-stu-id="ee242-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="ee242-632">Un elemento **NavigationProperty** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-633">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-634">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-635">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-635">Applicable Attributes</span></span>

<span data-ttu-id="ee242-636">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="ee242-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="ee242-637">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-637">Attribute Name</span></span>   | <span data-ttu-id="ee242-638">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-638">Is Required</span></span> | <span data-ttu-id="ee242-639">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-640">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-640">**Name**</span></span>         | <span data-ttu-id="ee242-641">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-641">Yes</span></span>         | <span data-ttu-id="ee242-642">Nome della proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="ee242-643">**Relazione**</span><span class="sxs-lookup"><span data-stu-id="ee242-643">**Relationship**</span></span> | <span data-ttu-id="ee242-644">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-644">Yes</span></span>         | <span data-ttu-id="ee242-645">Nome di un'associazione che è all'interno dell'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="ee242-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="ee242-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="ee242-646">**ToRole**</span></span>       | <span data-ttu-id="ee242-647">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-647">Yes</span></span>         | <span data-ttu-id="ee242-648">Entità finale dell'associazione in corrispondenza della quale termina la navigazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="ee242-649">Il valore dell'attributo **ToRole** deve corrispondere al valore di uno degli attributi **Role** definiti in una delle entità finali dell'associazione (definite nell'elemento AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="ee242-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="ee242-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="ee242-650">**FromRole**</span></span>     | <span data-ttu-id="ee242-651">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-651">Yes</span></span>         | <span data-ttu-id="ee242-652">Entità finale dell'associazione dalla quale ha inizio la navigazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="ee242-653">Il valore dell'attributo **FromRole** deve corrispondere al valore di uno degli attributi **Role** definiti in una delle entità finali dell'associazione (definite nell'elemento AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="ee242-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-654">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="ee242-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="ee242-655">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-656">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-657">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-657">Example</span></span>

<span data-ttu-id="ee242-658">Nell'esempio seguente viene definito un tipo di entità (**book**) con due proprietà di navigazione (**PublishedBy** e **WrittenBy**):</span><span class="sxs-lookup"><span data-stu-id="ee242-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="ee242-659">Elemento OnDelete (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="ee242-660">L'elemento **OnDelete** in Conceptual Schema Definition Language (CSDL) definisce il comportamento connesso a un'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="ee242-661">Se l'attributo **Action** è impostato su **Cascade** in un'entità finale di un'associazione, i tipi di entità correlati sull'altra entità finale dell'associazione vengono eliminati quando viene eliminato il tipo di entità alla prima estremità.</span><span class="sxs-lookup"><span data-stu-id="ee242-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="ee242-662">Se l'associazione tra due tipi di entità è una relazione tra chiave primaria e chiave primaria, un oggetto dipendente caricato viene eliminato quando l'oggetto Principal sull'altra entità finale dell'associazione viene eliminato indipendentemente dalla specifica **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="ee242-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="ee242-663">L'elemento **OnDelete** influisca solo sul comportamento di runtime di un'applicazione. non influisce sul comportamento nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="ee242-664">Il comportamento definito nell'origine dati deve essere uguale al comportamento definito nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="ee242-665">Un elemento **OnDelete** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-666">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-667">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-668">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-668">Applicable Attributes</span></span>

<span data-ttu-id="ee242-669">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="ee242-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="ee242-670">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-670">Attribute Name</span></span> | <span data-ttu-id="ee242-671">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-671">Is Required</span></span> | <span data-ttu-id="ee242-672">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-673">**Azione**</span><span class="sxs-lookup"><span data-stu-id="ee242-673">**Action**</span></span>     | <span data-ttu-id="ee242-674">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-674">Yes</span></span>         | <span data-ttu-id="ee242-675">**Cascade** o **None**.</span><span class="sxs-lookup"><span data-stu-id="ee242-675">**Cascade** or **None**.</span></span> <span data-ttu-id="ee242-676">Se **Cascade**, i tipi di entità dipendenti verranno eliminati quando il tipo di entità principale viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="ee242-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="ee242-677">Se **None**, i tipi di entità dipendenti non verranno eliminati quando il tipo di entità principale viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="ee242-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-678">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="ee242-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="ee242-679">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-680">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-681">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-681">Example</span></span>

<span data-ttu-id="ee242-682">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="ee242-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="ee242-683">L'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati quando il **cliente** viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="ee242-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="ee242-684">Elemento Parameter (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="ee242-685">L'elemento **Parameter** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento FunctionImport o dell'elemento Function.</span><span class="sxs-lookup"><span data-stu-id="ee242-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="ee242-686">Applicazione dell'elemento FunctionImport</span><span class="sxs-lookup"><span data-stu-id="ee242-686">FunctionImport Element Application</span></span>

<span data-ttu-id="ee242-687">Un elemento **Parameter** (come figlio dell'elemento **FunctionImport** ) viene usato per definire i parametri di input e output per le importazioni di funzioni dichiarate in CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="ee242-688">L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-689">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="ee242-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ee242-690">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="ee242-691">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-691">Applicable Attributes</span></span>

<span data-ttu-id="ee242-692">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="ee242-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="ee242-693">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-693">Attribute Name</span></span> | <span data-ttu-id="ee242-694">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-694">Is Required</span></span> | <span data-ttu-id="ee242-695">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-696">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-696">**Name**</span></span>       | <span data-ttu-id="ee242-697">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-697">Yes</span></span>         | <span data-ttu-id="ee242-698">Nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="ee242-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="ee242-699">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-699">**Type**</span></span>       | <span data-ttu-id="ee242-700">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-700">Yes</span></span>         | <span data-ttu-id="ee242-701">Tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="ee242-701">The parameter type.</span></span> <span data-ttu-id="ee242-702">Il valore deve essere di tipo **EDMSimpleType** o di un tipo complesso compreso nell'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="ee242-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="ee242-703">**Modalità**</span><span class="sxs-lookup"><span data-stu-id="ee242-703">**Mode**</span></span>       | <span data-ttu-id="ee242-704">No</span><span class="sxs-lookup"><span data-stu-id="ee242-704">No</span></span>          | <span data-ttu-id="ee242-705">**In**, **out**o **InOut** a seconda che il parametro sia un parametro di input, di output o di input/output.</span><span class="sxs-lookup"><span data-stu-id="ee242-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="ee242-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-706">**MaxLength**</span></span>  | <span data-ttu-id="ee242-707">No</span><span class="sxs-lookup"><span data-stu-id="ee242-707">No</span></span>          | <span data-ttu-id="ee242-708">Lunghezza massima consentita per il parametro.</span><span class="sxs-lookup"><span data-stu-id="ee242-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="ee242-709">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-709">**Precision**</span></span>  | <span data-ttu-id="ee242-710">No</span><span class="sxs-lookup"><span data-stu-id="ee242-710">No</span></span>          | <span data-ttu-id="ee242-711">Precisione del parametro</span><span class="sxs-lookup"><span data-stu-id="ee242-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="ee242-712">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-712">**Scale**</span></span>      | <span data-ttu-id="ee242-713">No</span><span class="sxs-lookup"><span data-stu-id="ee242-713">No</span></span>          | <span data-ttu-id="ee242-714">Scalabilità del parametro</span><span class="sxs-lookup"><span data-stu-id="ee242-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="ee242-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-715">**SRID**</span></span>       | <span data-ttu-id="ee242-716">No</span><span class="sxs-lookup"><span data-stu-id="ee242-716">No</span></span>          | <span data-ttu-id="ee242-717">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ee242-718">Valido solo per i parametri dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="ee242-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="ee242-719">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee242-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-720">All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="ee242-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="ee242-721">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-722">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ee242-723">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-723">Example</span></span>

<span data-ttu-id="ee242-724">Nell'esempio seguente viene illustrato un elemento **FunctionImport** con un elemento figlio **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="ee242-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="ee242-725">La funzione accetta un parametro di input e restituisce una raccolta di tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="ee242-726">Applicazione dell'elemento Function</span><span class="sxs-lookup"><span data-stu-id="ee242-726">Function Element Application</span></span>

<span data-ttu-id="ee242-727">Un elemento **Parameter** (come figlio dell'elemento **Function** ) definisce i parametri per le funzioni definite o dichiarate in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="ee242-728">L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-729">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="ee242-730">CollectionType (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="ee242-731">ReferenceType (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="ee242-732">RowType (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-733">Solo uno degli elementi **CollectionType**, **ReferenceType**o **RowType** può essere un elemento figlio di un elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="ee242-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="ee242-734">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-735">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="ee242-736">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="ee242-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ee242-737">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-737">Applicable Attributes</span></span>

<span data-ttu-id="ee242-738">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="ee242-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="ee242-739">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-739">Attribute Name</span></span>   | <span data-ttu-id="ee242-740">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-740">Is Required</span></span> | <span data-ttu-id="ee242-741">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-742">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-742">**Name**</span></span>         | <span data-ttu-id="ee242-743">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-743">Yes</span></span>         | <span data-ttu-id="ee242-744">Nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="ee242-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="ee242-745">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-745">**Type**</span></span>         | <span data-ttu-id="ee242-746">No</span><span class="sxs-lookup"><span data-stu-id="ee242-746">No</span></span>          | <span data-ttu-id="ee242-747">Tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="ee242-747">The parameter type.</span></span> <span data-ttu-id="ee242-748">Un parametro può essere uno qualsiasi dei seguenti tipi (o raccolte di questi tipi):</span><span class="sxs-lookup"><span data-stu-id="ee242-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="ee242-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="ee242-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="ee242-750">tipo di entità</span><span class="sxs-lookup"><span data-stu-id="ee242-750">entity type</span></span> <br/> <span data-ttu-id="ee242-751">tipo complesso</span><span class="sxs-lookup"><span data-stu-id="ee242-751">complex type</span></span> <br/> <span data-ttu-id="ee242-752">tipo di riga</span><span class="sxs-lookup"><span data-stu-id="ee242-752">row type</span></span> <br/> <span data-ttu-id="ee242-753">tipo di riferimento</span><span class="sxs-lookup"><span data-stu-id="ee242-753">reference type</span></span>                             |
| <span data-ttu-id="ee242-754">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="ee242-754">**Nullable**</span></span>     | <span data-ttu-id="ee242-755">No</span><span class="sxs-lookup"><span data-stu-id="ee242-755">No</span></span>          | <span data-ttu-id="ee242-756">**True** (valore predefinito) o **false** a seconda che la proprietà possa avere un valore **null** .</span><span class="sxs-lookup"><span data-stu-id="ee242-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="ee242-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="ee242-757">**DefaultValue**</span></span> | <span data-ttu-id="ee242-758">No</span><span class="sxs-lookup"><span data-stu-id="ee242-758">No</span></span>          | <span data-ttu-id="ee242-759">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ee242-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-760">**MaxLength**</span></span>    | <span data-ttu-id="ee242-761">No</span><span class="sxs-lookup"><span data-stu-id="ee242-761">No</span></span>          | <span data-ttu-id="ee242-762">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ee242-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-763">**FixedLength**</span></span>  | <span data-ttu-id="ee242-764">No</span><span class="sxs-lookup"><span data-stu-id="ee242-764">No</span></span>          | <span data-ttu-id="ee242-765">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="ee242-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ee242-766">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-766">**Precision**</span></span>    | <span data-ttu-id="ee242-767">No</span><span class="sxs-lookup"><span data-stu-id="ee242-767">No</span></span>          | <span data-ttu-id="ee242-768">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ee242-769">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-769">**Scale**</span></span>        | <span data-ttu-id="ee242-770">No</span><span class="sxs-lookup"><span data-stu-id="ee242-770">No</span></span>          | <span data-ttu-id="ee242-771">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ee242-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-772">**SRID**</span></span>         | <span data-ttu-id="ee242-773">No</span><span class="sxs-lookup"><span data-stu-id="ee242-773">No</span></span>          | <span data-ttu-id="ee242-774">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ee242-775">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="ee242-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ee242-776">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee242-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ee242-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ee242-777">**Unicode**</span></span>      | <span data-ttu-id="ee242-778">No</span><span class="sxs-lookup"><span data-stu-id="ee242-778">No</span></span>          | <span data-ttu-id="ee242-779">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="ee242-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ee242-780">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="ee242-780">**Collation**</span></span>    | <span data-ttu-id="ee242-781">No</span><span class="sxs-lookup"><span data-stu-id="ee242-781">No</span></span>          | <span data-ttu-id="ee242-782">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ee242-783">All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="ee242-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="ee242-784">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-785">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ee242-786">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-786">Example</span></span>

<span data-ttu-id="ee242-787">Nell'esempio seguente viene illustrato un elemento **Function** che utilizza un elemento figlio **Parameter** per definire un parametro di funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="ee242-788">Elemento Principal (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="ee242-789">L'elemento **Principal** in Conceptual Schema Definition Language (CSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale principale di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="ee242-790">Un elemento **ReferentialConstraint** definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="ee242-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="ee242-791">Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="ee242-792">Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="ee242-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="ee242-793">Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="ee242-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="ee242-794">Gli elementi **PropertyRef** vengono utilizzati per specificare a quali chiavi viene fatto riferimento dall'entità finale dipendente.</span><span class="sxs-lookup"><span data-stu-id="ee242-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="ee242-795">L'elemento **Principal** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-796">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="ee242-797">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-798">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-798">Applicable Attributes</span></span>

<span data-ttu-id="ee242-799">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Principal** .</span><span class="sxs-lookup"><span data-stu-id="ee242-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="ee242-800">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-800">Attribute Name</span></span> | <span data-ttu-id="ee242-801">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-801">Is Required</span></span> | <span data-ttu-id="ee242-802">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="ee242-803">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="ee242-803">**Role**</span></span>       | <span data-ttu-id="ee242-804">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-804">Yes</span></span>         | <span data-ttu-id="ee242-805">Nome del tipo di entità in un'entità finale principale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-806">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **principale** .</span><span class="sxs-lookup"><span data-stu-id="ee242-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="ee242-807">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-808">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-809">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-809">Example</span></span>

<span data-ttu-id="ee242-810">Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** che fa parte della definizione dell'associazione **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="ee242-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="ee242-811">La proprietà **ID** del tipo di entità **Publisher** costituisce l'entità finale principale del vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="ee242-812">Elemento Property (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-812">Property Element (CSDL)</span></span>

<span data-ttu-id="ee242-813">L'elemento **Property** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento EntityType, dell'elemento complexType o dell'elemento RowType.</span><span class="sxs-lookup"><span data-stu-id="ee242-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="ee242-814">Applicazione degli elementi EntityType e ComplexType</span><span class="sxs-lookup"><span data-stu-id="ee242-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="ee242-815">Gli elementi **Property** (come elementi figlio degli elementi **EntityType** o **complexType** ) definiscono la forma e le caratteristiche dei dati che un'istanza del tipo di entità o di tipo complesso conterrà.</span><span class="sxs-lookup"><span data-stu-id="ee242-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="ee242-816">Le proprietà in un modello concettuale sono analoghe alle proprietà definite su una classe.</span><span class="sxs-lookup"><span data-stu-id="ee242-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="ee242-817">Nello stesso modo in cui le proprietà su una classe definiscono la forma della classe e forniscono informazioni su oggetti, le proprietà in un modello concettuale definiscono la forma di un tipo di entità e forniscono informazioni su istanze del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="ee242-818">L'elemento **Property** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-819">Elemento Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="ee242-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ee242-820">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="ee242-821">A un elemento **Property** è possibile applicare i facet seguenti: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span><span class="sxs-lookup"><span data-stu-id="ee242-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="ee242-822">I facet sono attributi XML che forniscono informazioni sul modo in cui i valori di proprietà vengono archiviati nell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-823">I facet possono essere applicati solo a proprietà di tipo **EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="ee242-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ee242-824">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-824">Applicable Attributes</span></span>

<span data-ttu-id="ee242-825">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="ee242-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="ee242-826">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-826">Attribute Name</span></span>                                                         | <span data-ttu-id="ee242-827">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-827">Is Required</span></span> | <span data-ttu-id="ee242-828">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-829">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-829">**Name**</span></span>                                                               | <span data-ttu-id="ee242-830">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-830">Yes</span></span>         | <span data-ttu-id="ee242-831">Nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="ee242-832">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-832">**Type**</span></span>                                                               | <span data-ttu-id="ee242-833">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-833">Yes</span></span>         | <span data-ttu-id="ee242-834">Tipo del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-834">The type of the property value.</span></span> <span data-ttu-id="ee242-835">Il tipo del valore della proprietà deve corrispondere a **EDMSimpleType** o a un tipo complesso (indicato da un nome completo) nell'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="ee242-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="ee242-836">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="ee242-836">**Nullable**</span></span>                                                           | <span data-ttu-id="ee242-837">No</span><span class="sxs-lookup"><span data-stu-id="ee242-837">No</span></span>          | <span data-ttu-id="ee242-838">**True** (valore predefinito) o <strong>False</strong>, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="ee242-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="ee242-839">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="ee242-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="ee242-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="ee242-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="ee242-841">No</span><span class="sxs-lookup"><span data-stu-id="ee242-841">No</span></span>          | <span data-ttu-id="ee242-842">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ee242-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="ee242-844">No</span><span class="sxs-lookup"><span data-stu-id="ee242-844">No</span></span>          | <span data-ttu-id="ee242-845">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ee242-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="ee242-847">No</span><span class="sxs-lookup"><span data-stu-id="ee242-847">No</span></span>          | <span data-ttu-id="ee242-848">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="ee242-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ee242-849">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-849">**Precision**</span></span>                                                          | <span data-ttu-id="ee242-850">No</span><span class="sxs-lookup"><span data-stu-id="ee242-850">No</span></span>          | <span data-ttu-id="ee242-851">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ee242-852">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-852">**Scale**</span></span>                                                              | <span data-ttu-id="ee242-853">No</span><span class="sxs-lookup"><span data-stu-id="ee242-853">No</span></span>          | <span data-ttu-id="ee242-854">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ee242-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-855">**SRID**</span></span>                                                               | <span data-ttu-id="ee242-856">No</span><span class="sxs-lookup"><span data-stu-id="ee242-856">No</span></span>          | <span data-ttu-id="ee242-857">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ee242-858">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="ee242-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ee242-859">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee242-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ee242-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ee242-860">**Unicode**</span></span>                                                            | <span data-ttu-id="ee242-861">No</span><span class="sxs-lookup"><span data-stu-id="ee242-861">No</span></span>          | <span data-ttu-id="ee242-862">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="ee242-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ee242-863">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="ee242-863">**Collation**</span></span>                                                          | <span data-ttu-id="ee242-864">No</span><span class="sxs-lookup"><span data-stu-id="ee242-864">No</span></span>          | <span data-ttu-id="ee242-865">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="ee242-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="ee242-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="ee242-867">No</span><span class="sxs-lookup"><span data-stu-id="ee242-867">No</span></span>          | <span data-ttu-id="ee242-868">**None** (valore predefinito) o **Fixed**.</span><span class="sxs-lookup"><span data-stu-id="ee242-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="ee242-869">Se il valore è impostato su **Fixed**, il valore della proprietà verrà utilizzato nei controlli della concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="ee242-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="ee242-870">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="ee242-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="ee242-871">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-872">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ee242-873">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-873">Example</span></span>

<span data-ttu-id="ee242-874">Nell'esempio seguente viene illustrato un elemento **EntityType** con tre elementi **Property** :</span><span class="sxs-lookup"><span data-stu-id="ee242-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="ee242-875">Nell'esempio seguente viene illustrato un elemento **complexType** con cinque elementi **Property** :</span><span class="sxs-lookup"><span data-stu-id="ee242-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="ee242-876">Applicazione dell'elemento RowType</span><span class="sxs-lookup"><span data-stu-id="ee242-876">RowType Element Application</span></span>

<span data-ttu-id="ee242-877">Gli elementi **Property** (come gli elementi figlio di un elemento **RowType** ) definiscono la forma e le caratteristiche dei dati che possono essere passati o restituiti da una funzione definita dal modello.</span><span class="sxs-lookup"><span data-stu-id="ee242-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="ee242-878">L'elemento **Property** può avere esattamente uno degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="ee242-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ee242-879">CollectionType</span></span>
-   <span data-ttu-id="ee242-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="ee242-880">ReferenceType</span></span>
-   <span data-ttu-id="ee242-881">RowType</span><span class="sxs-lookup"><span data-stu-id="ee242-881">RowType</span></span>

<span data-ttu-id="ee242-882">L'elemento **Property** può includere qualsiasi numero di elementi Annotation figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-883">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="ee242-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ee242-884">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-884">Applicable Attributes</span></span>

<span data-ttu-id="ee242-885">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="ee242-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="ee242-886">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-886">Attribute Name</span></span>                                                     | <span data-ttu-id="ee242-887">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-887">Is Required</span></span> | <span data-ttu-id="ee242-888">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-889">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-889">**Name**</span></span>                                                           | <span data-ttu-id="ee242-890">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-890">Yes</span></span>         | <span data-ttu-id="ee242-891">Nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="ee242-892">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-892">**Type**</span></span>                                                           | <span data-ttu-id="ee242-893">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-893">Yes</span></span>         | <span data-ttu-id="ee242-894">Tipo del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="ee242-895">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="ee242-895">**Nullable**</span></span>                                                       | <span data-ttu-id="ee242-896">No</span><span class="sxs-lookup"><span data-stu-id="ee242-896">No</span></span>          | <span data-ttu-id="ee242-897">**True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="ee242-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="ee242-898">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="ee242-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="ee242-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="ee242-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="ee242-900">No</span><span class="sxs-lookup"><span data-stu-id="ee242-900">No</span></span>          | <span data-ttu-id="ee242-901">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ee242-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="ee242-903">No</span><span class="sxs-lookup"><span data-stu-id="ee242-903">No</span></span>          | <span data-ttu-id="ee242-904">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ee242-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="ee242-906">No</span><span class="sxs-lookup"><span data-stu-id="ee242-906">No</span></span>          | <span data-ttu-id="ee242-907">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="ee242-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ee242-908">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-908">**Precision**</span></span>                                                      | <span data-ttu-id="ee242-909">No</span><span class="sxs-lookup"><span data-stu-id="ee242-909">No</span></span>          | <span data-ttu-id="ee242-910">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ee242-911">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-911">**Scale**</span></span>                                                          | <span data-ttu-id="ee242-912">No</span><span class="sxs-lookup"><span data-stu-id="ee242-912">No</span></span>          | <span data-ttu-id="ee242-913">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ee242-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-914">**SRID**</span></span>                                                           | <span data-ttu-id="ee242-915">No</span><span class="sxs-lookup"><span data-stu-id="ee242-915">No</span></span>          | <span data-ttu-id="ee242-916">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ee242-917">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="ee242-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ee242-918">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee242-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ee242-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ee242-919">**Unicode**</span></span>                                                        | <span data-ttu-id="ee242-920">No</span><span class="sxs-lookup"><span data-stu-id="ee242-920">No</span></span>          | <span data-ttu-id="ee242-921">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="ee242-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ee242-922">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="ee242-922">**Collation**</span></span>                                                      | <span data-ttu-id="ee242-923">No</span><span class="sxs-lookup"><span data-stu-id="ee242-923">No</span></span>          | <span data-ttu-id="ee242-924">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ee242-925">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="ee242-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="ee242-926">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-927">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ee242-928">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-928">Example</span></span>

<span data-ttu-id="ee242-929">Nell'esempio seguente vengono illustrati gli elementi di **Proprietà** utilizzati per definire la forma del tipo restituito di una funzione definita dal modello.</span><span class="sxs-lookup"><span data-stu-id="ee242-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="ee242-930">Elemento PropertyRef (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="ee242-931">L'elemento **PropertyRef** in Conceptual Schema Definition Language (CSDL) fa riferimento a una proprietà di un tipo di entità per indicare che la proprietà eseguirà uno dei ruoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="ee242-932">Parte della chiave di entità (una proprietà o un set di proprietà di un tipo di entità che determinano l'identità).</span><span class="sxs-lookup"><span data-stu-id="ee242-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="ee242-933">Per definire una chiave di entità, è possibile usare uno o più elementi **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="ee242-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="ee242-934">L'entità finale dipendente o principale di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="ee242-935">L'elemento **PropertyRef** può contenere solo elementi Annotation (zero o più) come elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-936">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="ee242-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-937">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-937">Applicable Attributes</span></span>

<span data-ttu-id="ee242-938">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="ee242-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="ee242-939">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-939">Attribute Name</span></span> | <span data-ttu-id="ee242-940">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-940">Is Required</span></span> | <span data-ttu-id="ee242-941">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="ee242-942">**Nome**</span><span class="sxs-lookup"><span data-stu-id="ee242-942">**Name**</span></span>       | <span data-ttu-id="ee242-943">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-943">Yes</span></span>         | <span data-ttu-id="ee242-944">Nome della proprietà alla quale viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="ee242-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-945">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="ee242-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="ee242-946">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-947">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-948">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-948">Example</span></span>

<span data-ttu-id="ee242-949">Nell'esempio seguente viene definito un tipo di entità (**book**).</span><span class="sxs-lookup"><span data-stu-id="ee242-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="ee242-950">La chiave di entità viene definita facendo riferimento alla proprietà **ISBN** del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="ee242-951">Nell'esempio seguente vengono usati due elementi **PropertyRef** per indicare che due proprietà (**ID** e **publisherID**) sono le estremità principale e dipendente di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="ee242-952">Elemento ReferenceType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="ee242-953">L'elemento **ReferenceType** in Conceptual Schema Definition Language (CSDL) specifica un riferimento a un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="ee242-954">L'elemento **ReferenceType** può essere figlio dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="ee242-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="ee242-955">ReturnType (funzione)</span><span class="sxs-lookup"><span data-stu-id="ee242-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="ee242-956">Parametro</span><span class="sxs-lookup"><span data-stu-id="ee242-956">Parameter</span></span>
-   <span data-ttu-id="ee242-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ee242-957">CollectionType</span></span>

<span data-ttu-id="ee242-958">L'elemento **ReferenceType** viene usato quando si definisce un parametro o un tipo restituito per una funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="ee242-959">Un elemento **ReferenceType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-960">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-961">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-962">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-962">Applicable Attributes</span></span>

<span data-ttu-id="ee242-963">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **ReferenceType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="ee242-964">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-964">Attribute Name</span></span> | <span data-ttu-id="ee242-965">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-965">Is Required</span></span> | <span data-ttu-id="ee242-966">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="ee242-967">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-967">**Type**</span></span>       | <span data-ttu-id="ee242-968">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-968">Yes</span></span>         | <span data-ttu-id="ee242-969">Nome del tipo di entità a cui viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="ee242-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-970">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **ReferenceType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="ee242-971">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-972">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-973">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-973">Example</span></span>

<span data-ttu-id="ee242-974">Nell'esempio seguente viene illustrato l'elemento **ReferenceType** usato come figlio di un elemento **Parameter** in una funzione definita dal modello che accetta un riferimento a un tipo di entità **Person** :</span><span class="sxs-lookup"><span data-stu-id="ee242-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="ee242-975">Nell'esempio seguente viene illustrato l'elemento **ReferenceType** utilizzato come elemento figlio di un elemento **returnType** (Function) in una funzione definita dal modello che restituisce un riferimento a un tipo di entità **Person** :</span><span class="sxs-lookup"><span data-stu-id="ee242-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="ee242-976">Elemento ReferentialConstraint (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="ee242-977">Un elemento **ReferentialConstraint** in Conceptual Schema Definition Language (CSDL) definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="ee242-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="ee242-978">Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="ee242-979">Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="ee242-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="ee242-980">Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="ee242-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="ee242-981">Se una chiave esterna esposta in un tipo di entità fa riferimento a una proprietà in un altro tipo di entità, l'elemento **ReferentialConstraint** definisce un'associazione tra i due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="ee242-982">Poiché l'elemento **ReferentialConstraint** fornisce informazioni sul modo in cui due tipi di entità sono correlati, non è necessario alcun elemento AssociationSetMapping corrispondente nella Mapping Specification Language (MSL).</span><span class="sxs-lookup"><span data-stu-id="ee242-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="ee242-983">Un'associazione tra due tipi di entità che non dispongono di chiavi esterne esposte deve avere un elemento **AssociationSetMapping** corrispondente per eseguire il mapping delle informazioni di associazione all'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="ee242-984">Se una chiave esterna non è esposta in un tipo di entità, l'elemento **ReferentialConstraint** può definire solo un vincolo PRIMARY KEY-Primary Key tra il tipo di entità e un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="ee242-985">Un elemento **ReferentialConstraint** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-986">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-987">Principal (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="ee242-988">Dependent (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="ee242-989">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-990">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-990">Applicable Attributes</span></span>

<span data-ttu-id="ee242-991">L'elemento **ReferentialConstraint** può avere un numero qualsiasi di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="ee242-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="ee242-992">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-993">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-994">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-994">Example</span></span>

<span data-ttu-id="ee242-995">Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** usato come parte della definizione dell'associazione **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="ee242-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="ee242-996">Elemento ReturnType (Function) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="ee242-997">L'elemento **returnType** (Function) in Conceptual Schema Definition Language (CSDL) specifica il tipo restituito per una funzione definita in un elemento Function.</span><span class="sxs-lookup"><span data-stu-id="ee242-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="ee242-998">Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="ee242-999">I tipi restituiti possono essere qualsiasi **EDMSimpleType**, tipo di entità, tipo complesso, tipo di riga, tipo di riferimento o una raccolta di uno di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="ee242-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="ee242-1000">Il tipo restituito di una funzione può essere specificato con l'attributo **Type** dell'elemento **returnType** (Function) o con uno degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="ee242-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ee242-1001">CollectionType</span></span>
-   <span data-ttu-id="ee242-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="ee242-1002">ReferenceType</span></span>
-   <span data-ttu-id="ee242-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="ee242-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-1004">Un modello non viene convalidato se si specifica un tipo restituito dalla funzione con l'attributo **Type** dell'elemento **returnType** (Function) e uno degli elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="ee242-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-1005">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1005">Applicable Attributes</span></span>

<span data-ttu-id="ee242-1006">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **returnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="ee242-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="ee242-1007">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-1007">Attribute Name</span></span> | <span data-ttu-id="ee242-1008">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-1008">Is Required</span></span> | <span data-ttu-id="ee242-1009">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="ee242-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="ee242-1010">**ReturnType**</span></span> | <span data-ttu-id="ee242-1011">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1011">No</span></span>          | <span data-ttu-id="ee242-1012">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-1013">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="ee242-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="ee242-1014">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-1015">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-1016">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1016">Example</span></span>

<span data-ttu-id="ee242-1017">Nell'esempio seguente viene usato un elemento **Function** per definire una funzione che restituisce il numero di anni in cui un libro è stato stampato.</span><span class="sxs-lookup"><span data-stu-id="ee242-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="ee242-1018">Si noti che il tipo restituito viene specificato dall'attributo **Type** di un elemento **returnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="ee242-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="ee242-1019">Elemento ReturnType (FunctionImport) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="ee242-1020">L'elemento **returnType** (FunctionImport) in Conceptual Schema Definition Language (CSDL) specifica il tipo restituito per una funzione definita in un elemento FunctionImport.</span><span class="sxs-lookup"><span data-stu-id="ee242-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="ee242-1021">Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="ee242-1022">I tipi restituiti possono essere qualsiasi raccolta di tipi di entità, tipi complessi o **EDMSimpleType**,</span><span class="sxs-lookup"><span data-stu-id="ee242-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="ee242-1023">Il tipo restituito di una funzione viene specificato con l'attributo **Type** dell'elemento **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="ee242-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-1024">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1024">Applicable Attributes</span></span>

<span data-ttu-id="ee242-1025">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="ee242-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="ee242-1026">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-1026">Attribute Name</span></span> | <span data-ttu-id="ee242-1027">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-1027">Is Required</span></span> | <span data-ttu-id="ee242-1028">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-1029">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-1029">**Type**</span></span>       | <span data-ttu-id="ee242-1030">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1030">No</span></span>          | <span data-ttu-id="ee242-1031">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="ee242-1031">The type that the function returns.</span></span> <span data-ttu-id="ee242-1032">Il valore deve essere una raccolta di ComplexType, EntityType o EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="ee242-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="ee242-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="ee242-1033">**EntitySet**</span></span>  | <span data-ttu-id="ee242-1034">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1034">No</span></span>          | <span data-ttu-id="ee242-1035">Se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve essere il set di entità a cui appartiene la raccolta.</span><span class="sxs-lookup"><span data-stu-id="ee242-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="ee242-1036">In caso contrario, l'attributo **EntitySet** non deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="ee242-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-1037">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="ee242-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="ee242-1038">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-1039">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-1040">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1040">Example</span></span>

<span data-ttu-id="ee242-1041">Nell'esempio seguente viene usato un **FunctionImport** che restituisce libri e autori.</span><span class="sxs-lookup"><span data-stu-id="ee242-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="ee242-1042">Si noti che la funzione restituisce due set di risultati e pertanto sono specificati due elementi **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="ee242-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="ee242-1043">Elemento RowType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="ee242-1044">Un elemento **RowType** in Conceptual Schema Definition Language (CSDL) definisce una struttura senza nome come parametro o tipo restituito per una funzione definita nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="ee242-1045">Un elemento **RowType** può essere figlio dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="ee242-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="ee242-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ee242-1046">CollectionType</span></span>
-   <span data-ttu-id="ee242-1047">Parametro</span><span class="sxs-lookup"><span data-stu-id="ee242-1047">Parameter</span></span>
-   <span data-ttu-id="ee242-1048">ReturnType (funzione)</span><span class="sxs-lookup"><span data-stu-id="ee242-1048">ReturnType (Function)</span></span>

<span data-ttu-id="ee242-1049">Un elemento **RowType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-1050">Property (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-1050">Property (one or more)</span></span>
-   <span data-ttu-id="ee242-1051">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-1052">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1052">Applicable Attributes</span></span>

<span data-ttu-id="ee242-1053">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="ee242-1054">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-1055">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-1056">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1056">Example</span></span>

<span data-ttu-id="ee242-1057">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a><span data-ttu-id="ee242-1058">Elemento Schema (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="ee242-1059">L'elemento **schema** è l'elemento radice di una definizione del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="ee242-1060">Contiene definizioni per gli oggetti, le funzioni e i contenitori che costituiscono un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="ee242-1061">L'elemento **schema** può contenere zero o più degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="ee242-1062">Con</span><span class="sxs-lookup"><span data-stu-id="ee242-1062">Using</span></span>
-   <span data-ttu-id="ee242-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="ee242-1063">EntityContainer</span></span>
-   <span data-ttu-id="ee242-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="ee242-1064">EntityType</span></span>
-   <span data-ttu-id="ee242-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="ee242-1065">EnumType</span></span>
-   <span data-ttu-id="ee242-1066">Association Rules</span><span class="sxs-lookup"><span data-stu-id="ee242-1066">Association</span></span>
-   <span data-ttu-id="ee242-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="ee242-1067">ComplexType</span></span>
-   <span data-ttu-id="ee242-1068">Funzione</span><span class="sxs-lookup"><span data-stu-id="ee242-1068">Function</span></span>

<span data-ttu-id="ee242-1069">Un elemento **dello schema** può contenere zero o un elemento Annotation.</span><span class="sxs-lookup"><span data-stu-id="ee242-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-1070">L'elemento **Function** e gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="ee242-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="ee242-1071">L'elemento **schema** utilizza l'attributo **namespace** per definire lo spazio dei nomi per il tipo di entità, il tipo complesso e gli oggetti di associazione in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="ee242-1072">All'interno di uno spazio dei nomi due oggetti non possono avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="ee242-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="ee242-1073">Gli spazi dei nomi possono estendersi a più elementi **dello schema** e a più file con estensione csdl.</span><span class="sxs-lookup"><span data-stu-id="ee242-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="ee242-1074">Uno spazio dei nomi del modello concettuale è diverso dallo spazio dei nomi XML dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="ee242-1075">Uno spazio dei nomi del modello concettuale (definito dall'attributo **namespace** ) è un contenitore logico per tipi di entità, tipi complessi e tipi di associazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="ee242-1076">Lo spazio dei nomi XML, indicato dall'attributo **xmlns** , di un elemento **schema** è lo spazio dei nomi predefinito per gli elementi figlio e gli attributi dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="ee242-1077">Gli spazi dei nomi XML del form https://schemas.microsoft.com/ado/YYYY/MM/edm (dove aaaa e mm rappresentano rispettivamente l'anno e il mese) sono riservati per CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="ee242-1078">Gli elementi e gli attributi personalizzati non possono essere in spazi dei nomi che hanno questo form.</span><span class="sxs-lookup"><span data-stu-id="ee242-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-1079">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1079">Applicable Attributes</span></span>

<span data-ttu-id="ee242-1080">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="ee242-1081">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-1081">Attribute Name</span></span> | <span data-ttu-id="ee242-1082">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-1082">Is Required</span></span> | <span data-ttu-id="ee242-1083">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-1084">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="ee242-1084">**Namespace**</span></span>  | <span data-ttu-id="ee242-1085">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1085">Yes</span></span>         | <span data-ttu-id="ee242-1086">Spazio dei nomi del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="ee242-1087">Il valore dell'attributo **namespace** viene utilizzato per formare il nome completo di un tipo.</span><span class="sxs-lookup"><span data-stu-id="ee242-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="ee242-1088">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi Simple. example. Model, il nome completo di **EntityType** sarà sarà SimpleExampleModel. Customer.</span><span class="sxs-lookup"><span data-stu-id="ee242-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="ee242-1089">Non è possibile utilizzare le seguenti stringhe come valore per l'attributo **namespace** : **System**, **Transient**o **EDM**.</span><span class="sxs-lookup"><span data-stu-id="ee242-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="ee242-1090">Il valore dell'attributo **namespace** non può corrispondere al valore dell'attributo **namespace** nell'elemento schema SSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="ee242-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="ee242-1091">**Alias**</span></span>      | <span data-ttu-id="ee242-1092">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1092">No</span></span>          | <span data-ttu-id="ee242-1093">Identificatore utilizzato al posto del nome dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ee242-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="ee242-1094">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi Simple. example. Model e il valore dell'attributo **alias** è *Model*, sarà possibile utilizzare Model. Customer come nome completo dell'elemento **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="ee242-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="ee242-1095">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="ee242-1096">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-1097">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-1098">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1098">Example</span></span>

<span data-ttu-id="ee242-1099">Nell'esempio seguente viene illustrato un elemento **dello schema** che contiene un elemento **EntityContainer** , due elementi **EntityType** e un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="ee242-1100">Elemento TypeRef (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="ee242-1101">L'elemento **typeref** in Conceptual Schema Definition Language (CSDL) fornisce un riferimento a un tipo denominato esistente.</span><span class="sxs-lookup"><span data-stu-id="ee242-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="ee242-1102">L'elemento **typeref** può essere un elemento figlio dell'elemento CollectionType, che viene usato per specificare che una funzione ha una raccolta come parametro o tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="ee242-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="ee242-1103">Un elemento **typeref** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="ee242-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ee242-1104">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="ee242-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ee242-1105">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="ee242-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-1106">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1106">Applicable Attributes</span></span>

<span data-ttu-id="ee242-1107">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **typeref** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="ee242-1108">Si noti che gli attributi **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**e **Collation** sono applicabili solo a **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="ee242-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="ee242-1109">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="ee242-1110">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-1110">Is Required</span></span> | <span data-ttu-id="ee242-1111">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-1112">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="ee242-1112">**Type**</span></span>                                                           | <span data-ttu-id="ee242-1113">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1113">No</span></span>          | <span data-ttu-id="ee242-1114">Nome del tipo a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="ee242-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="ee242-1115">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="ee242-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="ee242-1116">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1116">No</span></span>          | <span data-ttu-id="ee242-1117">**True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="ee242-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="ee242-1118">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="ee242-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="ee242-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="ee242-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="ee242-1120">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1120">No</span></span>          | <span data-ttu-id="ee242-1121">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ee242-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="ee242-1123">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1123">No</span></span>          | <span data-ttu-id="ee242-1124">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ee242-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="ee242-1126">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1126">No</span></span>          | <span data-ttu-id="ee242-1127">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="ee242-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ee242-1128">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-1128">**Precision**</span></span>                                                      | <span data-ttu-id="ee242-1129">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1129">No</span></span>          | <span data-ttu-id="ee242-1130">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ee242-1131">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-1131">**Scale**</span></span>                                                          | <span data-ttu-id="ee242-1132">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1132">No</span></span>          | <span data-ttu-id="ee242-1133">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ee242-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-1134">**SRID**</span></span>                                                           | <span data-ttu-id="ee242-1135">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1135">No</span></span>          | <span data-ttu-id="ee242-1136">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ee242-1137">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ee242-1138">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee242-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ee242-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ee242-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="ee242-1140">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1140">No</span></span>          | <span data-ttu-id="ee242-1141">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="ee242-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ee242-1142">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="ee242-1142">**Collation**</span></span>                                                      | <span data-ttu-id="ee242-1143">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1143">No</span></span>          | <span data-ttu-id="ee242-1144">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="ee242-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ee242-1145">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="ee242-1146">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-1147">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-1148">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1148">Example</span></span>

<span data-ttu-id="ee242-1149">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza l'elemento **typeref** (come figlio di un elemento **CollectionType** ) per specificare che la funzione accetta una raccolta di tipi di entità **Department** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="ee242-1150">Elemento Using (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="ee242-1151">L'elemento **using** in Conceptual Schema Definition Language (CSDL) importa il contenuto di un modello concettuale presente in uno spazio dei nomi diverso.</span><span class="sxs-lookup"><span data-stu-id="ee242-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="ee242-1152">Impostando il valore dell'attributo **namespace** , è possibile fare riferimento a tipi di entità, tipi complessi e tipi di associazione definiti in un altro modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="ee242-1153">Più di un elemento **using** può essere figlio di un elemento **dello schema** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-1154">L'elemento **using** in CSDL non funziona esattamente come un'istruzione **using** in un linguaggio di programmazione.</span><span class="sxs-lookup"><span data-stu-id="ee242-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="ee242-1155">Importando uno spazio dei nomi con un'istruzione **using** in un linguaggio di programmazione, non si influiscono sugli oggetti nello spazio dei nomi originale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="ee242-1156">In CSDL, uno spazio dei nomi importato può contenere un tipo di entità derivato da un tipo di entità nello spazio dei nomi originale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="ee242-1157">Ciò può influire sui set di entità dichiarati nello spazio dei nomi originale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="ee242-1158">L'elemento **using** può includere i seguenti elementi figlio:</span><span class="sxs-lookup"><span data-stu-id="ee242-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="ee242-1159">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="ee242-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ee242-1160">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="ee242-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ee242-1161">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1161">Applicable Attributes</span></span>

<span data-ttu-id="ee242-1162">La tabella seguente descrive gli attributi che possono essere applicati all'elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="ee242-1163">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="ee242-1163">Attribute Name</span></span> | <span data-ttu-id="ee242-1164">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="ee242-1164">Is Required</span></span> | <span data-ttu-id="ee242-1165">Valore</span><span class="sxs-lookup"><span data-stu-id="ee242-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ee242-1166">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="ee242-1166">**Namespace**</span></span>  | <span data-ttu-id="ee242-1167">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1167">Yes</span></span>         | <span data-ttu-id="ee242-1168">Nome dello spazio dei nomi importato.</span><span class="sxs-lookup"><span data-stu-id="ee242-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="ee242-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="ee242-1169">**Alias**</span></span>      | <span data-ttu-id="ee242-1170">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1170">Yes</span></span>         | <span data-ttu-id="ee242-1171">Identificatore utilizzato al posto del nome dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ee242-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="ee242-1172">Anche se questo attributo è obbligatorio, non è necessario utilizzarlo al posto del nome dello spazio dei nomi per qualificare nomi di oggetti.</span><span class="sxs-lookup"><span data-stu-id="ee242-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ee242-1173">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="ee242-1174">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ee242-1175">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ee242-1176">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1176">Example</span></span>

<span data-ttu-id="ee242-1177">Nell'esempio seguente viene illustrato l'elemento **using** usato per importare uno spazio dei nomi definito altrove.</span><span class="sxs-lookup"><span data-stu-id="ee242-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="ee242-1178">Si noti che lo spazio dei nomi per l'elemento **dello schema** visualizzato è `BooksModel` .</span><span class="sxs-lookup"><span data-stu-id="ee242-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="ee242-1179">La `Address` Proprietà in `Publisher` **EntityType** è un tipo complesso definito nello `ExtendedBooksModel` spazio dei nomi (importato con l'elemento **using** ).</span><span class="sxs-lookup"><span data-stu-id="ee242-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="ee242-1180">Attributi di annotazione (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="ee242-1181">Gli attributi di annotazione in Conceptual Schema Definition Language (CSDL) sono attributi XML personalizzati nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="ee242-1182">Oltre ad avere una struttura XML valida, per gli attributi di annotazione hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="ee242-1183">Gli attributi di annotazione non devono trovarsi in spazi dei nomi XML riservati a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="ee242-1184">È possibile applicare più attributi di annotazione a un determinato elemento CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="ee242-1185">I nomi completi di due attributi di annotazione non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="ee242-1186">Gli attributi di annotazione possono essere utilizzati per fornire metadati aggiuntivi sugli elementi in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="ee242-1187">È possibile accedere ai metadati contenuti negli elementi Annotation in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="ee242-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-1188">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1188">Example</span></span>

<span data-ttu-id="ee242-1189">Nell'esempio seguente viene illustrato un elemento **EntityType** con un attributo Annotation (**CustomAttribute**).</span><span class="sxs-lookup"><span data-stu-id="ee242-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="ee242-1190">Nell'esempio viene inoltre mostrato un elemento di annotazione applicato all'elemento del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-1190">The example also shows an annotation element applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

<span data-ttu-id="ee242-1191">Il codice seguente recupera i metadati dell'attributo di annotazione e li scrive nella console:</span><span class="sxs-lookup"><span data-stu-id="ee242-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

<span data-ttu-id="ee242-1192">Nel codice riportato in precedenza si presuppone che il file `School.csdl` si trovi nella directory di output del progetto e che al progetto siano state aggiunte le istruzioni `Imports` e `Using` seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="ee242-1193">Elementi di annotazione (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="ee242-1194">Gli elementi Annotation in Conceptual Schema Definition Language (CSDL) sono elementi XML personalizzati nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="ee242-1195">Oltre ad avere una struttura XML valida, gli elementi Annotation hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="ee242-1196">Gli elementi Annotation non devono trovarsi in spazi dei nomi XML riservati a CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="ee242-1197">Più elementi Annotation possono essere figli di un dato elemento CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="ee242-1198">I nomi completi di due elementi Annotation non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="ee242-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="ee242-1199">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio di un dato elemento CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="ee242-1200">Gli elementi Annotation possono essere utilizzati per fornire metadati aggiuntivi sugli elementi in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="ee242-1201">A partire da .NET Framework versione 4, è possibile accedere ai metadati contenuti negli elementi di annotazione in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="ee242-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-1202">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1202">Example</span></span>

<span data-ttu-id="ee242-1203">Nell'esempio seguente viene illustrato un elemento **EntityType** con un elemento Annotation (**CustomElement**).</span><span class="sxs-lookup"><span data-stu-id="ee242-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="ee242-1204">Nell'esempio viene inoltre mostrato un attributo di annotazione applicato all'elemento del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ee242-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

<span data-ttu-id="ee242-1205">Il codice seguente recupera i metadati dell'elemento Annotation e li scrive nella console:</span><span class="sxs-lookup"><span data-stu-id="ee242-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

<span data-ttu-id="ee242-1206">Nel codice riportato in precedenza si presuppone che il file School.csdl si trovi nella directory di output del progetto e che al progetto siano state aggiunte le istruzioni `Imports` e `Using` seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="ee242-1207">Tipi del modello concettuale (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="ee242-1208">CSDL (Conceptual Schema Definition Language) supporta un set di tipi di dati primitivi astratti, denominati **EDMSimpleTypes**, che definiscono le proprietà in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="ee242-1209">**EDMSimpleTypes** sono proxy per i tipi di dati primitivi supportati nell'ambiente di archiviazione o host.</span><span class="sxs-lookup"><span data-stu-id="ee242-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="ee242-1210">Nella tabella seguente vengono elencati i tipi di dati primitivi supportati da CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="ee242-1211">Nella tabella sono inoltre elencati i facet che è possibile applicare a ogni **EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="ee242-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="ee242-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="ee242-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="ee242-1213">Descrizione</span><span class="sxs-lookup"><span data-stu-id="ee242-1213">Description</span></span>                                                | <span data-ttu-id="ee242-1214">Facet applicabili</span><span class="sxs-lookup"><span data-stu-id="ee242-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="ee242-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="ee242-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="ee242-1216">Contiene dati binari.</span><span class="sxs-lookup"><span data-stu-id="ee242-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="ee242-1217">MaxLength, FixedLength, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="ee242-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="ee242-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="ee242-1219">Contiene il valore **true** o **false**.</span><span class="sxs-lookup"><span data-stu-id="ee242-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="ee242-1220">Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="ee242-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="ee242-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="ee242-1222">Contiene un Unsigned Integer a 8 bit.</span><span class="sxs-lookup"><span data-stu-id="ee242-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="ee242-1223">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="ee242-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="ee242-1225">Rappresenta una data e un'ora.</span><span class="sxs-lookup"><span data-stu-id="ee242-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="ee242-1226">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="ee242-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="ee242-1228">Contiene una data e un'ora come offset in minuti rispetto all'ora GMT.</span><span class="sxs-lookup"><span data-stu-id="ee242-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="ee242-1229">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="ee242-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="ee242-1231">Contiene un valore numerico con scala e precisione fisse.</span><span class="sxs-lookup"><span data-stu-id="ee242-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="ee242-1232">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="ee242-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="ee242-1234">Contiene un numero a virgola mobile con precisione di 15 cifre</span><span class="sxs-lookup"><span data-stu-id="ee242-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="ee242-1235">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1236">**EDM. float**</span><span class="sxs-lookup"><span data-stu-id="ee242-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="ee242-1237">Contiene un numero a virgola mobile con precisione a 7 cifre.</span><span class="sxs-lookup"><span data-stu-id="ee242-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="ee242-1238">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="ee242-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="ee242-1240">Contiene un identificatore univoco a 16 byte.</span><span class="sxs-lookup"><span data-stu-id="ee242-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="ee242-1241">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="ee242-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="ee242-1243">Contiene un Signed Integer a 16 bit.</span><span class="sxs-lookup"><span data-stu-id="ee242-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="ee242-1244">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="ee242-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="ee242-1246">Contiene un Signed Integer a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="ee242-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="ee242-1247">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="ee242-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="ee242-1249">Contiene un Signed Integer a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="ee242-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="ee242-1250">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="ee242-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="ee242-1252">Contiene un Signed Integer a 8 bit.</span><span class="sxs-lookup"><span data-stu-id="ee242-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="ee242-1253">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="ee242-1254">**Edm.String**</span></span>                   | <span data-ttu-id="ee242-1255">Contiene dati di tipo carattere.</span><span class="sxs-lookup"><span data-stu-id="ee242-1255">Contains character data.</span></span>                                   | <span data-ttu-id="ee242-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="ee242-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="ee242-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="ee242-1258">Contiene un'ora del giorno.</span><span class="sxs-lookup"><span data-stu-id="ee242-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="ee242-1259">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="ee242-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ee242-1260">**EDM. Geography**</span><span class="sxs-lookup"><span data-stu-id="ee242-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="ee242-1261">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="ee242-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="ee242-1263">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1264">**EDM. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="ee242-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="ee242-1265">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1266">**EDM. GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="ee242-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="ee242-1267">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1268">**EDM. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="ee242-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="ee242-1269">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1270">**EDM. GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="ee242-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="ee242-1271">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1272">**EDM. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="ee242-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="ee242-1273">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1274">**EDM. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="ee242-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="ee242-1275">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1276">**EDM. Geometry**</span><span class="sxs-lookup"><span data-stu-id="ee242-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="ee242-1277">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1278">**EDM. GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="ee242-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="ee242-1279">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1280">**EDM. GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="ee242-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="ee242-1281">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1282">**EDM. GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="ee242-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="ee242-1283">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1284">**EDM. GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="ee242-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="ee242-1285">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1286">**EDM. GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="ee242-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="ee242-1287">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1288">**EDM. GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="ee242-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="ee242-1289">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ee242-1290">**EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="ee242-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="ee242-1291">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="ee242-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="ee242-1292">Facet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ee242-1292">Facets (CSDL)</span></span>

<span data-ttu-id="ee242-1293">I facet in Conceptual Schema Definition Language (CSDL) rappresentano vincoli sulle proprietà di tipi di entità e di tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="ee242-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="ee242-1294">I facet appaiono come attributi XML negli elementi CSDL seguenti:</span><span class="sxs-lookup"><span data-stu-id="ee242-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="ee242-1295">Proprietà</span><span class="sxs-lookup"><span data-stu-id="ee242-1295">Property</span></span>
-   <span data-ttu-id="ee242-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="ee242-1296">TypeRef</span></span>
-   <span data-ttu-id="ee242-1297">Parametro</span><span class="sxs-lookup"><span data-stu-id="ee242-1297">Parameter</span></span>

<span data-ttu-id="ee242-1298">Nella tabella seguente vengono descritti i facet supportati in CSDL.</span><span class="sxs-lookup"><span data-stu-id="ee242-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="ee242-1299">Tutti i facet sono facoltativi.</span><span class="sxs-lookup"><span data-stu-id="ee242-1299">All facets are optional.</span></span> <span data-ttu-id="ee242-1300">Alcuni facet elencati di seguito vengono usati dal Entity Framework durante la generazione di un database da un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="ee242-1301">Per informazioni sui tipi di dati in un modello concettuale, vedere tipi di modelli concettuali (CSDL).</span><span class="sxs-lookup"><span data-stu-id="ee242-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="ee242-1302">Facet</span><span class="sxs-lookup"><span data-stu-id="ee242-1302">Facet</span></span>               | <span data-ttu-id="ee242-1303">Descrizione</span><span class="sxs-lookup"><span data-stu-id="ee242-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="ee242-1304">Si applica a</span><span class="sxs-lookup"><span data-stu-id="ee242-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="ee242-1305">Utilizzato per la generazione di database.</span><span class="sxs-lookup"><span data-stu-id="ee242-1305">Used for the database generation</span></span> | <span data-ttu-id="ee242-1306">Utilizzato dal runtime</span><span class="sxs-lookup"><span data-stu-id="ee242-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="ee242-1307">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="ee242-1307">**Collation**</span></span>       | <span data-ttu-id="ee242-1308">Specifica la sequenza di ordinamento da usare quando si eseguono operazioni di confronto e di ordinamento su valori della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="ee242-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="ee242-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ee242-1310">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1310">Yes</span></span>                              | <span data-ttu-id="ee242-1311">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1311">No</span></span>                  |
| <span data-ttu-id="ee242-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="ee242-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="ee242-1313">Indica che il valore della proprietà deve essere usato per le verifiche della concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="ee242-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="ee242-1314">Tutte le proprietà di **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="ee242-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="ee242-1315">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1315">No</span></span>                               | <span data-ttu-id="ee242-1316">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-1316">Yes</span></span>                 |
| <span data-ttu-id="ee242-1317">**Default**</span><span class="sxs-lookup"><span data-stu-id="ee242-1317">**Default**</span></span>         | <span data-ttu-id="ee242-1318">Specifica il valore predefinito della proprietà se durante la creazione di istanze non viene fornito alcun valore.</span><span class="sxs-lookup"><span data-stu-id="ee242-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="ee242-1319">Tutte le proprietà di **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="ee242-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="ee242-1320">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1320">Yes</span></span>                              | <span data-ttu-id="ee242-1321">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1321">Yes</span></span>                 |
| <span data-ttu-id="ee242-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-1322">**FixedLength**</span></span>     | <span data-ttu-id="ee242-1323">Specifica se la lunghezza del valore della proprietà può variare.</span><span class="sxs-lookup"><span data-stu-id="ee242-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="ee242-1324">**EDM. Binary**, **EDM. String**</span><span class="sxs-lookup"><span data-stu-id="ee242-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ee242-1325">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1325">Yes</span></span>                              | <span data-ttu-id="ee242-1326">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1326">No</span></span>                  |
| <span data-ttu-id="ee242-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ee242-1327">**MaxLength**</span></span>       | <span data-ttu-id="ee242-1328">Specifica la lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="ee242-1329">**EDM. Binary**, **EDM. String**</span><span class="sxs-lookup"><span data-stu-id="ee242-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ee242-1330">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1330">Yes</span></span>                              | <span data-ttu-id="ee242-1331">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1331">No</span></span>                  |
| <span data-ttu-id="ee242-1332">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="ee242-1332">**Nullable**</span></span>        | <span data-ttu-id="ee242-1333">Specifica se la proprietà può avere un valore **null** .</span><span class="sxs-lookup"><span data-stu-id="ee242-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="ee242-1334">Tutte le proprietà di **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="ee242-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="ee242-1335">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-1335">Yes</span></span>                              | <span data-ttu-id="ee242-1336">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-1336">Yes</span></span>                 |
| <span data-ttu-id="ee242-1337">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="ee242-1337">**Precision**</span></span>       | <span data-ttu-id="ee242-1338">Per le proprietà di tipo **Decimal**, specifica il numero di cifre che un valore della proprietà può avere.</span><span class="sxs-lookup"><span data-stu-id="ee242-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="ee242-1339">Per le proprietà di tipo **Time**, **DateTime**e **DateTimeOffset**, specifica il numero di cifre per la parte frazionaria dei secondi del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="ee242-1340">**EDM. DateTime**, **EDM. DateTimeOffset**, **EDM. Decimal**, **EDM. Time**</span><span class="sxs-lookup"><span data-stu-id="ee242-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="ee242-1341">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-1341">Yes</span></span>                              | <span data-ttu-id="ee242-1342">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1342">No</span></span>                  |
| <span data-ttu-id="ee242-1343">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="ee242-1343">**Scale**</span></span>           | <span data-ttu-id="ee242-1344">Specifica il numero di cifre a destra del separatore decimale per il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="ee242-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="ee242-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="ee242-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="ee242-1346">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1346">Yes</span></span>                              | <span data-ttu-id="ee242-1347">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1347">No</span></span>                  |
| <span data-ttu-id="ee242-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ee242-1348">**SRID**</span></span>            | <span data-ttu-id="ee242-1349">Specifica l'ID del sistema di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="ee242-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="ee242-1350">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="ee242-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="ee242-1351">**EDM. Geography, Edm. GeographyPoint, Edm. GeographyLineString, Edm. GeographyPolygon, Edm. GeographyMultiPoint, Edm. GeographyMultiLineString, Edm. GeographyMultiPolygon, Edm. GeographyCollection, Edm. Geometry, Edm. GeometryPoint, Edm. GeometryLineString, Edm. GeometryPolygon, Edm. GeometryMultiPoint, Edm. GeometryMultiLineString, Edm. GeometryMultiPolygon, Edm. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="ee242-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="ee242-1352">No</span><span class="sxs-lookup"><span data-stu-id="ee242-1352">No</span></span>                               | <span data-ttu-id="ee242-1353">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-1353">Yes</span></span>                 |
| <span data-ttu-id="ee242-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ee242-1354">**Unicode**</span></span>         | <span data-ttu-id="ee242-1355">Viene indicato se il valore della proprietà viene archiviato come Unicode.</span><span class="sxs-lookup"><span data-stu-id="ee242-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="ee242-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="ee242-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ee242-1357">Sì</span><span class="sxs-lookup"><span data-stu-id="ee242-1357">Yes</span></span>                              | <span data-ttu-id="ee242-1358">Yes</span><span class="sxs-lookup"><span data-stu-id="ee242-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="ee242-1359">Quando si genera un database da un modello concettuale, la procedura guidata genera database riconoscerà il valore dell'attributo **StoreGeneratedPattern** su un elemento **Proprietà** se si trova nello spazio dei nomi seguente: https://schemas.microsoft.com/ado/2009/02/edm/annotation .</span><span class="sxs-lookup"><span data-stu-id="ee242-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="ee242-1360">I valori supportati per l'attributo sono **Identity** e **calcolato**.</span><span class="sxs-lookup"><span data-stu-id="ee242-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="ee242-1361">Il valore **Identity** produrrà una colonna di database con un valore Identity generato nel database.</span><span class="sxs-lookup"><span data-stu-id="ee242-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="ee242-1362">Il valore **calcolato** produrrà una colonna con un valore calcolato nel database.</span><span class="sxs-lookup"><span data-stu-id="ee242-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="ee242-1363">Esempio</span><span class="sxs-lookup"><span data-stu-id="ee242-1363">Example</span></span>

<span data-ttu-id="ee242-1364">Nell'esempio seguente vengono mostrati i facet applicati alle proprietà di un tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="ee242-1364">The following example shows facets applied to the properties of an entity type:</span></span>

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
