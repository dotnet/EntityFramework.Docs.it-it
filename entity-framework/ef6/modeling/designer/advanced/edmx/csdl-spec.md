---
title: Specifica CSDL-EF6
description: Specifica CSDL in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 9fdd8fc5ed16f7ba7d11e79a9449f120f5d579c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066356"
---
# <a name="csdl-specification"></a><span data-ttu-id="c7574-103">Specifica CSDL</span><span class="sxs-lookup"><span data-stu-id="c7574-103">CSDL Specification</span></span>
<span data-ttu-id="c7574-104">Conceptual Schema Definition Language (CSDL) è un linguaggio basato su XML che descrive le entità, le relazioni e le funzioni che costituiscono un modello concettuale di un'applicazione basata sui dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="c7574-105">Questo modello concettuale può essere utilizzato dal Entity Framework o WCF Data Services.</span><span class="sxs-lookup"><span data-stu-id="c7574-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="c7574-106">I metadati descritti con CSDL vengono utilizzati dalla Entity Framework per eseguire il mapping di entità e relazioni definite in un modello concettuale a un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="c7574-107">Per ulteriori informazioni, vedere [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) e [specifica MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span><span class="sxs-lookup"><span data-stu-id="c7574-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="c7574-108">CSDL è l'implementazione Entity Framework dell'Entity Data Model.</span><span class="sxs-lookup"><span data-stu-id="c7574-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="c7574-109">In un'applicazione Entity Framework, i metadati del modello concettuale vengono caricati da un file con estensione CSDL (scritto in CSDL) in un'istanza di System. Data. Metadata. Edm. EdmItemCollection ed è possibile accedervi usando i metodi della classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="c7574-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="c7574-110">Entity Framework usa i metadati del modello concettuale per tradurre le query sul modello concettuale in comandi specifici dell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="c7574-111">La finestra di progettazione EF archivia le informazioni sul modello concettuale in un file con estensione edmx in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="c7574-112">In fase di compilazione, la finestra di progettazione EF utilizza le informazioni in un file con estensione edmx per creare il file con estensione csdl necessario per Entity Framework in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="c7574-113">Le versioni di CSDL si differenziano tra loro per gli spazi dei nomi XML.</span><span class="sxs-lookup"><span data-stu-id="c7574-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="c7574-114">Versione CSDL</span><span class="sxs-lookup"><span data-stu-id="c7574-114">CSDL Version</span></span> | <span data-ttu-id="c7574-115">Spazio dei nomi XML</span><span class="sxs-lookup"><span data-stu-id="c7574-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="c7574-116">CSDL V1</span><span class="sxs-lookup"><span data-stu-id="c7574-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="c7574-117">CSDL V2</span><span class="sxs-lookup"><span data-stu-id="c7574-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="c7574-118">CSDL V3</span><span class="sxs-lookup"><span data-stu-id="c7574-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="c7574-119">Elemento Association (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-119">Association Element (CSDL)</span></span>

<span data-ttu-id="c7574-120">Un elemento **Association** definisce una relazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="c7574-121">Un'associazione deve specificare i tipi di entità coinvolti nella relazione e il possibile numero di tipi di entità a ogni entità finale della relazione, che è noto come molteplicità.</span><span class="sxs-lookup"><span data-stu-id="c7574-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="c7574-122">La molteplicità di un'entità finale di un'associazione può avere un valore pari a uno (1), zero o uno (0.. 1) o molti ( \* ).</span><span class="sxs-lookup"><span data-stu-id="c7574-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="c7574-123">Queste informazioni vengono specificate in due elementi End figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="c7574-124">Le istanze del tipo di entità in corrispondenza di un'entità finale di un'associazione sono accessibili attraverso proprietà di navigazione o chiavi esterne se sono esposte in un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="c7574-125">In un'applicazione, un'istanza di un'associazione rappresenta un'associazione specifica tra istanze di tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="c7574-126">Le istanze dell'associazione sono raggruppate logicamente in un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="c7574-127">Un elemento **Association** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-128">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-129">End (esattamente 2 elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="c7574-130">ReferentialConstraint (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="c7574-131">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-132">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-132">Applicable Attributes</span></span>

<span data-ttu-id="c7574-133">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="c7574-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="c7574-134">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-134">Attribute Name</span></span> | <span data-ttu-id="c7574-135">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-135">Is Required</span></span> | <span data-ttu-id="c7574-136">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="c7574-137">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-137">**Name**</span></span>       | <span data-ttu-id="c7574-138">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-138">Yes</span></span>         | <span data-ttu-id="c7574-139">Nome dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-140">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="c7574-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="c7574-141">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-142">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-143">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-143">Example</span></span>

<span data-ttu-id="c7574-144">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** quando le chiavi esterne non sono state esposte sui tipi di entità **Customer** e **Order** .</span><span class="sxs-lookup"><span data-stu-id="c7574-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="c7574-145">I valori di **molteplicità** per ogni **estremità** dell'associazione indicano che molti **ordini** possono essere associati a un **cliente**, ma solo un **cliente** può essere associato a un **ordine**.</span><span class="sxs-lookup"><span data-stu-id="c7574-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="c7574-146">Inoltre, l'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati se il **cliente** viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="c7574-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="c7574-147">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** quando le chiavi esterne sono state esposte sui tipi di entità **Customer** e **Order** .</span><span class="sxs-lookup"><span data-stu-id="c7574-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="c7574-148">Con le chiavi esterne esposte, la relazione tra le entità viene gestita con un elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="c7574-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="c7574-149">Un elemento AssociationSetMapping corrispondente non è necessario per eseguire il mapping di questa associazione all'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="c7574-150">Elemento AssociationSet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="c7574-151">L'elemento **associativo** in Conceptual Schema Definition Language (CSDL) è un contenitore logico per le istanze di associazione dello stesso tipo.</span><span class="sxs-lookup"><span data-stu-id="c7574-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="c7574-152">Un set di associazioni fornisce una definizione per il raggruppamento di istanze di associazioni in modo che possano essere mappate a un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="c7574-153">L'elemento **associationname** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-154">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="c7574-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="c7574-155">End (esattamente due elementi richiesti)</span><span class="sxs-lookup"><span data-stu-id="c7574-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="c7574-156">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="c7574-157">L'attributo **Association** specifica il tipo di associazione contenuto in un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="c7574-158">I set di entità che costituiscono le entità finali di un set di associazioni vengono specificati con esattamente due elementi **end** figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-159">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-159">Applicable Attributes</span></span>

<span data-ttu-id="c7574-160">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="c7574-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="c7574-161">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-161">Attribute Name</span></span>  | <span data-ttu-id="c7574-162">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-162">Is Required</span></span> | <span data-ttu-id="c7574-163">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-164">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-164">**Name**</span></span>        | <span data-ttu-id="c7574-165">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-165">Yes</span></span>         | <span data-ttu-id="c7574-166">Nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-166">The name of the entity set.</span></span> <span data-ttu-id="c7574-167">Il valore dell'attributo **Name** non può corrispondere al valore dell'attributo **Association** .</span><span class="sxs-lookup"><span data-stu-id="c7574-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="c7574-168">**Associazione**</span><span class="sxs-lookup"><span data-stu-id="c7574-168">**Association**</span></span> | <span data-ttu-id="c7574-169">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-169">Yes</span></span>         | <span data-ttu-id="c7574-170">Il nome completo dell'associazione le cui istanze sono contenute nel set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="c7574-171">L'associazione deve essere nello stesso spazio dei nomi del set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-172">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="c7574-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="c7574-173">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-174">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-175">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-175">Example</span></span>

<span data-ttu-id="c7574-176">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con due elementi di **associazione** :</span><span class="sxs-lookup"><span data-stu-id="c7574-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="c7574-177">Elemento CollectionType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="c7574-178">L'elemento **CollectionType** in Conceptual Schema Definition Language (CSDL) specifica che un parametro di funzione o un tipo restituito della funzione è una raccolta.</span><span class="sxs-lookup"><span data-stu-id="c7574-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="c7574-179">L'elemento **CollectionType** può essere un elemento figlio dell'elemento Parameter o dell'elemento ReturnType (Function).</span><span class="sxs-lookup"><span data-stu-id="c7574-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="c7574-180">Il tipo di raccolta può essere specificato tramite l'attributo **Type** o uno degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="c7574-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="c7574-181">**CollectionType**</span></span>
-   <span data-ttu-id="c7574-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="c7574-182">ReferenceType</span></span>
-   <span data-ttu-id="c7574-183">RowType</span><span class="sxs-lookup"><span data-stu-id="c7574-183">RowType</span></span>
-   <span data-ttu-id="c7574-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="c7574-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-185">Un modello non convaliderà se il tipo di una raccolta viene specificato sia con l'attributo di **tipo** che con un elemento figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-186">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-186">Applicable Attributes</span></span>

<span data-ttu-id="c7574-187">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="c7574-188">Si noti che gli attributi **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**e **Collation** sono applicabili solo alle raccolte di **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="c7574-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="c7574-189">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-189">Attribute Name</span></span>                                                          | <span data-ttu-id="c7574-190">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-190">Is Required</span></span> | <span data-ttu-id="c7574-191">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-192">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-192">**Type**</span></span>                                                                | <span data-ttu-id="c7574-193">No</span><span class="sxs-lookup"><span data-stu-id="c7574-193">No</span></span>          | <span data-ttu-id="c7574-194">Tipo della raccolta.</span><span class="sxs-lookup"><span data-stu-id="c7574-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="c7574-195">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="c7574-195">**Nullable**</span></span>                                                            | <span data-ttu-id="c7574-196">No</span><span class="sxs-lookup"><span data-stu-id="c7574-196">No</span></span>          | <span data-ttu-id="c7574-197">**True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="c7574-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="c7574-198">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="c7574-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="c7574-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="c7574-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="c7574-200">No</span><span class="sxs-lookup"><span data-stu-id="c7574-200">No</span></span>          | <span data-ttu-id="c7574-201">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="c7574-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="c7574-203">No</span><span class="sxs-lookup"><span data-stu-id="c7574-203">No</span></span>          | <span data-ttu-id="c7574-204">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="c7574-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="c7574-206">No</span><span class="sxs-lookup"><span data-stu-id="c7574-206">No</span></span>          | <span data-ttu-id="c7574-207">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="c7574-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="c7574-208">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-208">**Precision**</span></span>                                                           | <span data-ttu-id="c7574-209">No</span><span class="sxs-lookup"><span data-stu-id="c7574-209">No</span></span>          | <span data-ttu-id="c7574-210">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="c7574-211">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-211">**Scale**</span></span>                                                               | <span data-ttu-id="c7574-212">No</span><span class="sxs-lookup"><span data-stu-id="c7574-212">No</span></span>          | <span data-ttu-id="c7574-213">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="c7574-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-214">**SRID**</span></span>                                                                | <span data-ttu-id="c7574-215">No</span><span class="sxs-lookup"><span data-stu-id="c7574-215">No</span></span>          | <span data-ttu-id="c7574-216">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="c7574-217">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="c7574-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="c7574-218">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="c7574-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="c7574-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="c7574-219">**Unicode**</span></span>                                                             | <span data-ttu-id="c7574-220">No</span><span class="sxs-lookup"><span data-stu-id="c7574-220">No</span></span>          | <span data-ttu-id="c7574-221">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="c7574-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="c7574-222">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="c7574-222">**Collation**</span></span>                                                           | <span data-ttu-id="c7574-223">No</span><span class="sxs-lookup"><span data-stu-id="c7574-223">No</span></span>          | <span data-ttu-id="c7574-224">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="c7574-225">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="c7574-226">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-227">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-228">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-228">Example</span></span>

<span data-ttu-id="c7574-229">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di tipi di entità **Person** (come specificato con l'attributo **elementType** ).</span><span class="sxs-lookup"><span data-stu-id="c7574-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="c7574-230">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="c7574-231">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza l'elemento **CollectionType** per specificare che la funzione accetta come parametro una raccolta di tipi di entità **Department** .</span><span class="sxs-lookup"><span data-stu-id="c7574-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="c7574-232">Elemento ComplexType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="c7574-233">Un elemento **complexType** definisce una struttura di dati composta da proprietà **EDMSimpleType** o altri tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="c7574-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="c7574-234">Un tipo complesso può essere una proprietà di un tipo di entità o un altro tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="c7574-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="c7574-235">Un tipo complesso è simile a un tipo di entità in quanto il tipo complesso definisce i dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="c7574-236">Tuttavia esistono alcune differenze importanti tra i tipi complessi e i tipi di entità:</span><span class="sxs-lookup"><span data-stu-id="c7574-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="c7574-237">I tipi complessi non dispongono di identità o chiavi e pertanto non possono esistere indipendentemente.</span><span class="sxs-lookup"><span data-stu-id="c7574-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="c7574-238">I tipi complessi possono esistere solo come proprietà di tipi di entità o gli altri tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="c7574-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="c7574-239">I tipi complessi non possono far parte di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="c7574-240">Le entità finali di un'associazione non possono essere tipi complessi, di conseguenza non è possibile definire proprietà di navigazione per i tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="c7574-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="c7574-241">Una proprietà di tipo complesso non può avere un valore null, sebbene ogni proprietà scalare di un tipo complesso possa essere impostata su Null.</span><span class="sxs-lookup"><span data-stu-id="c7574-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="c7574-242">Un elemento **complexType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-243">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-244">Property (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="c7574-245">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="c7574-246">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **complexType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="c7574-247">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="c7574-248">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-248">Is Required</span></span> | <span data-ttu-id="c7574-249">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-250">Nome</span><span class="sxs-lookup"><span data-stu-id="c7574-250">Name</span></span>                                                                                                           | <span data-ttu-id="c7574-251">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-251">Yes</span></span>         | <span data-ttu-id="c7574-252">Nome del tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="c7574-252">The name of the complex type.</span></span> <span data-ttu-id="c7574-253">Il nome di un tipo complesso non può essere uguale a quello di un altro tipo complesso, di un tipo di entità o di un'associazione che si trova entro l'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="c7574-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="c7574-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="c7574-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="c7574-255">No</span><span class="sxs-lookup"><span data-stu-id="c7574-255">No</span></span>          | <span data-ttu-id="c7574-256">Nome di un altro tipo complesso che è il tipo di base del tipo complesso definito.</span><span class="sxs-lookup"><span data-stu-id="c7574-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="c7574-257">> questo attributo non è applicabile in CSDL V1.</span><span class="sxs-lookup"><span data-stu-id="c7574-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="c7574-258">L'ereditarietà per i tipi complessi non è supportata in quella versione.</span><span class="sxs-lookup"><span data-stu-id="c7574-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="c7574-259">Contenuto</span><span class="sxs-lookup"><span data-stu-id="c7574-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="c7574-260">No</span><span class="sxs-lookup"><span data-stu-id="c7574-260">No</span></span>          | <span data-ttu-id="c7574-261">**True** o **false** (valore predefinito) a seconda che il tipo complesso sia un tipo astratto.</span><span class="sxs-lookup"><span data-stu-id="c7574-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="c7574-262">> questo attributo non è applicabile in CSDL V1.</span><span class="sxs-lookup"><span data-stu-id="c7574-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="c7574-263">I tipi complessi in quella versione non possono essere tipi astratti.</span><span class="sxs-lookup"><span data-stu-id="c7574-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="c7574-264">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **complexType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="c7574-265">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-266">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-267">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-267">Example</span></span>

<span data-ttu-id="c7574-268">Nell'esempio seguente viene illustrato un tipo complesso, **Address**, con le proprietà **EDMSimpleType** **StreetAddress**, **City**, **StateOrProvince**, **Country**e **PostalCode**.</span><span class="sxs-lookup"><span data-stu-id="c7574-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="c7574-269">Per definire l' **Indirizzo** del tipo complesso (sopra) come proprietà di un tipo di entità, è necessario dichiarare il tipo di proprietà nella definizione del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="c7574-270">Nell'esempio seguente viene illustrata la proprietà **Address** come tipo complesso in un tipo di entità (**Publisher**):</span><span class="sxs-lookup"><span data-stu-id="c7574-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="c7574-271">Elemento DefiningExpression (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="c7574-272">L'elemento **DefiningExpression** in Conceptual Schema Definition Language (CSDL) contiene un'espressione Entity SQL che definisce una funzione nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="c7574-273">Ai fini della convalida, un elemento **DefiningExpression** può contenere contenuto arbitrario.</span><span class="sxs-lookup"><span data-stu-id="c7574-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="c7574-274">Tuttavia, Entity Framework genererà un'eccezione in fase di esecuzione se un elemento **DefiningExpression** non contiene Entity SQL validi.</span><span class="sxs-lookup"><span data-stu-id="c7574-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-275">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-275">Applicable Attributes</span></span>

<span data-ttu-id="c7574-276">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **DefiningExpression** .</span><span class="sxs-lookup"><span data-stu-id="c7574-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="c7574-277">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-278">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-279">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-279">Example</span></span>

<span data-ttu-id="c7574-280">Nell'esempio seguente viene utilizzato un elemento **DefiningExpression** per definire una funzione che restituisce il numero di anni da quando un libro è stato pubblicato.</span><span class="sxs-lookup"><span data-stu-id="c7574-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="c7574-281">Il contenuto dell'elemento **DefiningExpression** è scritto in Entity SQL.</span><span class="sxs-lookup"><span data-stu-id="c7574-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="c7574-282">Elemento Dependent (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="c7574-283">L'elemento **dipendente** in Conceptual Schema Definition Language (CSDL) è un elemento figlio dell'elemento ReferentialConstraint e definisce l'entità finale dipendente di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="c7574-284">Un elemento **ReferentialConstraint** definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="c7574-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="c7574-285">Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="c7574-286">Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="c7574-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="c7574-287">Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="c7574-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="c7574-288">Gli elementi **PropertyRef** vengono utilizzati per specificare quali chiavi fanno riferimento all'entità finale principale.</span><span class="sxs-lookup"><span data-stu-id="c7574-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="c7574-289">L'elemento **dipendente** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-290">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="c7574-291">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-292">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-292">Applicable Attributes</span></span>

<span data-ttu-id="c7574-293">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="c7574-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="c7574-294">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-294">Attribute Name</span></span> | <span data-ttu-id="c7574-295">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-295">Is Required</span></span> | <span data-ttu-id="c7574-296">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="c7574-297">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="c7574-297">**Role**</span></span>       | <span data-ttu-id="c7574-298">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-298">Yes</span></span>         | <span data-ttu-id="c7574-299">Nome del tipo di entità in un'entità finale dipendente dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-300">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="c7574-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="c7574-301">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-302">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-303">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-303">Example</span></span>

<span data-ttu-id="c7574-304">Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** usato come parte della definizione dell'associazione **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="c7574-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="c7574-305">La proprietà **publisherID** del tipo di entità **book** costituisce l'entità finale dipendente del vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="c7574-306">Elemento Documentation (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="c7574-307">L'elemento **Documentation** in Conceptual Schema Definition Language (CSDL) può essere utilizzato per fornire informazioni su un oggetto definito in un elemento padre.</span><span class="sxs-lookup"><span data-stu-id="c7574-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="c7574-308">In un file con estensione edmx, quando l'elemento **Documentation** è un elemento figlio di un elemento che viene visualizzato come oggetto nell'area di progettazione di Entity Framework Designer, ad esempio un'entità, un'associazione o una proprietà, il contenuto dell'elemento **Documentation** verrà visualizzato nella finestra **proprietà** di Visual Studio per l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="c7574-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="c7574-309">L'elemento **Documentation** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-310">**Riepilogo**: breve descrizione dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="c7574-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="c7574-311">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="c7574-311">(zero or one element)</span></span>
-   <span data-ttu-id="c7574-312">**LongDescription**: Descrizione completa dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="c7574-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="c7574-313">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="c7574-313">(zero or one element)</span></span>
-   <span data-ttu-id="c7574-314">Elementi Annotation.</span><span class="sxs-lookup"><span data-stu-id="c7574-314">Annotation elements.</span></span> <span data-ttu-id="c7574-315">Zero o più elementi.</span><span class="sxs-lookup"><span data-stu-id="c7574-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-316">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-316">Applicable Attributes</span></span>

<span data-ttu-id="c7574-317">All'elemento **Documentation** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="c7574-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="c7574-318">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-319">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-320">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-320">Example</span></span>

<span data-ttu-id="c7574-321">Nell'esempio seguente viene illustrato l'elemento **Documentation** come elemento figlio di un elemento EntityType.</span><span class="sxs-lookup"><span data-stu-id="c7574-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="c7574-322">Se il frammento di codice seguente si trova nel contenuto CSDL di un file con estensione edmx, il contenuto degli elementi **Summary** e **LongDescription** verrà visualizzato nella finestra **proprietà** di Visual Studio quando si fa clic sul `Customer` tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="c7574-323">Elemento End (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-323">End Element (CSDL)</span></span>

<span data-ttu-id="c7574-324">L'elemento **end** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento Association o dell'elemento associationname.</span><span class="sxs-lookup"><span data-stu-id="c7574-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="c7574-325">In ogni caso, il ruolo dell'elemento **end** è diverso e gli attributi applicabili sono diversi.</span><span class="sxs-lookup"><span data-stu-id="c7574-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="c7574-326">Elemento End come figlio dell'elemento Association</span><span class="sxs-lookup"><span data-stu-id="c7574-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="c7574-327">Un elemento **end** (come figlio dell'elemento **Association** ) identifica il tipo di entità in un'entità finale di un'associazione e il numero di istanze del tipo di entità che possono esistere a tale estremità di un'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="c7574-328">Le entità finali dell'associazione sono definite come parte di un'associazione; un'associazione deve disporre esattamente di due entità finali.</span><span class="sxs-lookup"><span data-stu-id="c7574-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="c7574-329">Le istanze del tipo di entità in corrispondenza di un'entità finale di un'associazione sono accessibili attraverso proprietà di navigazione o chiavi esterne se sono esposte in un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="c7574-330">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-331">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-332">OnDelete (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="c7574-333">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="c7574-334">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-334">Applicable Attributes</span></span>

<span data-ttu-id="c7574-335">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **finale** quando è figlio di un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="c7574-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="c7574-336">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-336">Attribute Name</span></span>   | <span data-ttu-id="c7574-337">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-337">Is Required</span></span> | <span data-ttu-id="c7574-338">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-339">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-339">**Type**</span></span>         | <span data-ttu-id="c7574-340">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-340">Yes</span></span>         | <span data-ttu-id="c7574-341">Nome del tipo di entità in una entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="c7574-342">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="c7574-342">**Role**</span></span>         | <span data-ttu-id="c7574-343">No</span><span class="sxs-lookup"><span data-stu-id="c7574-343">No</span></span>          | <span data-ttu-id="c7574-344">Nome per l'entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-344">A name for the association end.</span></span> <span data-ttu-id="c7574-345">Se non è fornito alcun nome, verrà utilizzato il nome del tipo di entità nell'entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="c7574-346">**Molteplicità**</span><span class="sxs-lookup"><span data-stu-id="c7574-346">**Multiplicity**</span></span> | <span data-ttu-id="c7574-347">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-347">Yes</span></span>         | <span data-ttu-id="c7574-348">**1**, **0.. 1**o a **\*** seconda del numero di istanze del tipo di entità che possono trovarsi alla fine dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="c7574-349">**1** indica che nell'entità finale dell'associazione esiste esattamente un'istanza del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="c7574-350">**0.. 1** indica che nell'entità finale dell'associazione sono presenti zero o un'istanza del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="c7574-351">**\*** indica che nell'entità finale dell'associazione sono presenti zero, una o più istanze del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-352">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="c7574-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="c7574-353">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-354">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="c7574-355">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-355">Example</span></span>

<span data-ttu-id="c7574-356">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="c7574-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="c7574-357">I valori di **molteplicità** per ogni **estremità** dell'associazione indicano che molti **ordini** possono essere associati a un **cliente**, ma solo un **cliente** può essere associato a un **ordine**.</span><span class="sxs-lookup"><span data-stu-id="c7574-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="c7574-358">Inoltre, l'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati se il **cliente** viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="c7574-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="c7574-359">Elemento End come figlio dell'elemento AssociationSet</span><span class="sxs-lookup"><span data-stu-id="c7574-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="c7574-360">L'elemento **end** specifica un'estremità di un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="c7574-361">L'elemento **associationname** deve contenere due elementi **end** .</span><span class="sxs-lookup"><span data-stu-id="c7574-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="c7574-362">Le informazioni contenute in un elemento **end** vengono utilizzate per eseguire il mapping di un set di associazioni a un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="c7574-363">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-364">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-365">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-366">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="c7574-367">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c7574-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="c7574-368">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-368">Applicable Attributes</span></span>

<span data-ttu-id="c7574-369">Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **finale** quando è figlio di un elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="c7574-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="c7574-370">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-370">Attribute Name</span></span> | <span data-ttu-id="c7574-371">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-371">Is Required</span></span> | <span data-ttu-id="c7574-372">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="c7574-373">**EntitySet**</span></span>  | <span data-ttu-id="c7574-374">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-374">Yes</span></span>         | <span data-ttu-id="c7574-375">Nome dell'elemento **EntitySet** che definisce un'entità finale dell'elemento di **associazione** padre.</span><span class="sxs-lookup"><span data-stu-id="c7574-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="c7574-376">L'elemento **EntitySet** deve essere definito nello stesso contenitore di entità dell'elemento di **associazione** padre.</span><span class="sxs-lookup"><span data-stu-id="c7574-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="c7574-377">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="c7574-377">**Role**</span></span>       | <span data-ttu-id="c7574-378">No</span><span class="sxs-lookup"><span data-stu-id="c7574-378">No</span></span>          | <span data-ttu-id="c7574-379">Nome dell'entità finale del set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-379">The name of the association set end.</span></span> <span data-ttu-id="c7574-380">Se non si utilizza l'attributo **Role** , il nome dell'entità finale del set di associazioni sarà il nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="c7574-381">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="c7574-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="c7574-382">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-383">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="c7574-384">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-384">Example</span></span>

<span data-ttu-id="c7574-385">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con due elementi di **associazione** , ognuno con due elementi **end** :</span><span class="sxs-lookup"><span data-stu-id="c7574-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="c7574-386">Elemento EntityContainer (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="c7574-387">L'elemento **EntityContainer** in Conceptual Schema Definition Language (CSDL) è un contenitore logico per set di entità, set di associazioni e importazioni di funzioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="c7574-388">Un contenitore di entità del modello concettuale esegue il mapping a un contenitore di entità del modello di archiviazione attraverso l'elemento EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="c7574-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="c7574-389">Un contenitore di entità del modello di archiviazione descrive la struttura del database: i set di entità descrivono le tabelle, i set di associazioni descrivono i vincoli delle chiavi esterne e le importazioni di funzioni descrivono le stored procedure in un database.</span><span class="sxs-lookup"><span data-stu-id="c7574-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="c7574-390">Un elemento **EntityContainer** può avere uno o più elementi di documentazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="c7574-391">Se è presente un elemento di **documentazione** , deve precedere tutti gli elementi **EntitySet**, **associationname**e **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="c7574-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="c7574-392">Un elemento **EntityContainer** può avere zero o più degli elementi figlio seguenti (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="c7574-393">EntitySet</span></span>
-   <span data-ttu-id="c7574-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="c7574-394">AssociationSet</span></span>
-   <span data-ttu-id="c7574-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="c7574-395">FunctionImport</span></span>
-   <span data-ttu-id="c7574-396">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="c7574-396">Annotation elements</span></span>

<span data-ttu-id="c7574-397">È possibile estendere un elemento **EntityContainer** per includere il contenuto di un altro oggetto **EntityContainer** che si trova all'interno dello stesso spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c7574-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="c7574-398">Per includere il contenuto di un altro **EntityContainer**, nell'elemento **EntityContainer** di riferimento, impostare il valore dell'attributo **extends** sul nome dell'elemento **EntityContainer** che si desidera includere.</span><span class="sxs-lookup"><span data-stu-id="c7574-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="c7574-399">Tutti gli elementi figlio dell'elemento **EntityContainer** incluso verranno considerati come elementi figlio dell'elemento **EntityContainer** di riferimento.</span><span class="sxs-lookup"><span data-stu-id="c7574-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-400">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-400">Applicable Attributes</span></span>

<span data-ttu-id="c7574-401">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="c7574-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="c7574-402">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-402">Attribute Name</span></span> | <span data-ttu-id="c7574-403">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-403">Is Required</span></span> | <span data-ttu-id="c7574-404">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="c7574-405">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-405">**Name**</span></span>       | <span data-ttu-id="c7574-406">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-406">Yes</span></span>         | <span data-ttu-id="c7574-407">Nome del contenitore di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="c7574-408">**Estende**</span><span class="sxs-lookup"><span data-stu-id="c7574-408">**Extends**</span></span>    | <span data-ttu-id="c7574-409">No</span><span class="sxs-lookup"><span data-stu-id="c7574-409">No</span></span>          | <span data-ttu-id="c7574-410">Nome di un altro contenitore di entità all'interno dello stesso spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c7574-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-411">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="c7574-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="c7574-412">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-413">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-414">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-414">Example</span></span>

<span data-ttu-id="c7574-415">Nell'esempio seguente viene illustrato un elemento **EntityContainer** che definisce tre set di entità e due set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="c7574-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="c7574-416">Elemento EntitySet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="c7574-417">L'elemento **EntitySet** in Conceptual Schema Definition Language è un contenitore logico per le istanze di un tipo di entità e le istanze di qualsiasi tipo derivato da tale tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="c7574-418">La relazione tra un tipo di entità e un set di entità è analoga alla relazione tra una riga e una tabella in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="c7574-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="c7574-419">Analogamente a una riga, un tipo di entità definisce un set di dati correlati e, analogamente a una tabella, un set di entità contiene istanze di quella definizione.</span><span class="sxs-lookup"><span data-stu-id="c7574-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="c7574-420">Un set di entità fornisce un construct per il raggruppamento di istanze del tipo di entità in modo che se ne possa eseguire il mapping alle strutture dei dati correlati in un'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="c7574-421">È possibile definire più set di entità per un particolare tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-422">EF designer non supporta i modelli concettuali che contengono Multiple Entity Sets per Type.</span><span class="sxs-lookup"><span data-stu-id="c7574-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="c7574-423">L'elemento **EntitySet** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-424">Elemento Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="c7574-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="c7574-425">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-426">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-426">Applicable Attributes</span></span>

<span data-ttu-id="c7574-427">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="c7574-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="c7574-428">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-428">Attribute Name</span></span> | <span data-ttu-id="c7574-429">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-429">Is Required</span></span> | <span data-ttu-id="c7574-430">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-431">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-431">**Name**</span></span>       | <span data-ttu-id="c7574-432">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-432">Yes</span></span>         | <span data-ttu-id="c7574-433">Nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="c7574-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="c7574-434">**EntityType**</span></span> | <span data-ttu-id="c7574-435">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-435">Yes</span></span>         | <span data-ttu-id="c7574-436">Nome completo del tipo di entità per il quale il set di entità contiene delle istanze.</span><span class="sxs-lookup"><span data-stu-id="c7574-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-437">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="c7574-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="c7574-438">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-439">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-440">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-440">Example</span></span>

<span data-ttu-id="c7574-441">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con tre elementi **EntitySet** :</span><span class="sxs-lookup"><span data-stu-id="c7574-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="c7574-442">È possibile definire più set di entità per tipo (MEST).</span><span class="sxs-lookup"><span data-stu-id="c7574-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="c7574-443">Nell'esempio seguente viene definito un contenitore di entità con due set di entità per il tipo di entità **book** :</span><span class="sxs-lookup"><span data-stu-id="c7574-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="c7574-444">Elemento EntityType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="c7574-445">L'elemento **EntityType** rappresenta la struttura di un concetto di primo livello, ad esempio un cliente o un ordine, in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="c7574-446">Un tipo di entità è un modello per istanze di tipi di entità in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="c7574-447">Ogni modello contiene le informazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="c7574-448">Un nome univoco.</span><span class="sxs-lookup"><span data-stu-id="c7574-448">A unique name.</span></span> <span data-ttu-id="c7574-449">Obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="c7574-449">(Required.)</span></span>
-   <span data-ttu-id="c7574-450">Una chiave di entità che è definita da una o più proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="c7574-451">Obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="c7574-451">(Required.)</span></span>
-   <span data-ttu-id="c7574-452">Proprietà per contenere dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-452">Properties for containing data.</span></span> <span data-ttu-id="c7574-453">Facoltativo.</span><span class="sxs-lookup"><span data-stu-id="c7574-453">(Optional.)</span></span>
-   <span data-ttu-id="c7574-454">Proprietà di navigazione che consentono di navigare da un'entità finale di un'associazione all'altra.</span><span class="sxs-lookup"><span data-stu-id="c7574-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="c7574-455">Facoltativo.</span><span class="sxs-lookup"><span data-stu-id="c7574-455">(Optional.)</span></span>

<span data-ttu-id="c7574-456">In un'applicazione, un'istanza di un tipo di entità rappresenta un oggetto specifico, quale ad esempio un cliente o un ordine specifico.</span><span class="sxs-lookup"><span data-stu-id="c7574-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="c7574-457">Ogni istanza di un tipo di entità deve avere una chiave di entità univoca all'interno di un set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="c7574-458">Due istanze di tipi di entità sono considerate uguali solo se sono dello stesso tipo e se i valori delle rispettive chiavi di entità sono uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="c7574-459">Un elemento **EntityType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-460">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-461">Key (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="c7574-462">Property (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="c7574-463">NavigationProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="c7574-464">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-465">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-465">Applicable Attributes</span></span>

<span data-ttu-id="c7574-466">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="c7574-467">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="c7574-468">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-468">Is Required</span></span> | <span data-ttu-id="c7574-469">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-470">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="c7574-471">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-471">Yes</span></span>         | <span data-ttu-id="c7574-472">Nome del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="c7574-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="c7574-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="c7574-474">No</span><span class="sxs-lookup"><span data-stu-id="c7574-474">No</span></span>          | <span data-ttu-id="c7574-475">Nome di un altro tipo di entità, che rappresenta il tipo di base del tipo di entità in corso di definizione.</span><span class="sxs-lookup"><span data-stu-id="c7574-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="c7574-476">**Astratta**</span><span class="sxs-lookup"><span data-stu-id="c7574-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="c7574-477">No</span><span class="sxs-lookup"><span data-stu-id="c7574-477">No</span></span>          | <span data-ttu-id="c7574-478">**True** o **false**, a seconda che il tipo di entità sia un tipo astratto.</span><span class="sxs-lookup"><span data-stu-id="c7574-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="c7574-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="c7574-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="c7574-480">No</span><span class="sxs-lookup"><span data-stu-id="c7574-480">No</span></span>          | <span data-ttu-id="c7574-481">**True** o **false** a seconda che il tipo di entità sia un tipo di entità aperto.</span><span class="sxs-lookup"><span data-stu-id="c7574-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="c7574-482">> l'attributo **OpenType** è applicabile solo ai tipi di entità definiti nei modelli concettuali utilizzati con ADO.NET Data Services.</span><span class="sxs-lookup"><span data-stu-id="c7574-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="c7574-483">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="c7574-484">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-485">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-486">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-486">Example</span></span>

<span data-ttu-id="c7574-487">Nell'esempio seguente viene illustrato un elemento **EntityType** con tre elementi **Property** e due elementi **NavigationProperty** :</span><span class="sxs-lookup"><span data-stu-id="c7574-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="c7574-488">Elemento EnumType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="c7574-489">L'elemento **enumType** rappresenta un tipo enumerato.</span><span class="sxs-lookup"><span data-stu-id="c7574-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="c7574-490">Un elemento **enumType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-491">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-492">Member (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="c7574-493">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-494">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-494">Applicable Attributes</span></span>

<span data-ttu-id="c7574-495">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **enumType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="c7574-496">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-496">Attribute Name</span></span>     | <span data-ttu-id="c7574-497">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-497">Is Required</span></span> | <span data-ttu-id="c7574-498">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-499">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-499">**Name**</span></span>           | <span data-ttu-id="c7574-500">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-500">Yes</span></span>         | <span data-ttu-id="c7574-501">Nome del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="c7574-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="c7574-502">**IsFlags**</span></span>        | <span data-ttu-id="c7574-503">No</span><span class="sxs-lookup"><span data-stu-id="c7574-503">No</span></span>          | <span data-ttu-id="c7574-504">**True** o **false**, a seconda che il tipo enum possa essere utilizzato come set di flag.</span><span class="sxs-lookup"><span data-stu-id="c7574-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="c7574-505">Il valore predefinito è **false.**</span><span class="sxs-lookup"><span data-stu-id="c7574-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="c7574-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="c7574-506">**UnderlyingType**</span></span> | <span data-ttu-id="c7574-507">No</span><span class="sxs-lookup"><span data-stu-id="c7574-507">No</span></span>          | <span data-ttu-id="c7574-508">**EDM. byte**, **EDM. Int16**, **EDM. Int32**, **EDM. Int64** o **EDM. SByte** che definisce l'intervallo di valori del tipo.</span><span class="sxs-lookup"><span data-stu-id="c7574-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="c7574-509">Il tipo sottostante predefinito degli elementi di enumerazione è **EDM. Int32.**.</span><span class="sxs-lookup"><span data-stu-id="c7574-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-510">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **enumType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="c7574-511">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-512">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-513">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-513">Example</span></span>

<span data-ttu-id="c7574-514">Nell'esempio seguente viene illustrato un elemento **enumType** con tre elementi **member** :</span><span class="sxs-lookup"><span data-stu-id="c7574-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="c7574-515">Elemento Function (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-515">Function Element (CSDL)</span></span>

<span data-ttu-id="c7574-516">L'elemento **Function** in Conceptual Schema Definition Language (CSDL) viene utilizzato per definire o dichiarare funzioni nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="c7574-517">Un funzione viene definita utilizzando un elemento DefiningExpression.</span><span class="sxs-lookup"><span data-stu-id="c7574-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="c7574-518">Un elemento **Function** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-519">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-520">Parameter (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="c7574-521">DefiningExpression (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="c7574-522">ReturnType (funzione) (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="c7574-523">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="c7574-524">Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** (Function) o l'attributo **returnType** (vedere di seguito), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="c7574-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="c7574-525">I tipi restituiti possibili sono il edmSimpleType, il tipo di entità, il tipo complesso, il tipo di riga o il tipo di riferimento o una raccolta di uno di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="c7574-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-526">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-526">Applicable Attributes</span></span>

<span data-ttu-id="c7574-527">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Function** .</span><span class="sxs-lookup"><span data-stu-id="c7574-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="c7574-528">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-528">Attribute Name</span></span> | <span data-ttu-id="c7574-529">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-529">Is Required</span></span> | <span data-ttu-id="c7574-530">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="c7574-531">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-531">**Name**</span></span>       | <span data-ttu-id="c7574-532">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-532">Yes</span></span>         | <span data-ttu-id="c7574-533">Nome della funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-533">The name of the function.</span></span>          |
| <span data-ttu-id="c7574-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="c7574-534">**ReturnType**</span></span> | <span data-ttu-id="c7574-535">No</span><span class="sxs-lookup"><span data-stu-id="c7574-535">No</span></span>          | <span data-ttu-id="c7574-536">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-537">All'elemento **Function** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="c7574-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="c7574-538">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-539">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-540">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-540">Example</span></span>

<span data-ttu-id="c7574-541">Nell'esempio seguente viene utilizzato un elemento **Function** per definire una funzione che restituisce il numero di anni dall'assunzione di un insegnante.</span><span class="sxs-lookup"><span data-stu-id="c7574-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="c7574-542">Elemento FunctionImport (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="c7574-543">L'elemento **FunctionImport** in Conceptual Schema Definition Language (CSDL) rappresenta una funzione definita nell'origine dati, ma disponibile per gli oggetti tramite il modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="c7574-544">Ad esempio, è possibile utilizzare un elemento Function nel modello di archiviazione per rappresentare una stored procedure in un database.</span><span class="sxs-lookup"><span data-stu-id="c7574-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="c7574-545">Un elemento **FunctionImport** nel modello concettuale rappresenta la funzione corrispondente in un'applicazione Entity Framework e viene mappato alla funzione del modello di archiviazione utilizzando l'elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="c7574-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="c7574-546">Quando la funzione viene chiamata nell'applicazione, la stored procedure corrispondente viene eseguita nel database.</span><span class="sxs-lookup"><span data-stu-id="c7574-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="c7574-547">L'elemento **FunctionImport** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-548">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="c7574-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="c7574-549">Parameter (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="c7574-550">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="c7574-551">ReturnType (FunctionImport) (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="c7574-552">È necessario definire un elemento **Parameter** per ogni parametro accettato dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="c7574-553">Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** (FunctionImport) o l'attributo **returnType** (vedere di seguito), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="c7574-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="c7574-554">Il valore del tipo restituito deve essere una raccolta di EdmSimpleType, EntityType o ComplexType.</span><span class="sxs-lookup"><span data-stu-id="c7574-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-555">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-555">Applicable Attributes</span></span>

<span data-ttu-id="c7574-556">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="c7574-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="c7574-557">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-557">Attribute Name</span></span>   | <span data-ttu-id="c7574-558">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-558">Is Required</span></span> | <span data-ttu-id="c7574-559">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-560">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-560">**Name**</span></span>         | <span data-ttu-id="c7574-561">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-561">Yes</span></span>         | <span data-ttu-id="c7574-562">Nome della funzione importata.</span><span class="sxs-lookup"><span data-stu-id="c7574-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="c7574-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="c7574-563">**ReturnType**</span></span>   | <span data-ttu-id="c7574-564">No</span><span class="sxs-lookup"><span data-stu-id="c7574-564">No</span></span>          | <span data-ttu-id="c7574-565">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-565">The type that the function returns.</span></span> <span data-ttu-id="c7574-566">Non utilizzare questo attributo se la funzione non restituisce un valore.</span><span class="sxs-lookup"><span data-stu-id="c7574-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="c7574-567">In caso contrario, il valore deve essere una raccolta di ComplexType, EntityType o EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="c7574-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="c7574-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="c7574-568">**EntitySet**</span></span>    | <span data-ttu-id="c7574-569">No</span><span class="sxs-lookup"><span data-stu-id="c7574-569">No</span></span>          | <span data-ttu-id="c7574-570">Se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve essere il set di entità a cui appartiene la raccolta.</span><span class="sxs-lookup"><span data-stu-id="c7574-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="c7574-571">In caso contrario, l'attributo **EntitySet** non deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="c7574-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="c7574-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="c7574-572">**IsComposable**</span></span> | <span data-ttu-id="c7574-573">No</span><span class="sxs-lookup"><span data-stu-id="c7574-573">No</span></span>          | <span data-ttu-id="c7574-574">Se il valore è impostato su true, la funzione è componibile (funzione con valori di tabella) e può essere utilizzata in una query LINQ.</span><span class="sxs-lookup"><span data-stu-id="c7574-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="c7574-575">Il valore predefinito è **false**.</span><span class="sxs-lookup"><span data-stu-id="c7574-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="c7574-576">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="c7574-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="c7574-577">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-578">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-579">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-579">Example</span></span>

<span data-ttu-id="c7574-580">Nell'esempio seguente viene illustrato un elemento **FunctionImport** che accetta un parametro e restituisce una raccolta di tipi di entità:</span><span class="sxs-lookup"><span data-stu-id="c7574-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="c7574-581">Elemento Key (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-581">Key Element (CSDL)</span></span>

<span data-ttu-id="c7574-582">L'elemento **Key** è un elemento figlio dell'elemento EntityType e definisce una *chiave di entità* , ovvero una proprietà o un set di proprietà di un tipo di entità che determinano l'identità.</span><span class="sxs-lookup"><span data-stu-id="c7574-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="c7574-583">Le proprietà che costituiscono una chiave di entità vengono scelte in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="c7574-584">I valori delle proprietà della chiave di entità devono identificare in modo univoco in fase di esecuzione un'istanza del tipo di entità all'interno di un set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="c7574-585">Le proprietà che costituiscono una chiave di entità devono essere scelte per garantire univocità delle istanze in un set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="c7574-586">L'elemento **Key** definisce una chiave di entità facendo riferimento a una o più proprietà di un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="c7574-587">L'elemento **chiave** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="c7574-588">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="c7574-589">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-590">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-590">Applicable Attributes</span></span>

<span data-ttu-id="c7574-591">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **chiave** .</span><span class="sxs-lookup"><span data-stu-id="c7574-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="c7574-592">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-593">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-594">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-594">Example</span></span>

<span data-ttu-id="c7574-595">Nell'esempio seguente viene definito un tipo di entità denominato **book**.</span><span class="sxs-lookup"><span data-stu-id="c7574-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="c7574-596">La chiave di entità viene definita facendo riferimento alla proprietà **ISBN** del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="c7574-597">La proprietà **ISBN** è una scelta ottimale per la chiave di entità perché un numero di libro standard internazionale (ISBN) identifica in modo univoco un libro.</span><span class="sxs-lookup"><span data-stu-id="c7574-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="c7574-598">Nell'esempio seguente viene illustrato un tipo di entità (**autore**) che dispone di una chiave di entità costituita da due proprietà, **Name** e **Address**.</span><span class="sxs-lookup"><span data-stu-id="c7574-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="c7574-599">L'uso del **nome** e dell' **Indirizzo** per la chiave di entità è una scelta ragionevole, perché due autori con lo stesso nome non possono vivere allo stesso indirizzo.</span><span class="sxs-lookup"><span data-stu-id="c7574-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="c7574-600">Questa scelta per una chiave di entità non garantisce tuttavia in modo assoluto chiavi di entità univoche in un set di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="c7574-601">In questo caso, è consigliabile aggiungere una proprietà, ad esempio **autorizzazione**, che può essere usata per identificare in modo univoco un autore.</span><span class="sxs-lookup"><span data-stu-id="c7574-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="c7574-602">Elemento member (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-602">Member Element (CSDL)</span></span>

<span data-ttu-id="c7574-603">L'elemento **member** è un elemento figlio dell'elemento enumType e definisce un membro del tipo enumerato.</span><span class="sxs-lookup"><span data-stu-id="c7574-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-604">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-604">Applicable Attributes</span></span>

<span data-ttu-id="c7574-605">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="c7574-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="c7574-606">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-606">Attribute Name</span></span> | <span data-ttu-id="c7574-607">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-607">Is Required</span></span> | <span data-ttu-id="c7574-608">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-609">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-609">**Name**</span></span>       | <span data-ttu-id="c7574-610">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-610">Yes</span></span>         | <span data-ttu-id="c7574-611">Nome del membro.</span><span class="sxs-lookup"><span data-stu-id="c7574-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="c7574-612">**Valore**</span><span class="sxs-lookup"><span data-stu-id="c7574-612">**Value**</span></span>      | <span data-ttu-id="c7574-613">No</span><span class="sxs-lookup"><span data-stu-id="c7574-613">No</span></span>          | <span data-ttu-id="c7574-614">Valore del membro.</span><span class="sxs-lookup"><span data-stu-id="c7574-614">The value of the member.</span></span> <span data-ttu-id="c7574-615">Per impostazione predefinita, il primo membro ha il valore 0 e il valore di ogni enumeratore successivo viene incrementato di 1.</span><span class="sxs-lookup"><span data-stu-id="c7574-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="c7574-616">Potrebbero esistere più membri con gli stessi valori.</span><span class="sxs-lookup"><span data-stu-id="c7574-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-617">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="c7574-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="c7574-618">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-619">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-620">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-620">Example</span></span>

<span data-ttu-id="c7574-621">Nell'esempio seguente viene illustrato un elemento **enumType** con tre elementi **member** :</span><span class="sxs-lookup"><span data-stu-id="c7574-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="c7574-622">Elemento NavigationProperty (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="c7574-623">Un elemento **NavigationProperty** definisce una proprietà di navigazione che fornisce un riferimento all'altra entità finale di un'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="c7574-624">A differenza delle proprietà definite con l'elemento Property, le proprietà di navigazione non definiscono la forma e le caratteristiche dei dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="c7574-625">Forniscono un modo per navigare in un'associazione tra due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="c7574-626">Si noti che le proprietà di navigazione sono facoltative in entrambi tipi di entità nelle entità finali di un'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="c7574-627">Se si definisce una proprietà di navigazione su un tipo di entità nell'entità finale di un'associazione, non è necessario definire una proprietà di navigazione sul tipo di entità nell'altra entità finale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="c7574-628">Il tipo di dati restituito da una proprietà di navigazione è determinato dalla molteplicità della relativa entità finale di associazione remota.</span><span class="sxs-lookup"><span data-stu-id="c7574-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="c7574-629">Si supponga, ad esempio, che una proprietà di navigazione, **OrdersNavProp**, esista in un tipo di entità **Customer** e si sposta in un'associazione uno-a-molti tra **Customer** e **Order**.</span><span class="sxs-lookup"><span data-stu-id="c7574-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="c7574-630">Poiché l'entità finale dell'associazione remota per la proprietà di navigazione dispone di molteplicità many ( \* ), il relativo tipo di dati è una raccolta (di **Order**).</span><span class="sxs-lookup"><span data-stu-id="c7574-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="c7574-631">Analogamente, se esiste una proprietà di navigazione, **CustomerNavProp nel**, nel tipo di entità **Order** , il relativo tipo di dati sarà **Customer** poiché la molteplicità di end remoto è uno (1).</span><span class="sxs-lookup"><span data-stu-id="c7574-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="c7574-632">Un elemento **NavigationProperty** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-633">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-634">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-635">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-635">Applicable Attributes</span></span>

<span data-ttu-id="c7574-636">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="c7574-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="c7574-637">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-637">Attribute Name</span></span>   | <span data-ttu-id="c7574-638">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-638">Is Required</span></span> | <span data-ttu-id="c7574-639">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-640">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-640">**Name**</span></span>         | <span data-ttu-id="c7574-641">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-641">Yes</span></span>         | <span data-ttu-id="c7574-642">Nome della proprietà di navigazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="c7574-643">**Relationship**</span><span class="sxs-lookup"><span data-stu-id="c7574-643">**Relationship**</span></span> | <span data-ttu-id="c7574-644">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-644">Yes</span></span>         | <span data-ttu-id="c7574-645">Nome di un'associazione che è all'interno dell'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="c7574-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="c7574-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="c7574-646">**ToRole**</span></span>       | <span data-ttu-id="c7574-647">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-647">Yes</span></span>         | <span data-ttu-id="c7574-648">Entità finale dell'associazione in corrispondenza della quale termina la navigazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="c7574-649">Il valore dell'attributo **ToRole** deve corrispondere al valore di uno degli attributi **Role** definiti in una delle entità finali dell'associazione (definite nell'elemento AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="c7574-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="c7574-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="c7574-650">**FromRole**</span></span>     | <span data-ttu-id="c7574-651">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-651">Yes</span></span>         | <span data-ttu-id="c7574-652">Entità finale dell'associazione dalla quale ha inizio la navigazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="c7574-653">Il valore dell'attributo **FromRole** deve corrispondere al valore di uno degli attributi **Role** definiti in una delle entità finali dell'associazione (definite nell'elemento AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="c7574-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-654">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="c7574-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="c7574-655">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-656">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-657">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-657">Example</span></span>

<span data-ttu-id="c7574-658">Nell'esempio seguente viene definito un tipo di entità (**book**) con due proprietà di navigazione (**PublishedBy** e **WrittenBy**):</span><span class="sxs-lookup"><span data-stu-id="c7574-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="c7574-659">Elemento OnDelete (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="c7574-660">L'elemento **OnDelete** in Conceptual Schema Definition Language (CSDL) definisce il comportamento connesso a un'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="c7574-661">Se l'attributo **Action** è impostato su **Cascade** in un'entità finale di un'associazione, i tipi di entità correlati sull'altra entità finale dell'associazione vengono eliminati quando viene eliminato il tipo di entità alla prima estremità.</span><span class="sxs-lookup"><span data-stu-id="c7574-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="c7574-662">Se l'associazione tra due tipi di entità è una relazione tra chiave primaria e chiave primaria, un oggetto dipendente caricato viene eliminato quando l'oggetto Principal sull'altra entità finale dell'associazione viene eliminato indipendentemente dalla specifica **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="c7574-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="c7574-663">L'elemento **OnDelete** influisca solo sul comportamento di runtime di un'applicazione. non influisce sul comportamento nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="c7574-664">Il comportamento definito nell'origine dati deve essere uguale al comportamento definito nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="c7574-665">Un elemento **OnDelete** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-666">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-667">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-668">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-668">Applicable Attributes</span></span>

<span data-ttu-id="c7574-669">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="c7574-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="c7574-670">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-670">Attribute Name</span></span> | <span data-ttu-id="c7574-671">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-671">Is Required</span></span> | <span data-ttu-id="c7574-672">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-673">**Azione**</span><span class="sxs-lookup"><span data-stu-id="c7574-673">**Action**</span></span>     | <span data-ttu-id="c7574-674">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-674">Yes</span></span>         | <span data-ttu-id="c7574-675">**Cascade** o **None**.</span><span class="sxs-lookup"><span data-stu-id="c7574-675">**Cascade** or **None**.</span></span> <span data-ttu-id="c7574-676">Se **Cascade**, i tipi di entità dipendenti verranno eliminati quando il tipo di entità principale viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="c7574-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="c7574-677">Se **None**, i tipi di entità dipendenti non verranno eliminati quando il tipo di entità principale viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="c7574-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-678">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="c7574-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="c7574-679">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-680">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-681">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-681">Example</span></span>

<span data-ttu-id="c7574-682">Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="c7574-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="c7574-683">L'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati quando il **cliente** viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="c7574-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="c7574-684">Elemento Parameter (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="c7574-685">L'elemento **Parameter** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento FunctionImport o dell'elemento Function.</span><span class="sxs-lookup"><span data-stu-id="c7574-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="c7574-686">Applicazione dell'elemento FunctionImport</span><span class="sxs-lookup"><span data-stu-id="c7574-686">FunctionImport Element Application</span></span>

<span data-ttu-id="c7574-687">Un elemento **Parameter** (come figlio dell'elemento **FunctionImport** ) viene usato per definire i parametri di input e output per le importazioni di funzioni dichiarate in CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="c7574-688">L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-689">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="c7574-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="c7574-690">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="c7574-691">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-691">Applicable Attributes</span></span>

<span data-ttu-id="c7574-692">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="c7574-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="c7574-693">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-693">Attribute Name</span></span> | <span data-ttu-id="c7574-694">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-694">Is Required</span></span> | <span data-ttu-id="c7574-695">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-696">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-696">**Name**</span></span>       | <span data-ttu-id="c7574-697">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-697">Yes</span></span>         | <span data-ttu-id="c7574-698">Nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="c7574-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="c7574-699">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-699">**Type**</span></span>       | <span data-ttu-id="c7574-700">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-700">Yes</span></span>         | <span data-ttu-id="c7574-701">Tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="c7574-701">The parameter type.</span></span> <span data-ttu-id="c7574-702">Il valore deve essere di tipo **EDMSimpleType** o di un tipo complesso compreso nell'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="c7574-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="c7574-703">**Modalità**</span><span class="sxs-lookup"><span data-stu-id="c7574-703">**Mode**</span></span>       | <span data-ttu-id="c7574-704">No</span><span class="sxs-lookup"><span data-stu-id="c7574-704">No</span></span>          | <span data-ttu-id="c7574-705">**In**, **out**o **InOut** a seconda che il parametro sia un parametro di input, di output o di input/output.</span><span class="sxs-lookup"><span data-stu-id="c7574-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="c7574-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-706">**MaxLength**</span></span>  | <span data-ttu-id="c7574-707">No</span><span class="sxs-lookup"><span data-stu-id="c7574-707">No</span></span>          | <span data-ttu-id="c7574-708">Lunghezza massima consentita per il parametro.</span><span class="sxs-lookup"><span data-stu-id="c7574-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="c7574-709">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-709">**Precision**</span></span>  | <span data-ttu-id="c7574-710">No</span><span class="sxs-lookup"><span data-stu-id="c7574-710">No</span></span>          | <span data-ttu-id="c7574-711">Precisione del parametro</span><span class="sxs-lookup"><span data-stu-id="c7574-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="c7574-712">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-712">**Scale**</span></span>      | <span data-ttu-id="c7574-713">No</span><span class="sxs-lookup"><span data-stu-id="c7574-713">No</span></span>          | <span data-ttu-id="c7574-714">Scalabilità del parametro</span><span class="sxs-lookup"><span data-stu-id="c7574-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="c7574-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-715">**SRID**</span></span>       | <span data-ttu-id="c7574-716">No</span><span class="sxs-lookup"><span data-stu-id="c7574-716">No</span></span>          | <span data-ttu-id="c7574-717">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="c7574-718">Valido solo per i parametri dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="c7574-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="c7574-719">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="c7574-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-720">All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="c7574-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="c7574-721">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-722">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="c7574-723">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-723">Example</span></span>

<span data-ttu-id="c7574-724">Nell'esempio seguente viene illustrato un elemento **FunctionImport** con un elemento figlio **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="c7574-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="c7574-725">La funzione accetta un parametro di input e restituisce una raccolta di tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="c7574-726">Applicazione dell'elemento Function</span><span class="sxs-lookup"><span data-stu-id="c7574-726">Function Element Application</span></span>

<span data-ttu-id="c7574-727">Un elemento **Parameter** (come figlio dell'elemento **Function** ) definisce i parametri per le funzioni definite o dichiarate in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="c7574-728">L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-729">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="c7574-730">CollectionType (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="c7574-731">ReferenceType (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="c7574-732">RowType (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-733">Solo uno degli elementi **CollectionType**, **ReferenceType**o **RowType** può essere un elemento figlio di un elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="c7574-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="c7574-734">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-735">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="c7574-736">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c7574-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="c7574-737">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-737">Applicable Attributes</span></span>

<span data-ttu-id="c7574-738">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="c7574-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="c7574-739">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-739">Attribute Name</span></span>   | <span data-ttu-id="c7574-740">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-740">Is Required</span></span> | <span data-ttu-id="c7574-741">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-742">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-742">**Name**</span></span>         | <span data-ttu-id="c7574-743">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-743">Yes</span></span>         | <span data-ttu-id="c7574-744">Nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="c7574-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="c7574-745">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-745">**Type**</span></span>         | <span data-ttu-id="c7574-746">No</span><span class="sxs-lookup"><span data-stu-id="c7574-746">No</span></span>          | <span data-ttu-id="c7574-747">Tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="c7574-747">The parameter type.</span></span> <span data-ttu-id="c7574-748">Un parametro può essere uno qualsiasi dei seguenti tipi (o raccolte di questi tipi):</span><span class="sxs-lookup"><span data-stu-id="c7574-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="c7574-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="c7574-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="c7574-750">tipo di entità</span><span class="sxs-lookup"><span data-stu-id="c7574-750">entity type</span></span> <br/> <span data-ttu-id="c7574-751">tipo complesso</span><span class="sxs-lookup"><span data-stu-id="c7574-751">complex type</span></span> <br/> <span data-ttu-id="c7574-752">tipo di riga</span><span class="sxs-lookup"><span data-stu-id="c7574-752">row type</span></span> <br/> <span data-ttu-id="c7574-753">tipo di riferimento</span><span class="sxs-lookup"><span data-stu-id="c7574-753">reference type</span></span>                             |
| <span data-ttu-id="c7574-754">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="c7574-754">**Nullable**</span></span>     | <span data-ttu-id="c7574-755">No</span><span class="sxs-lookup"><span data-stu-id="c7574-755">No</span></span>          | <span data-ttu-id="c7574-756">**True** (valore predefinito) o **false** a seconda che la proprietà possa avere un valore **null** .</span><span class="sxs-lookup"><span data-stu-id="c7574-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="c7574-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="c7574-757">**DefaultValue**</span></span> | <span data-ttu-id="c7574-758">No</span><span class="sxs-lookup"><span data-stu-id="c7574-758">No</span></span>          | <span data-ttu-id="c7574-759">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="c7574-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-760">**MaxLength**</span></span>    | <span data-ttu-id="c7574-761">No</span><span class="sxs-lookup"><span data-stu-id="c7574-761">No</span></span>          | <span data-ttu-id="c7574-762">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="c7574-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-763">**FixedLength**</span></span>  | <span data-ttu-id="c7574-764">No</span><span class="sxs-lookup"><span data-stu-id="c7574-764">No</span></span>          | <span data-ttu-id="c7574-765">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="c7574-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="c7574-766">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-766">**Precision**</span></span>    | <span data-ttu-id="c7574-767">No</span><span class="sxs-lookup"><span data-stu-id="c7574-767">No</span></span>          | <span data-ttu-id="c7574-768">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="c7574-769">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-769">**Scale**</span></span>        | <span data-ttu-id="c7574-770">No</span><span class="sxs-lookup"><span data-stu-id="c7574-770">No</span></span>          | <span data-ttu-id="c7574-771">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="c7574-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-772">**SRID**</span></span>         | <span data-ttu-id="c7574-773">No</span><span class="sxs-lookup"><span data-stu-id="c7574-773">No</span></span>          | <span data-ttu-id="c7574-774">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="c7574-775">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="c7574-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="c7574-776">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="c7574-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="c7574-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="c7574-777">**Unicode**</span></span>      | <span data-ttu-id="c7574-778">No</span><span class="sxs-lookup"><span data-stu-id="c7574-778">No</span></span>          | <span data-ttu-id="c7574-779">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="c7574-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="c7574-780">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="c7574-780">**Collation**</span></span>    | <span data-ttu-id="c7574-781">No</span><span class="sxs-lookup"><span data-stu-id="c7574-781">No</span></span>          | <span data-ttu-id="c7574-782">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="c7574-783">All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="c7574-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="c7574-784">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-785">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="c7574-786">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-786">Example</span></span>

<span data-ttu-id="c7574-787">Nell'esempio seguente viene illustrato un elemento **Function** che utilizza un elemento figlio **Parameter** per definire un parametro di funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="c7574-788">Elemento Principal (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="c7574-789">L'elemento **Principal** in Conceptual Schema Definition Language (CSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale principale di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="c7574-790">Un elemento **ReferentialConstraint** definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="c7574-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="c7574-791">Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="c7574-792">Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="c7574-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="c7574-793">Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="c7574-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="c7574-794">Gli elementi **PropertyRef** vengono utilizzati per specificare a quali chiavi viene fatto riferimento dall'entità finale dipendente.</span><span class="sxs-lookup"><span data-stu-id="c7574-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="c7574-795">L'elemento **Principal** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-796">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="c7574-797">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-798">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-798">Applicable Attributes</span></span>

<span data-ttu-id="c7574-799">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Principal** .</span><span class="sxs-lookup"><span data-stu-id="c7574-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="c7574-800">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-800">Attribute Name</span></span> | <span data-ttu-id="c7574-801">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-801">Is Required</span></span> | <span data-ttu-id="c7574-802">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="c7574-803">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="c7574-803">**Role**</span></span>       | <span data-ttu-id="c7574-804">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-804">Yes</span></span>         | <span data-ttu-id="c7574-805">Nome del tipo di entità in un'entità finale principale dell'associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-806">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **principale** .</span><span class="sxs-lookup"><span data-stu-id="c7574-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="c7574-807">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-808">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-809">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-809">Example</span></span>

<span data-ttu-id="c7574-810">Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** che fa parte della definizione dell'associazione **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="c7574-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="c7574-811">La proprietà **ID** del tipo di entità **Publisher** costituisce l'entità finale principale del vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="c7574-812">Elemento Property (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-812">Property Element (CSDL)</span></span>

<span data-ttu-id="c7574-813">L'elemento **Property** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento EntityType, dell'elemento complexType o dell'elemento RowType.</span><span class="sxs-lookup"><span data-stu-id="c7574-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="c7574-814">Applicazione degli elementi EntityType e ComplexType</span><span class="sxs-lookup"><span data-stu-id="c7574-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="c7574-815">Gli elementi **Property** (come elementi figlio degli elementi **EntityType** o **complexType** ) definiscono la forma e le caratteristiche dei dati che un'istanza del tipo di entità o di tipo complesso conterrà.</span><span class="sxs-lookup"><span data-stu-id="c7574-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="c7574-816">Le proprietà in un modello concettuale sono analoghe alle proprietà definite su una classe.</span><span class="sxs-lookup"><span data-stu-id="c7574-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="c7574-817">Nello stesso modo in cui le proprietà su una classe definiscono la forma della classe e forniscono informazioni su oggetti, le proprietà in un modello concettuale definiscono la forma di un tipo di entità e forniscono informazioni su istanze del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="c7574-818">L'elemento **Property** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-819">Elemento Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="c7574-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="c7574-820">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="c7574-821">A un elemento **Property** è possibile applicare i facet seguenti: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span><span class="sxs-lookup"><span data-stu-id="c7574-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="c7574-822">I facet sono attributi XML che forniscono informazioni sul modo in cui i valori di proprietà vengono archiviati nell'archivio dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-823">I facet possono essere applicati solo a proprietà di tipo **EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="c7574-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="c7574-824">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-824">Applicable Attributes</span></span>

<span data-ttu-id="c7574-825">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="c7574-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="c7574-826">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-826">Attribute Name</span></span>                                                         | <span data-ttu-id="c7574-827">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-827">Is Required</span></span> | <span data-ttu-id="c7574-828">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-829">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-829">**Name**</span></span>                                                               | <span data-ttu-id="c7574-830">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-830">Yes</span></span>         | <span data-ttu-id="c7574-831">Nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="c7574-832">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-832">**Type**</span></span>                                                               | <span data-ttu-id="c7574-833">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-833">Yes</span></span>         | <span data-ttu-id="c7574-834">Tipo del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-834">The type of the property value.</span></span> <span data-ttu-id="c7574-835">Il tipo del valore della proprietà deve corrispondere a **EDMSimpleType** o a un tipo complesso (indicato da un nome completo) nell'ambito del modello.</span><span class="sxs-lookup"><span data-stu-id="c7574-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="c7574-836">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="c7574-836">**Nullable**</span></span>                                                           | <span data-ttu-id="c7574-837">No</span><span class="sxs-lookup"><span data-stu-id="c7574-837">No</span></span>          | <span data-ttu-id="c7574-838">**True** (valore predefinito) o <strong>False</strong>, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="c7574-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="c7574-839">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="c7574-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="c7574-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="c7574-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="c7574-841">No</span><span class="sxs-lookup"><span data-stu-id="c7574-841">No</span></span>          | <span data-ttu-id="c7574-842">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="c7574-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="c7574-844">No</span><span class="sxs-lookup"><span data-stu-id="c7574-844">No</span></span>          | <span data-ttu-id="c7574-845">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="c7574-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="c7574-847">No</span><span class="sxs-lookup"><span data-stu-id="c7574-847">No</span></span>          | <span data-ttu-id="c7574-848">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="c7574-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="c7574-849">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-849">**Precision**</span></span>                                                          | <span data-ttu-id="c7574-850">No</span><span class="sxs-lookup"><span data-stu-id="c7574-850">No</span></span>          | <span data-ttu-id="c7574-851">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="c7574-852">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-852">**Scale**</span></span>                                                              | <span data-ttu-id="c7574-853">No</span><span class="sxs-lookup"><span data-stu-id="c7574-853">No</span></span>          | <span data-ttu-id="c7574-854">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="c7574-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-855">**SRID**</span></span>                                                               | <span data-ttu-id="c7574-856">No</span><span class="sxs-lookup"><span data-stu-id="c7574-856">No</span></span>          | <span data-ttu-id="c7574-857">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="c7574-858">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="c7574-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="c7574-859">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="c7574-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="c7574-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="c7574-860">**Unicode**</span></span>                                                            | <span data-ttu-id="c7574-861">No</span><span class="sxs-lookup"><span data-stu-id="c7574-861">No</span></span>          | <span data-ttu-id="c7574-862">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="c7574-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="c7574-863">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="c7574-863">**Collation**</span></span>                                                          | <span data-ttu-id="c7574-864">No</span><span class="sxs-lookup"><span data-stu-id="c7574-864">No</span></span>          | <span data-ttu-id="c7574-865">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="c7574-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="c7574-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="c7574-867">No</span><span class="sxs-lookup"><span data-stu-id="c7574-867">No</span></span>          | <span data-ttu-id="c7574-868">**None** (valore predefinito) o **Fixed**.</span><span class="sxs-lookup"><span data-stu-id="c7574-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="c7574-869">Se il valore è impostato su **Fixed**, il valore della proprietà verrà utilizzato nei controlli della concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="c7574-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="c7574-870">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="c7574-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="c7574-871">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-872">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="c7574-873">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-873">Example</span></span>

<span data-ttu-id="c7574-874">Nell'esempio seguente viene illustrato un elemento **EntityType** con tre elementi **Property** :</span><span class="sxs-lookup"><span data-stu-id="c7574-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="c7574-875">Nell'esempio seguente viene illustrato un elemento **complexType** con cinque elementi **Property** :</span><span class="sxs-lookup"><span data-stu-id="c7574-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="c7574-876">Applicazione dell'elemento RowType</span><span class="sxs-lookup"><span data-stu-id="c7574-876">RowType Element Application</span></span>

<span data-ttu-id="c7574-877">Gli elementi **Property** (come gli elementi figlio di un elemento **RowType** ) definiscono la forma e le caratteristiche dei dati che possono essere passati o restituiti da una funzione definita dal modello.</span><span class="sxs-lookup"><span data-stu-id="c7574-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="c7574-878">L'elemento **Property** può avere esattamente uno degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="c7574-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="c7574-879">CollectionType</span></span>
-   <span data-ttu-id="c7574-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="c7574-880">ReferenceType</span></span>
-   <span data-ttu-id="c7574-881">RowType</span><span class="sxs-lookup"><span data-stu-id="c7574-881">RowType</span></span>

<span data-ttu-id="c7574-882">L'elemento **Property** può includere qualsiasi numero di elementi Annotation figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-883">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c7574-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="c7574-884">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-884">Applicable Attributes</span></span>

<span data-ttu-id="c7574-885">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="c7574-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="c7574-886">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-886">Attribute Name</span></span>                                                     | <span data-ttu-id="c7574-887">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-887">Is Required</span></span> | <span data-ttu-id="c7574-888">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-889">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-889">**Name**</span></span>                                                           | <span data-ttu-id="c7574-890">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-890">Yes</span></span>         | <span data-ttu-id="c7574-891">Nome della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="c7574-892">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-892">**Type**</span></span>                                                           | <span data-ttu-id="c7574-893">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-893">Yes</span></span>         | <span data-ttu-id="c7574-894">Tipo del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="c7574-895">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="c7574-895">**Nullable**</span></span>                                                       | <span data-ttu-id="c7574-896">No</span><span class="sxs-lookup"><span data-stu-id="c7574-896">No</span></span>          | <span data-ttu-id="c7574-897">**True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="c7574-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="c7574-898">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="c7574-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="c7574-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="c7574-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="c7574-900">No</span><span class="sxs-lookup"><span data-stu-id="c7574-900">No</span></span>          | <span data-ttu-id="c7574-901">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="c7574-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="c7574-903">No</span><span class="sxs-lookup"><span data-stu-id="c7574-903">No</span></span>          | <span data-ttu-id="c7574-904">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="c7574-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="c7574-906">No</span><span class="sxs-lookup"><span data-stu-id="c7574-906">No</span></span>          | <span data-ttu-id="c7574-907">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="c7574-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="c7574-908">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-908">**Precision**</span></span>                                                      | <span data-ttu-id="c7574-909">No</span><span class="sxs-lookup"><span data-stu-id="c7574-909">No</span></span>          | <span data-ttu-id="c7574-910">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="c7574-911">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-911">**Scale**</span></span>                                                          | <span data-ttu-id="c7574-912">No</span><span class="sxs-lookup"><span data-stu-id="c7574-912">No</span></span>          | <span data-ttu-id="c7574-913">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="c7574-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-914">**SRID**</span></span>                                                           | <span data-ttu-id="c7574-915">No</span><span class="sxs-lookup"><span data-stu-id="c7574-915">No</span></span>          | <span data-ttu-id="c7574-916">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="c7574-917">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="c7574-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="c7574-918">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="c7574-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="c7574-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="c7574-919">**Unicode**</span></span>                                                        | <span data-ttu-id="c7574-920">No</span><span class="sxs-lookup"><span data-stu-id="c7574-920">No</span></span>          | <span data-ttu-id="c7574-921">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="c7574-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="c7574-922">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="c7574-922">**Collation**</span></span>                                                      | <span data-ttu-id="c7574-923">No</span><span class="sxs-lookup"><span data-stu-id="c7574-923">No</span></span>          | <span data-ttu-id="c7574-924">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="c7574-925">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="c7574-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="c7574-926">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-927">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="c7574-928">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-928">Example</span></span>

<span data-ttu-id="c7574-929">Nell'esempio seguente vengono illustrati gli elementi di **Proprietà** utilizzati per definire la forma del tipo restituito di una funzione definita dal modello.</span><span class="sxs-lookup"><span data-stu-id="c7574-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="c7574-930">Elemento PropertyRef (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="c7574-931">L'elemento **PropertyRef** in Conceptual Schema Definition Language (CSDL) fa riferimento a una proprietà di un tipo di entità per indicare che la proprietà eseguirà uno dei ruoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="c7574-932">Parte della chiave di entità (una proprietà o un set di proprietà di un tipo di entità che determinano l'identità).</span><span class="sxs-lookup"><span data-stu-id="c7574-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="c7574-933">Per definire una chiave di entità, è possibile usare uno o più elementi **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="c7574-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="c7574-934">L'entità finale dipendente o principale di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="c7574-935">L'elemento **PropertyRef** può contenere solo elementi Annotation (zero o più) come elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-936">Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c7574-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-937">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-937">Applicable Attributes</span></span>

<span data-ttu-id="c7574-938">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="c7574-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="c7574-939">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-939">Attribute Name</span></span> | <span data-ttu-id="c7574-940">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-940">Is Required</span></span> | <span data-ttu-id="c7574-941">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="c7574-942">**Nome**</span><span class="sxs-lookup"><span data-stu-id="c7574-942">**Name**</span></span>       | <span data-ttu-id="c7574-943">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-943">Yes</span></span>         | <span data-ttu-id="c7574-944">Nome della proprietà alla quale viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="c7574-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-945">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="c7574-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="c7574-946">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-947">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-948">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-948">Example</span></span>

<span data-ttu-id="c7574-949">Nell'esempio seguente viene definito un tipo di entità (**book**).</span><span class="sxs-lookup"><span data-stu-id="c7574-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="c7574-950">La chiave di entità viene definita facendo riferimento alla proprietà **ISBN** del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="c7574-951">Nell'esempio seguente vengono usati due elementi **PropertyRef** per indicare che due proprietà (**ID** e **publisherID**) sono le estremità principale e dipendente di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="c7574-952">Elemento ReferenceType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="c7574-953">L'elemento **ReferenceType** in Conceptual Schema Definition Language (CSDL) specifica un riferimento a un tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="c7574-954">L'elemento **ReferenceType** può essere figlio dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="c7574-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="c7574-955">ReturnType (funzione)</span><span class="sxs-lookup"><span data-stu-id="c7574-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="c7574-956">Parametro</span><span class="sxs-lookup"><span data-stu-id="c7574-956">Parameter</span></span>
-   <span data-ttu-id="c7574-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="c7574-957">CollectionType</span></span>

<span data-ttu-id="c7574-958">L'elemento **ReferenceType** viene usato quando si definisce un parametro o un tipo restituito per una funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="c7574-959">Un elemento **ReferenceType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-960">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-961">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-962">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-962">Applicable Attributes</span></span>

<span data-ttu-id="c7574-963">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **ReferenceType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="c7574-964">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-964">Attribute Name</span></span> | <span data-ttu-id="c7574-965">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-965">Is Required</span></span> | <span data-ttu-id="c7574-966">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="c7574-967">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-967">**Type**</span></span>       | <span data-ttu-id="c7574-968">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-968">Yes</span></span>         | <span data-ttu-id="c7574-969">Nome del tipo di entità a cui viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="c7574-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-970">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **ReferenceType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="c7574-971">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-972">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-973">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-973">Example</span></span>

<span data-ttu-id="c7574-974">Nell'esempio seguente viene illustrato l'elemento **ReferenceType** usato come figlio di un elemento **Parameter** in una funzione definita dal modello che accetta un riferimento a un tipo di entità **Person** :</span><span class="sxs-lookup"><span data-stu-id="c7574-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="c7574-975">Nell'esempio seguente viene illustrato l'elemento **ReferenceType** utilizzato come elemento figlio di un elemento **returnType** (Function) in una funzione definita dal modello che restituisce un riferimento a un tipo di entità **Person** :</span><span class="sxs-lookup"><span data-stu-id="c7574-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="c7574-976">Elemento ReferentialConstraint (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="c7574-977">Un elemento **ReferentialConstraint** in Conceptual Schema Definition Language (CSDL) definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="c7574-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="c7574-978">Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="c7574-979">Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="c7574-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="c7574-980">Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.</span><span class="sxs-lookup"><span data-stu-id="c7574-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="c7574-981">Se una chiave esterna esposta in un tipo di entità fa riferimento a una proprietà in un altro tipo di entità, l'elemento **ReferentialConstraint** definisce un'associazione tra i due tipi di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="c7574-982">Poiché l'elemento **ReferentialConstraint** fornisce informazioni sul modo in cui due tipi di entità sono correlati, non è necessario alcun elemento AssociationSetMapping corrispondente nella Mapping Specification Language (MSL).</span><span class="sxs-lookup"><span data-stu-id="c7574-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="c7574-983">Un'associazione tra due tipi di entità che non dispongono di chiavi esterne esposte deve avere un elemento **AssociationSetMapping** corrispondente per eseguire il mapping delle informazioni di associazione all'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="c7574-984">Se una chiave esterna non è esposta in un tipo di entità, l'elemento **ReferentialConstraint** può definire solo un vincolo PRIMARY KEY-Primary Key tra il tipo di entità e un altro tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="c7574-985">Un elemento **ReferentialConstraint** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-986">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-987">Principal (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="c7574-988">Dependent (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="c7574-989">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-990">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-990">Applicable Attributes</span></span>

<span data-ttu-id="c7574-991">L'elemento **ReferentialConstraint** può avere un numero qualsiasi di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="c7574-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="c7574-992">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-993">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-994">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-994">Example</span></span>

<span data-ttu-id="c7574-995">Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** usato come parte della definizione dell'associazione **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="c7574-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="c7574-996">Elemento ReturnType (Function) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="c7574-997">L'elemento **returnType** (Function) in Conceptual Schema Definition Language (CSDL) specifica il tipo restituito per una funzione definita in un elemento Function.</span><span class="sxs-lookup"><span data-stu-id="c7574-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="c7574-998">Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="c7574-999">I tipi restituiti possono essere qualsiasi **EDMSimpleType**, tipo di entità, tipo complesso, tipo di riga, tipo di riferimento o una raccolta di uno di questi tipi.</span><span class="sxs-lookup"><span data-stu-id="c7574-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="c7574-1000">Il tipo restituito di una funzione può essere specificato con l'attributo **Type** dell'elemento **returnType** (Function) o con uno degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="c7574-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="c7574-1001">CollectionType</span></span>
-   <span data-ttu-id="c7574-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="c7574-1002">ReferenceType</span></span>
-   <span data-ttu-id="c7574-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="c7574-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-1004">Un modello non viene convalidato se si specifica un tipo restituito dalla funzione con l'attributo **Type** dell'elemento **returnType** (Function) e uno degli elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="c7574-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-1005">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1005">Applicable Attributes</span></span>

<span data-ttu-id="c7574-1006">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **returnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="c7574-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="c7574-1007">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-1007">Attribute Name</span></span> | <span data-ttu-id="c7574-1008">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-1008">Is Required</span></span> | <span data-ttu-id="c7574-1009">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="c7574-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="c7574-1010">**ReturnType**</span></span> | <span data-ttu-id="c7574-1011">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1011">No</span></span>          | <span data-ttu-id="c7574-1012">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-1013">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="c7574-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="c7574-1014">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-1015">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-1016">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1016">Example</span></span>

<span data-ttu-id="c7574-1017">Nell'esempio seguente viene usato un elemento **Function** per definire una funzione che restituisce il numero di anni in cui un libro è stato stampato.</span><span class="sxs-lookup"><span data-stu-id="c7574-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="c7574-1018">Si noti che il tipo restituito viene specificato dall'attributo **Type** di un elemento **returnType** (Function).</span><span class="sxs-lookup"><span data-stu-id="c7574-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="c7574-1019">Elemento ReturnType (FunctionImport) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="c7574-1020">L'elemento **returnType** (FunctionImport) in Conceptual Schema Definition Language (CSDL) specifica il tipo restituito per una funzione definita in un elemento FunctionImport.</span><span class="sxs-lookup"><span data-stu-id="c7574-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="c7574-1021">Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="c7574-1022">I tipi restituiti possono essere qualsiasi raccolta di tipi di entità, tipi complessi o **EDMSimpleType**,</span><span class="sxs-lookup"><span data-stu-id="c7574-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="c7574-1023">Il tipo restituito di una funzione viene specificato con l'attributo **Type** dell'elemento **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="c7574-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-1024">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1024">Applicable Attributes</span></span>

<span data-ttu-id="c7574-1025">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="c7574-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="c7574-1026">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-1026">Attribute Name</span></span> | <span data-ttu-id="c7574-1027">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-1027">Is Required</span></span> | <span data-ttu-id="c7574-1028">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-1029">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-1029">**Type**</span></span>       | <span data-ttu-id="c7574-1030">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1030">No</span></span>          | <span data-ttu-id="c7574-1031">Tipo restituito dalla funzione.</span><span class="sxs-lookup"><span data-stu-id="c7574-1031">The type that the function returns.</span></span> <span data-ttu-id="c7574-1032">Il valore deve essere una raccolta di ComplexType, EntityType o EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="c7574-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="c7574-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="c7574-1033">**EntitySet**</span></span>  | <span data-ttu-id="c7574-1034">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1034">No</span></span>          | <span data-ttu-id="c7574-1035">Se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve essere il set di entità a cui appartiene la raccolta.</span><span class="sxs-lookup"><span data-stu-id="c7574-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="c7574-1036">In caso contrario, l'attributo **EntitySet** non deve essere utilizzato.</span><span class="sxs-lookup"><span data-stu-id="c7574-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-1037">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="c7574-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="c7574-1038">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-1039">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-1040">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1040">Example</span></span>

<span data-ttu-id="c7574-1041">Nell'esempio seguente viene usato un **FunctionImport** che restituisce libri e autori.</span><span class="sxs-lookup"><span data-stu-id="c7574-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="c7574-1042">Si noti che la funzione restituisce due set di risultati e pertanto sono specificati due elementi **returnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="c7574-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="c7574-1043">Elemento RowType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="c7574-1044">Un elemento **RowType** in Conceptual Schema Definition Language (CSDL) definisce una struttura senza nome come parametro o tipo restituito per una funzione definita nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="c7574-1045">Un elemento **RowType** può essere figlio dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="c7574-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="c7574-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="c7574-1046">CollectionType</span></span>
-   <span data-ttu-id="c7574-1047">Parametro</span><span class="sxs-lookup"><span data-stu-id="c7574-1047">Parameter</span></span>
-   <span data-ttu-id="c7574-1048">ReturnType (funzione)</span><span class="sxs-lookup"><span data-stu-id="c7574-1048">ReturnType (Function)</span></span>

<span data-ttu-id="c7574-1049">Un elemento **RowType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-1050">Property (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-1050">Property (one or more)</span></span>
-   <span data-ttu-id="c7574-1051">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-1052">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1052">Applicable Attributes</span></span>

<span data-ttu-id="c7574-1053">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="c7574-1054">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-1055">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-1056">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1056">Example</span></span>

<span data-ttu-id="c7574-1057">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="c7574-1058">Elemento Schema (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="c7574-1059">L'elemento **schema** è l'elemento radice di una definizione del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="c7574-1060">Contiene definizioni per gli oggetti, le funzioni e i contenitori che costituiscono un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="c7574-1061">L'elemento **schema** può contenere zero o più degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="c7574-1062">Con</span><span class="sxs-lookup"><span data-stu-id="c7574-1062">Using</span></span>
-   <span data-ttu-id="c7574-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="c7574-1063">EntityContainer</span></span>
-   <span data-ttu-id="c7574-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="c7574-1064">EntityType</span></span>
-   <span data-ttu-id="c7574-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="c7574-1065">EnumType</span></span>
-   <span data-ttu-id="c7574-1066">Association Rules</span><span class="sxs-lookup"><span data-stu-id="c7574-1066">Association</span></span>
-   <span data-ttu-id="c7574-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="c7574-1067">ComplexType</span></span>
-   <span data-ttu-id="c7574-1068">Funzione</span><span class="sxs-lookup"><span data-stu-id="c7574-1068">Function</span></span>

<span data-ttu-id="c7574-1069">Un elemento **dello schema** può contenere zero o un elemento Annotation.</span><span class="sxs-lookup"><span data-stu-id="c7574-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-1070">L'elemento **Function** e gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c7574-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="c7574-1071">L'elemento **schema** utilizza l'attributo **namespace** per definire lo spazio dei nomi per il tipo di entità, il tipo complesso e gli oggetti di associazione in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="c7574-1072">All'interno di uno spazio dei nomi due oggetti non possono avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="c7574-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="c7574-1073">Gli spazi dei nomi possono estendersi a più elementi **dello schema** e a più file con estensione csdl.</span><span class="sxs-lookup"><span data-stu-id="c7574-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="c7574-1074">Uno spazio dei nomi del modello concettuale è diverso dallo spazio dei nomi XML dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="c7574-1075">Uno spazio dei nomi del modello concettuale (definito dall'attributo **namespace** ) è un contenitore logico per tipi di entità, tipi complessi e tipi di associazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="c7574-1076">Lo spazio dei nomi XML, indicato dall'attributo **xmlns** , di un elemento **schema** è lo spazio dei nomi predefinito per gli elementi figlio e gli attributi dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="c7574-1077">Gli spazi dei nomi XML del form https://schemas.microsoft.com/ado/YYYY/MM/edm (dove aaaa e mm rappresentano rispettivamente l'anno e il mese) sono riservati per CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="c7574-1078">Gli elementi e gli attributi personalizzati non possono essere in spazi dei nomi che hanno questo form.</span><span class="sxs-lookup"><span data-stu-id="c7574-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-1079">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1079">Applicable Attributes</span></span>

<span data-ttu-id="c7574-1080">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="c7574-1081">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-1081">Attribute Name</span></span> | <span data-ttu-id="c7574-1082">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-1082">Is Required</span></span> | <span data-ttu-id="c7574-1083">Valore</span><span class="sxs-lookup"><span data-stu-id="c7574-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-1084">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="c7574-1084">**Namespace**</span></span>  | <span data-ttu-id="c7574-1085">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1085">Yes</span></span>         | <span data-ttu-id="c7574-1086">Spazio dei nomi del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="c7574-1087">Il valore dell'attributo **namespace** viene utilizzato per formare il nome completo di un tipo.</span><span class="sxs-lookup"><span data-stu-id="c7574-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="c7574-1088">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi Simple. example. Model, il nome completo di **EntityType** sarà sarà SimpleExampleModel. Customer.</span><span class="sxs-lookup"><span data-stu-id="c7574-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="c7574-1089">Non è possibile utilizzare le seguenti stringhe come valore per l'attributo **namespace** : **System**, **Transient**o **EDM**.</span><span class="sxs-lookup"><span data-stu-id="c7574-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="c7574-1090">Il valore dell'attributo **namespace** non può corrispondere al valore dell'attributo **namespace** nell'elemento schema SSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="c7574-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="c7574-1091">**Alias**</span></span>      | <span data-ttu-id="c7574-1092">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1092">No</span></span>          | <span data-ttu-id="c7574-1093">Identificatore utilizzato al posto del nome dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c7574-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="c7574-1094">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi Simple. example. Model e il valore dell'attributo **alias** è *Model*, sarà possibile utilizzare Model. Customer come nome completo dell'elemento **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="c7574-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="c7574-1095">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="c7574-1096">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-1097">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-1098">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1098">Example</span></span>

<span data-ttu-id="c7574-1099">Nell'esempio seguente viene illustrato un elemento **dello schema** che contiene un elemento **EntityContainer** , due elementi **EntityType** e un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="c7574-1100">Elemento TypeRef (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="c7574-1101">L'elemento **typeref** in Conceptual Schema Definition Language (CSDL) fornisce un riferimento a un tipo denominato esistente.</span><span class="sxs-lookup"><span data-stu-id="c7574-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="c7574-1102">L'elemento **typeref** può essere un elemento figlio dell'elemento CollectionType, che viene usato per specificare che una funzione ha una raccolta come parametro o tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="c7574-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="c7574-1103">Un elemento **typeref** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="c7574-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="c7574-1104">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="c7574-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="c7574-1105">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="c7574-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-1106">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1106">Applicable Attributes</span></span>

<span data-ttu-id="c7574-1107">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **typeref** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="c7574-1108">Si noti che gli attributi **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**e **Collation** sono applicabili solo a **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="c7574-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="c7574-1109">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="c7574-1110">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-1110">Is Required</span></span> | <span data-ttu-id="c7574-1111">valore</span><span class="sxs-lookup"><span data-stu-id="c7574-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-1112">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="c7574-1112">**Type**</span></span>                                                           | <span data-ttu-id="c7574-1113">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1113">No</span></span>          | <span data-ttu-id="c7574-1114">Nome del tipo a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="c7574-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="c7574-1115">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="c7574-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="c7574-1116">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1116">No</span></span>          | <span data-ttu-id="c7574-1117">**True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null.</span><span class="sxs-lookup"><span data-stu-id="c7574-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="c7574-1118">> in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="c7574-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="c7574-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="c7574-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="c7574-1120">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1120">No</span></span>          | <span data-ttu-id="c7574-1121">Valore predefinito della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="c7574-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="c7574-1123">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1123">No</span></span>          | <span data-ttu-id="c7574-1124">Lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="c7574-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="c7574-1126">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1126">No</span></span>          | <span data-ttu-id="c7574-1127">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="c7574-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="c7574-1128">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-1128">**Precision**</span></span>                                                      | <span data-ttu-id="c7574-1129">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1129">No</span></span>          | <span data-ttu-id="c7574-1130">Precisione del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="c7574-1131">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-1131">**Scale**</span></span>                                                          | <span data-ttu-id="c7574-1132">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1132">No</span></span>          | <span data-ttu-id="c7574-1133">Scala del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="c7574-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-1134">**SRID**</span></span>                                                           | <span data-ttu-id="c7574-1135">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1135">No</span></span>          | <span data-ttu-id="c7574-1136">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="c7574-1137">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="c7574-1138">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="c7574-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="c7574-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="c7574-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="c7574-1140">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1140">No</span></span>          | <span data-ttu-id="c7574-1141">**True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="c7574-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="c7574-1142">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="c7574-1142">**Collation**</span></span>                                                      | <span data-ttu-id="c7574-1143">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1143">No</span></span>          | <span data-ttu-id="c7574-1144">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="c7574-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="c7574-1145">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="c7574-1146">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-1147">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-1148">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1148">Example</span></span>

<span data-ttu-id="c7574-1149">Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza l'elemento **typeref** (come figlio di un elemento **CollectionType** ) per specificare che la funzione accetta una raccolta di tipi di entità **Department** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="c7574-1150">Elemento Using (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="c7574-1151">L'elemento **using** in Conceptual Schema Definition Language (CSDL) importa il contenuto di un modello concettuale presente in uno spazio dei nomi diverso.</span><span class="sxs-lookup"><span data-stu-id="c7574-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="c7574-1152">Impostando il valore dell'attributo **namespace** , è possibile fare riferimento a tipi di entità, tipi complessi e tipi di associazione definiti in un altro modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="c7574-1153">Più di un elemento **using** può essere figlio di un elemento **dello schema** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-1154">L'elemento **using** in CSDL non funziona esattamente come un'istruzione **using** in un linguaggio di programmazione.</span><span class="sxs-lookup"><span data-stu-id="c7574-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="c7574-1155">Importando uno spazio dei nomi con un'istruzione **using** in un linguaggio di programmazione, non si influiscono sugli oggetti nello spazio dei nomi originale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="c7574-1156">In CSDL, uno spazio dei nomi importato può contenere un tipo di entità derivato da un tipo di entità nello spazio dei nomi originale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="c7574-1157">Ciò può influire sui set di entità dichiarati nello spazio dei nomi originale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="c7574-1158">L'elemento **using** può includere i seguenti elementi figlio:</span><span class="sxs-lookup"><span data-stu-id="c7574-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="c7574-1159">Documentation (zero elementi o un elemento consentito)</span><span class="sxs-lookup"><span data-stu-id="c7574-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="c7574-1160">Elementi Annotation (zero o più elementi consentiti)</span><span class="sxs-lookup"><span data-stu-id="c7574-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="c7574-1161">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1161">Applicable Attributes</span></span>

<span data-ttu-id="c7574-1162">La tabella seguente descrive gli attributi che possono essere applicati all'elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="c7574-1163">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="c7574-1163">Attribute Name</span></span> | <span data-ttu-id="c7574-1164">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="c7574-1164">Is Required</span></span> | <span data-ttu-id="c7574-1165">Valore</span><span class="sxs-lookup"><span data-stu-id="c7574-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="c7574-1166">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="c7574-1166">**Namespace**</span></span>  | <span data-ttu-id="c7574-1167">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1167">Yes</span></span>         | <span data-ttu-id="c7574-1168">Nome dello spazio dei nomi importato.</span><span class="sxs-lookup"><span data-stu-id="c7574-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="c7574-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="c7574-1169">**Alias**</span></span>      | <span data-ttu-id="c7574-1170">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1170">Yes</span></span>         | <span data-ttu-id="c7574-1171">Identificatore utilizzato al posto del nome dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="c7574-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="c7574-1172">Anche se questo attributo è obbligatorio, non è necessario utilizzarlo al posto del nome dello spazio dei nomi per qualificare nomi di oggetti.</span><span class="sxs-lookup"><span data-stu-id="c7574-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="c7574-1173">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="c7574-1174">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="c7574-1175">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="c7574-1176">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1176">Example</span></span>

<span data-ttu-id="c7574-1177">Nell'esempio seguente viene illustrato l'elemento **using** usato per importare uno spazio dei nomi definito altrove.</span><span class="sxs-lookup"><span data-stu-id="c7574-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="c7574-1178">Si noti che lo spazio dei nomi per l'elemento **dello schema** visualizzato è `BooksModel` .</span><span class="sxs-lookup"><span data-stu-id="c7574-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="c7574-1179">La `Address` Proprietà in `Publisher` **EntityType** è un tipo complesso definito nello `ExtendedBooksModel` spazio dei nomi (importato con l'elemento **using** ).</span><span class="sxs-lookup"><span data-stu-id="c7574-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="c7574-1180">Attributi di annotazione (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="c7574-1181">Gli attributi di annotazione in Conceptual Schema Definition Language (CSDL) sono attributi XML personalizzati nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="c7574-1182">Oltre ad avere una struttura XML valida, per gli attributi di annotazione hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="c7574-1183">Gli attributi di annotazione non devono trovarsi in spazi dei nomi XML riservati a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="c7574-1184">È possibile applicare più attributi di annotazione a un determinato elemento CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="c7574-1185">I nomi completi di due attributi di annotazione non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="c7574-1186">Gli attributi di annotazione possono essere utilizzati per fornire metadati aggiuntivi sugli elementi in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="c7574-1187">È possibile accedere ai metadati contenuti negli elementi Annotation in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="c7574-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-1188">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1188">Example</span></span>

<span data-ttu-id="c7574-1189">Nell'esempio seguente viene illustrato un elemento **EntityType** con un attributo Annotation (**CustomAttribute**).</span><span class="sxs-lookup"><span data-stu-id="c7574-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="c7574-1190">Nell'esempio viene inoltre mostrato un elemento di annotazione applicato all'elemento del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-1190">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="c7574-1191">Il codice seguente recupera i metadati dell'attributo di annotazione e li scrive nella console:</span><span class="sxs-lookup"><span data-stu-id="c7574-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="c7574-1192">Nel codice riportato in precedenza si presuppone che il file `School.csdl` si trovi nella directory di output del progetto e che al progetto siano state aggiunte le istruzioni `Imports` e `Using` seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="c7574-1193">Elementi di annotazione (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="c7574-1194">Gli elementi Annotation in Conceptual Schema Definition Language (CSDL) sono elementi XML personalizzati nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="c7574-1195">Oltre ad avere una struttura XML valida, gli elementi Annotation hanno le caratteristiche seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="c7574-1196">Gli elementi Annotation non devono trovarsi in spazi dei nomi XML riservati a CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="c7574-1197">Più elementi Annotation possono essere figli di un dato elemento CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="c7574-1198">I nomi completi di due elementi Annotation non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="c7574-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="c7574-1199">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio di un dato elemento CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="c7574-1200">Gli elementi Annotation possono essere utilizzati per fornire metadati aggiuntivi sugli elementi in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="c7574-1201">A partire da .NET Framework versione 4, è possibile accedere ai metadati contenuti negli elementi di annotazione in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="c7574-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-1202">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1202">Example</span></span>

<span data-ttu-id="c7574-1203">Nell'esempio seguente viene illustrato un elemento **EntityType** con un elemento Annotation (**CustomElement**).</span><span class="sxs-lookup"><span data-stu-id="c7574-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="c7574-1204">Nell'esempio viene inoltre mostrato un attributo di annotazione applicato all'elemento del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="c7574-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="c7574-1205">Il codice seguente recupera i metadati dell'elemento Annotation e li scrive nella console:</span><span class="sxs-lookup"><span data-stu-id="c7574-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="c7574-1206">Nel codice riportato in precedenza si presuppone che il file School.csdl si trovi nella directory di output del progetto e che al progetto siano state aggiunte le istruzioni `Imports` e `Using` seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="c7574-1207">Tipi del modello concettuale (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="c7574-1208">CSDL (Conceptual Schema Definition Language) supporta un set di tipi di dati primitivi astratti, denominati **EDMSimpleTypes**, che definiscono le proprietà in un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="c7574-1209">**EDMSimpleTypes** sono proxy per i tipi di dati primitivi supportati nell'ambiente di archiviazione o host.</span><span class="sxs-lookup"><span data-stu-id="c7574-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="c7574-1210">Nella tabella seguente vengono elencati i tipi di dati primitivi supportati da CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="c7574-1211">Nella tabella sono inoltre elencati i facet che è possibile applicare a ogni **EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="c7574-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="c7574-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="c7574-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="c7574-1213">Description</span><span class="sxs-lookup"><span data-stu-id="c7574-1213">Description</span></span>                                                | <span data-ttu-id="c7574-1214">Facet applicabili</span><span class="sxs-lookup"><span data-stu-id="c7574-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="c7574-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="c7574-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="c7574-1216">Contiene dati binari.</span><span class="sxs-lookup"><span data-stu-id="c7574-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="c7574-1217">MaxLength, FixedLength, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="c7574-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="c7574-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="c7574-1219">Contiene il valore **true** o **false**.</span><span class="sxs-lookup"><span data-stu-id="c7574-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="c7574-1220">Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="c7574-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="c7574-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="c7574-1222">Contiene un Unsigned Integer a 8 bit.</span><span class="sxs-lookup"><span data-stu-id="c7574-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="c7574-1223">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="c7574-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="c7574-1225">Rappresenta una data e un'ora.</span><span class="sxs-lookup"><span data-stu-id="c7574-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="c7574-1226">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="c7574-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="c7574-1228">Contiene una data e un'ora come offset in minuti rispetto all'ora GMT.</span><span class="sxs-lookup"><span data-stu-id="c7574-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="c7574-1229">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="c7574-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="c7574-1231">Contiene un valore numerico con scala e precisione fisse.</span><span class="sxs-lookup"><span data-stu-id="c7574-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="c7574-1232">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="c7574-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="c7574-1234">Contiene un numero a virgola mobile con precisione di 15 cifre</span><span class="sxs-lookup"><span data-stu-id="c7574-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="c7574-1235">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1236">**EDM. float**</span><span class="sxs-lookup"><span data-stu-id="c7574-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="c7574-1237">Contiene un numero a virgola mobile con precisione a 7 cifre.</span><span class="sxs-lookup"><span data-stu-id="c7574-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="c7574-1238">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="c7574-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="c7574-1240">Contiene un identificatore univoco a 16 byte.</span><span class="sxs-lookup"><span data-stu-id="c7574-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="c7574-1241">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="c7574-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="c7574-1243">Contiene un Signed Integer a 16 bit.</span><span class="sxs-lookup"><span data-stu-id="c7574-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="c7574-1244">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="c7574-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="c7574-1246">Contiene un Signed Integer a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="c7574-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="c7574-1247">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="c7574-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="c7574-1249">Contiene un Signed Integer a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="c7574-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="c7574-1250">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="c7574-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="c7574-1252">Contiene un Signed Integer a 8 bit.</span><span class="sxs-lookup"><span data-stu-id="c7574-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="c7574-1253">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="c7574-1254">**Edm.String**</span></span>                   | <span data-ttu-id="c7574-1255">Contiene dati di tipo carattere.</span><span class="sxs-lookup"><span data-stu-id="c7574-1255">Contains character data.</span></span>                                   | <span data-ttu-id="c7574-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="c7574-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="c7574-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="c7574-1258">Contiene un'ora del giorno.</span><span class="sxs-lookup"><span data-stu-id="c7574-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="c7574-1259">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="c7574-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="c7574-1260">**EDM. Geography**</span><span class="sxs-lookup"><span data-stu-id="c7574-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="c7574-1261">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="c7574-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="c7574-1263">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1264">**EDM. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="c7574-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="c7574-1265">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1266">**EDM. GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="c7574-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="c7574-1267">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1268">**EDM. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="c7574-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="c7574-1269">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1270">**EDM. GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="c7574-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="c7574-1271">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1272">**EDM. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="c7574-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="c7574-1273">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1274">**EDM. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="c7574-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="c7574-1275">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1276">**EDM. Geometry**</span><span class="sxs-lookup"><span data-stu-id="c7574-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="c7574-1277">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1278">**EDM. GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="c7574-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="c7574-1279">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1280">**EDM. GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="c7574-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="c7574-1281">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1282">**EDM. GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="c7574-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="c7574-1283">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1284">**EDM. GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="c7574-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="c7574-1285">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1286">**EDM. GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="c7574-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="c7574-1287">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1288">**EDM. GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="c7574-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="c7574-1289">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="c7574-1290">**EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="c7574-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="c7574-1291">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="c7574-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="c7574-1292">Facet (CSDL)</span><span class="sxs-lookup"><span data-stu-id="c7574-1292">Facets (CSDL)</span></span>

<span data-ttu-id="c7574-1293">I facet in Conceptual Schema Definition Language (CSDL) rappresentano vincoli sulle proprietà di tipi di entità e di tipi complessi.</span><span class="sxs-lookup"><span data-stu-id="c7574-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="c7574-1294">I facet appaiono come attributi XML negli elementi CSDL seguenti:</span><span class="sxs-lookup"><span data-stu-id="c7574-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="c7574-1295">Proprietà</span><span class="sxs-lookup"><span data-stu-id="c7574-1295">Property</span></span>
-   <span data-ttu-id="c7574-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="c7574-1296">TypeRef</span></span>
-   <span data-ttu-id="c7574-1297">Parametro</span><span class="sxs-lookup"><span data-stu-id="c7574-1297">Parameter</span></span>

<span data-ttu-id="c7574-1298">Nella tabella seguente vengono descritti i facet supportati in CSDL.</span><span class="sxs-lookup"><span data-stu-id="c7574-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="c7574-1299">Tutti i facet sono facoltativi.</span><span class="sxs-lookup"><span data-stu-id="c7574-1299">All facets are optional.</span></span> <span data-ttu-id="c7574-1300">Alcuni facet elencati di seguito vengono usati dal Entity Framework durante la generazione di un database da un modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="c7574-1301">Per informazioni sui tipi di dati in un modello concettuale, vedere tipi di modelli concettuali (CSDL).</span><span class="sxs-lookup"><span data-stu-id="c7574-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="c7574-1302">Facet</span><span class="sxs-lookup"><span data-stu-id="c7574-1302">Facet</span></span>               | <span data-ttu-id="c7574-1303">Description</span><span class="sxs-lookup"><span data-stu-id="c7574-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="c7574-1304">Si applica a</span><span class="sxs-lookup"><span data-stu-id="c7574-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="c7574-1305">Utilizzato per la generazione di database.</span><span class="sxs-lookup"><span data-stu-id="c7574-1305">Used for the database generation</span></span> | <span data-ttu-id="c7574-1306">Utilizzato dal runtime</span><span class="sxs-lookup"><span data-stu-id="c7574-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="c7574-1307">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="c7574-1307">**Collation**</span></span>       | <span data-ttu-id="c7574-1308">Specifica la sequenza di ordinamento da usare quando si eseguono operazioni di confronto e di ordinamento su valori della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="c7574-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="c7574-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="c7574-1310">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1310">Yes</span></span>                              | <span data-ttu-id="c7574-1311">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1311">No</span></span>                  |
| <span data-ttu-id="c7574-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="c7574-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="c7574-1313">Indica che il valore della proprietà deve essere usato per le verifiche della concorrenza ottimistica.</span><span class="sxs-lookup"><span data-stu-id="c7574-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="c7574-1314">Tutte le proprietà di **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="c7574-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="c7574-1315">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1315">No</span></span>                               | <span data-ttu-id="c7574-1316">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1316">Yes</span></span>                 |
| <span data-ttu-id="c7574-1317">**Default**</span><span class="sxs-lookup"><span data-stu-id="c7574-1317">**Default**</span></span>         | <span data-ttu-id="c7574-1318">Specifica il valore predefinito della proprietà se durante la creazione di istanze non viene fornito alcun valore.</span><span class="sxs-lookup"><span data-stu-id="c7574-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="c7574-1319">Tutte le proprietà di **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="c7574-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="c7574-1320">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1320">Yes</span></span>                              | <span data-ttu-id="c7574-1321">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1321">Yes</span></span>                 |
| <span data-ttu-id="c7574-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-1322">**FixedLength**</span></span>     | <span data-ttu-id="c7574-1323">Specifica se la lunghezza del valore della proprietà può variare.</span><span class="sxs-lookup"><span data-stu-id="c7574-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="c7574-1324">**EDM. Binary**, **EDM. String**</span><span class="sxs-lookup"><span data-stu-id="c7574-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="c7574-1325">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1325">Yes</span></span>                              | <span data-ttu-id="c7574-1326">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1326">No</span></span>                  |
| <span data-ttu-id="c7574-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="c7574-1327">**MaxLength**</span></span>       | <span data-ttu-id="c7574-1328">Specifica la lunghezza massima del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="c7574-1329">**EDM. Binary**, **EDM. String**</span><span class="sxs-lookup"><span data-stu-id="c7574-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="c7574-1330">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1330">Yes</span></span>                              | <span data-ttu-id="c7574-1331">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1331">No</span></span>                  |
| <span data-ttu-id="c7574-1332">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="c7574-1332">**Nullable**</span></span>        | <span data-ttu-id="c7574-1333">Specifica se la proprietà può avere un valore **null** .</span><span class="sxs-lookup"><span data-stu-id="c7574-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="c7574-1334">Tutte le proprietà di **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="c7574-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="c7574-1335">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1335">Yes</span></span>                              | <span data-ttu-id="c7574-1336">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1336">Yes</span></span>                 |
| <span data-ttu-id="c7574-1337">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="c7574-1337">**Precision**</span></span>       | <span data-ttu-id="c7574-1338">Per le proprietà di tipo **Decimal**, specifica il numero di cifre che un valore della proprietà può avere.</span><span class="sxs-lookup"><span data-stu-id="c7574-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="c7574-1339">Per le proprietà di tipo **Time**, **DateTime**e **DateTimeOffset**, specifica il numero di cifre per la parte frazionaria dei secondi del valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="c7574-1340">**EDM. DateTime**, **EDM. DateTimeOffset**, **EDM. Decimal**, **EDM. Time**</span><span class="sxs-lookup"><span data-stu-id="c7574-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="c7574-1341">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1341">Yes</span></span>                              | <span data-ttu-id="c7574-1342">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1342">No</span></span>                  |
| <span data-ttu-id="c7574-1343">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="c7574-1343">**Scale**</span></span>           | <span data-ttu-id="c7574-1344">Specifica il numero di cifre a destra del separatore decimale per il valore della proprietà.</span><span class="sxs-lookup"><span data-stu-id="c7574-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="c7574-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="c7574-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="c7574-1346">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1346">Yes</span></span>                              | <span data-ttu-id="c7574-1347">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1347">No</span></span>                  |
| <span data-ttu-id="c7574-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="c7574-1348">**SRID**</span></span>            | <span data-ttu-id="c7574-1349">Specifica l'ID del sistema di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="c7574-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="c7574-1350">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="c7574-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="c7574-1351">**EDM. Geography, Edm. GeographyPoint, Edm. GeographyLineString, Edm. GeographyPolygon, Edm. GeographyMultiPoint, Edm. GeographyMultiLineString, Edm. GeographyMultiPolygon, Edm. GeographyCollection, Edm. Geometry, Edm. GeometryPoint, Edm. GeometryLineString, Edm. GeometryPolygon, Edm. GeometryMultiPoint, Edm. GeometryMultiLineString, Edm. GeometryMultiPolygon, Edm. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="c7574-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="c7574-1352">No</span><span class="sxs-lookup"><span data-stu-id="c7574-1352">No</span></span>                               | <span data-ttu-id="c7574-1353">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1353">Yes</span></span>                 |
| <span data-ttu-id="c7574-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="c7574-1354">**Unicode**</span></span>         | <span data-ttu-id="c7574-1355">Viene indicato se il valore della proprietà viene archiviato come Unicode.</span><span class="sxs-lookup"><span data-stu-id="c7574-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="c7574-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="c7574-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="c7574-1357">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1357">Yes</span></span>                              | <span data-ttu-id="c7574-1358">Sì</span><span class="sxs-lookup"><span data-stu-id="c7574-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="c7574-1359">Quando si genera un database da un modello concettuale, la procedura guidata genera database riconoscerà il valore dell'attributo **StoreGeneratedPattern** su un elemento **Proprietà** se si trova nello spazio dei nomi seguente: https://schemas.microsoft.com/ado/2009/02/edm/annotation .</span><span class="sxs-lookup"><span data-stu-id="c7574-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="c7574-1360">I valori supportati per l'attributo sono **Identity** e **calcolato**.</span><span class="sxs-lookup"><span data-stu-id="c7574-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="c7574-1361">Il valore **Identity** produrrà una colonna di database con un valore Identity generato nel database.</span><span class="sxs-lookup"><span data-stu-id="c7574-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="c7574-1362">Il valore **calcolato** produrrà una colonna con un valore calcolato nel database.</span><span class="sxs-lookup"><span data-stu-id="c7574-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="c7574-1363">Esempio</span><span class="sxs-lookup"><span data-stu-id="c7574-1363">Example</span></span>

<span data-ttu-id="c7574-1364">Nell'esempio seguente vengono mostrati i facet applicati alle proprietà di un tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="c7574-1364">The following example shows facets applied to the properties of an entity type:</span></span>

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
