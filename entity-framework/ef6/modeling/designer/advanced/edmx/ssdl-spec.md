---
title: Specifica SSDL-EF6
description: Specifica SSDL in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: dbcd4c771e8ca1324a0520f9c8ca13ba4661487b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070211"
---
# <a name="ssdl-specification"></a><span data-ttu-id="2b5ba-103">Specifica SSDL</span><span class="sxs-lookup"><span data-stu-id="2b5ba-103">SSDL Specification</span></span>
<span data-ttu-id="2b5ba-104">Store Schema Definition Language (SSDL) è un linguaggio basato su XML che descrive il modello di archiviazione di un'applicazione Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="2b5ba-105">In un'applicazione Entity Framework i metadati del modello di archiviazione vengono caricati da un file con estensione SSDL (scritto in SSDL) in un'istanza di System. Data. Metadata. Edm. StoreItemCollection ed è possibile accedervi usando i metodi della classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="2b5ba-106">Entity Framework usa i metadati del modello di archiviazione per tradurre le query sul modello concettuale in comandi specifici dell'archivio.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="2b5ba-107">Il Entity Framework Designer (EF designer) archivia le informazioni sul modello di archiviazione in un file con estensione edmx in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="2b5ba-108">In fase di compilazione il Entity Designer utilizza le informazioni contenute in un file con estensione edmx per creare il file con estensione SSDL necessario per Entity Framework in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="2b5ba-109">Le versioni di SSDL si differenziano tra loro per gli spazi dei nomi XML.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="2b5ba-110">Versione SSDL</span><span class="sxs-lookup"><span data-stu-id="2b5ba-110">SSDL Version</span></span> | <span data-ttu-id="2b5ba-111">Spazio dei nomi XML</span><span class="sxs-lookup"><span data-stu-id="2b5ba-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="2b5ba-112">SSDL V1</span><span class="sxs-lookup"><span data-stu-id="2b5ba-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="2b5ba-113">SSDL V2</span><span class="sxs-lookup"><span data-stu-id="2b5ba-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="2b5ba-114">SSDL V3</span><span class="sxs-lookup"><span data-stu-id="2b5ba-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="2b5ba-115">Elemento Association (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-115">Association Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-116">Un elemento **Association** in Store Schema Definition Language (SSDL) specifica le colonne della tabella che fanno parte di un vincolo FOREIGN KEY nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="2b5ba-117">Due elementi End figlio obbligatori specificano le tabelle che costituiscono le entità finali dell'associazione e la molteplicità di ciascuna entità finale.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="2b5ba-118">Un elemento ReferentialConstraint facoltativo specifica le entità finali principale e dipendente dell'associazione, nonché le colonne coinvolte.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="2b5ba-119">Se non è presente alcun elemento **ReferentialConstraint** , è necessario utilizzare un elemento AssociationSetMapping per specificare i mapping delle colonne per l'associazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="2b5ba-120">L'elemento **Association** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-121">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-122">End (esattamente due elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-122">End (exactly two)</span></span>
-   <span data-ttu-id="2b5ba-123">ReferentialConstraint (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-124">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-125">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-125">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-126">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="2b5ba-127">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-127">Attribute Name</span></span> | <span data-ttu-id="2b5ba-128">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-128">Is Required</span></span> | <span data-ttu-id="2b5ba-129">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-130">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-130">**Name**</span></span>       | <span data-ttu-id="2b5ba-131">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-131">Yes</span></span>         | <span data-ttu-id="2b5ba-132">Il nome del vincolo di chiave esterna corrispondente nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-133">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="2b5ba-134">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-135">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-136">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-136">Example</span></span>

<span data-ttu-id="2b5ba-137">Nell'esempio seguente viene illustrato un elemento **Association** che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* :</span><span class="sxs-lookup"><span data-stu-id="2b5ba-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a><span data-ttu-id="2b5ba-138">Elemento AssociationSet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-139">L'elemento **associativo** in Store Schema Definition Language (SSDL) rappresenta un vincolo di chiave esterna tra due tabelle nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="2b5ba-140">Le colonne della tabella che fanno parte del vincolo di chiave esterna vengono specificate in un elemento Association.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="2b5ba-141">L'elemento **Association** che corrisponde a **un elemento di associazione specificato è** specificato nell'attributo **Association** dell'elemento associationname. **AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="2b5ba-142">Il mapping dei set di associazioni SSDL viene eseguito in set di associazioni CSDL da un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="2b5ba-143">Tuttavia, se l'associazione CSDL per un determinato set di associazioni CSDL viene definita utilizzando un elemento ReferentialConstraint, non è necessario alcun elemento **AssociationSetMapping** corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="2b5ba-144">In questo caso, se è presente un elemento **AssociationSetMapping** , i mapping che definisce verranno sottoposti a override dall'elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="2b5ba-145">L'elemento **associationname** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-146">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-147">End (zero o due elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-147">End (zero or two)</span></span>
-   <span data-ttu-id="2b5ba-148">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-149">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-149">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-150">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="2b5ba-151">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-151">Attribute Name</span></span>  | <span data-ttu-id="2b5ba-152">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-152">Is Required</span></span> | <span data-ttu-id="2b5ba-153">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-154">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-154">**Name**</span></span>        | <span data-ttu-id="2b5ba-155">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-155">Yes</span></span>         | <span data-ttu-id="2b5ba-156">Nome del vincolo di chiave esterna rappresentato dal set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="2b5ba-157">**Associazione**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-157">**Association**</span></span> | <span data-ttu-id="2b5ba-158">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-158">Yes</span></span>         | <span data-ttu-id="2b5ba-159">Nome dell'associazione che definisce le colonne che fanno parte del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-160">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="2b5ba-161">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-162">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-163">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-163">Example</span></span>

<span data-ttu-id="2b5ba-164">Nell'esempio seguente viene illustrato un elemento di **associazione** che rappresenta il `FK_CustomerOrders` vincolo di chiave esterna nel database sottostante:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="2b5ba-165">Elemento CollectionType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-166">L'elemento **CollectionType** in Store Schema Definition Language (SSDL) specifica che il tipo restituito di una funzione è una raccolta.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="2b5ba-167">L'elemento **CollectionType** è figlio dell'elemento ReturnType.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="2b5ba-168">Il tipo di raccolta viene specificato tramite l'elemento figlio RowType:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="2b5ba-169">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="2b5ba-170">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-171">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-172">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-172">Example</span></span>

<span data-ttu-id="2b5ba-173">Nell'esempio seguente viene illustrata una funzione che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="2b5ba-174">Elemento CommandText (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-175">L'elemento **CommandText** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento Function che consente di definire un'istruzione SQL che viene eseguita nel database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="2b5ba-176">L'elemento **CommandText** consente di aggiungere una funzionalità simile a una stored procedure nel database, ma l'elemento **CommandText** viene definito nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="2b5ba-177">L'elemento **CommandText** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="2b5ba-178">Il corpo dell'elemento **CommandText** deve essere un'istruzione SQL valida per il database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="2b5ba-179">Nessun attributo applicabile all'elemento **CommandText** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-180">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-180">Example</span></span>

<span data-ttu-id="2b5ba-181">Nell'esempio seguente viene illustrato un elemento **Function** con un elemento **CommandText** figlio.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="2b5ba-182">Esporre la funzione **UpdateProductInOrder** come metodo in ObjectContext mediante l'importazione nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="2b5ba-183">Elemento DefiningQuery (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-184">L'elemento **DefiningQuery** in Store Schema Definition Language (SSDL) consente di eseguire un'istruzione SQL direttamente nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="2b5ba-185">L'elemento **DefiningQuery** viene comunemente utilizzato come una vista di database, ma la vista è definita nel modello di archiviazione anziché nel database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="2b5ba-186">La vista definita in un elemento **DefiningQuery** può essere mappata a un tipo di entità nel modello concettuale tramite un elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="2b5ba-187">Questi mapping sono di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-187">These mappings are read-only.</span></span>  

<span data-ttu-id="2b5ba-188">Nella sintassi SSDL seguente viene illustrata la dichiarazione di un oggetto **EntitySet** seguito dall'elemento **DefiningQuery** che contiene una query utilizzata per recuperare la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

<span data-ttu-id="2b5ba-189">È possibile utilizzare le stored procedure nel Entity Framework per abilitare gli scenari di lettura e scrittura sulle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="2b5ba-190">È possibile utilizzare una vista origine dati o una vista Entity SQL come tabella di base per il recupero dei dati e per l'elaborazione delle modifiche da parte delle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="2b5ba-191">È possibile usare l'elemento **DefiningQuery** per la destinazione Microsoft SQL Server Compact 3,5.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="2b5ba-192">Sebbene SQL Server Compact 3,5 non supporti stored procedure, è possibile implementare una funzionalità simile con l'elemento **DefiningQuery** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="2b5ba-193">Un'altra situazione in cui può essere utile è nella creazione di stored procedure per risolvere una mancata corrispondenza tra i tipi di dati utilizzati nel linguaggio di programmazione e quelli dell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="2b5ba-194">È possibile scrivere un **DefiningQuery** che accetta un determinato set di parametri e quindi chiama un stored procedure con un set di parametri diverso, ad esempio un stored procedure che elimina i dati.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="2b5ba-195">Elemento Dependent (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-196">L'elemento **dipendente** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale dipendente di un vincolo di chiave esterna (detto anche vincolo referenziale).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="2b5ba-197">L'elemento **dipendente** specifica la colonna (o le colonne) in una tabella che fa riferimento a una colonna di chiave primaria (o a colonne).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="2b5ba-198">Gli elementi **PropertyRef** specificano le colonne a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="2b5ba-199">L'elemento Principal specifica le colonne chiave primaria a cui fanno riferimento le colonne specificate nell'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="2b5ba-200">L'elemento **dipendente** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-201">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="2b5ba-202">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-203">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-203">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-204">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="2b5ba-205">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-205">Attribute Name</span></span> | <span data-ttu-id="2b5ba-206">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-206">Is Required</span></span> | <span data-ttu-id="2b5ba-207">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-208">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-208">**Role**</span></span>       | <span data-ttu-id="2b5ba-209">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-209">Yes</span></span>         | <span data-ttu-id="2b5ba-210">Lo stesso valore dell'attributo **Role** (se utilizzato) dell'elemento End corrispondente; in caso contrario, il nome della tabella che contiene la colonna di riferimento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-211">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="2b5ba-212">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="2b5ba-213">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-214">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-214">Example</span></span>

<span data-ttu-id="2b5ba-215">Nell'esempio seguente viene illustrato un elemento Association che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="2b5ba-216">L'elemento **dipendente** specifica la colonna **CustomerID** della tabella **Order** come entità finale dipendente del vincolo.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a><span data-ttu-id="2b5ba-217">Elemento Documentation (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-218">L'elemento **Documentation** in Store Schema Definition Language (SSDL) può essere utilizzato per fornire informazioni su un oggetto definito in un elemento padre.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="2b5ba-219">L'elemento **Documentation** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-220">**Riepilogo**: breve descrizione dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="2b5ba-221">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-221">(zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-222">**LongDescription**: Descrizione completa dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="2b5ba-223">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-224">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-224">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-225">All'elemento **Documentation** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="2b5ba-226">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="2b5ba-227">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-228">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-228">Example</span></span>

<span data-ttu-id="2b5ba-229">Nell'esempio seguente viene illustrato l'elemento **Documentation** come elemento figlio di un elemento EntityType.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a><span data-ttu-id="2b5ba-230">Elemento End (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-230">End Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-231">L'elemento **end** in Store Schema Definition Language (SSDL) specifica la tabella e il numero di righe a un'estremità di un vincolo FOREIGN KEY nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="2b5ba-232">L'elemento **end** può essere un elemento figlio dell'elemento Association o dell'elemento associationname.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="2b5ba-233">In entrambi i casi, gli elementi figlio possibili e gli attributi applicabili sono diversi.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="2b5ba-234">Elemento End come figlio dell'elemento Association</span><span class="sxs-lookup"><span data-stu-id="2b5ba-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="2b5ba-235">Un elemento **end** (come figlio dell'elemento **Association** ) specifica la tabella e il numero di righe alla fine di un vincolo FOREIGN KEY con rispettivamente il **tipo** e gli attributi di **molteplicità** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="2b5ba-236">Le entità finali del vincolo di chiave esterna sono definite come parte dell'associazione SSDL. L'associazione SSDL deve disporre esattamente di due entità finali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="2b5ba-237">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-238">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-239">OnDelete (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-240">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-241">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-241">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-242">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **finale** quando è figlio di un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="2b5ba-243">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-243">Attribute Name</span></span>   | <span data-ttu-id="2b5ba-244">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-244">Is Required</span></span> | <span data-ttu-id="2b5ba-245">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-246">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-246">**Type**</span></span>         | <span data-ttu-id="2b5ba-247">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-247">Yes</span></span>         | <span data-ttu-id="2b5ba-248">Il nome completo del set di entità SSDL che si trova in corrispondenza dell'entità finale del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="2b5ba-249">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-249">**Role**</span></span>         | <span data-ttu-id="2b5ba-250">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-250">No</span></span>          | <span data-ttu-id="2b5ba-251">Valore dell'attributo **Role** nell'elemento Principal o dipendente dell'elemento ReferentialConstraint corrispondente (se usato).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="2b5ba-252">**Molteplicità**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-252">**Multiplicity**</span></span> | <span data-ttu-id="2b5ba-253">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-253">Yes</span></span>         | <span data-ttu-id="2b5ba-254">**1**, **0.. 1**o a **\*** seconda del numero di righe che possono trovarsi alla fine del vincolo FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="2b5ba-255">**1** indica che esiste esattamente una riga nell'entità finale del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="2b5ba-256">**0.. 1** indica che l'entità finale del vincolo di chiave esterna è pari a zero o a una riga.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="2b5ba-257">**\*** indica che nell'entità finale del vincolo di chiave esterna sono presenti zero, una o più righe.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-258">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="2b5ba-259">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="2b5ba-260">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="2b5ba-261">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-261">Example</span></span>

<span data-ttu-id="2b5ba-262">Nell'esempio seguente viene illustrato un elemento **Association** che definisce il vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="2b5ba-263">I valori di **molteplicità** specificati in ogni elemento **end** indicano che molte righe della tabella **Orders** possono essere associate a una riga nella tabella **Customers** , ma solo una riga nella tabella **Customers** può essere associata a una riga nella tabella **Orders** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="2b5ba-264">Inoltre, l'elemento **OnDelete** indica che tutte le righe della tabella **Orders** che fanno riferimento a una determinata riga nella tabella **Customers** verranno eliminate se la riga della tabella **Customers** viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="2b5ba-265">Elemento End come figlio dell'elemento AssociationSet</span><span class="sxs-lookup"><span data-stu-id="2b5ba-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="2b5ba-266">L'elemento **end** (come figlio dell'elemento **associationname** ) specifica una tabella in corrispondenza di un'entità finale di un vincolo FOREIGN KEY nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="2b5ba-267">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-268">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-269">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-270">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-270">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-271">Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **finale** quando è figlio di un elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="2b5ba-272">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-272">Attribute Name</span></span> | <span data-ttu-id="2b5ba-273">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-273">Is Required</span></span> | <span data-ttu-id="2b5ba-274">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-275">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-275">**EntitySet**</span></span>  | <span data-ttu-id="2b5ba-276">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-276">Yes</span></span>         | <span data-ttu-id="2b5ba-277">Il nome del set di entità SSDL in corrispondenza dell'entità finale del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="2b5ba-278">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-278">**Role**</span></span>       | <span data-ttu-id="2b5ba-279">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-279">No</span></span>          | <span data-ttu-id="2b5ba-280">Valore di uno degli attributi **Role** specificati in un elemento **end** dell'elemento Association corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-281">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="2b5ba-282">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="2b5ba-283">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="2b5ba-284">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-284">Example</span></span>

<span data-ttu-id="2b5ba-285">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con un elemento di **associazione** con due elementi **end** :</span><span class="sxs-lookup"><span data-stu-id="2b5ba-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="2b5ba-286">Elemento EntityContainer (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-287">Un elemento **EntityContainer** in Store Schema Definition Language (SSDL) descrive la struttura dell'origine dati sottostante in un'applicazione Entity Framework: i set di entità SSDL (definiti negli elementi EntitySet) rappresentano le tabelle in un database, i tipi di entità SSDL (definiti negli elementi EntityType) rappresentano le righe in una tabella e i set di associazioni (definiti in elementi di associazioni) rappresentano vincoli di chiave esterna in un database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="2b5ba-288">Un contenitore di entità del modello di archiviazione esegue il mapping a un contenitore di entità del modello concettuale attraverso l'elemento EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="2b5ba-289">Un elemento **EntityContainer** può avere uno o più elementi di documentazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="2b5ba-290">Se è presente un elemento di **documentazione** , deve precedere tutti gli altri elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="2b5ba-291">Un elemento **EntityContainer** può avere zero o più degli elementi figlio seguenti (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-292">EntitySet</span><span class="sxs-lookup"><span data-stu-id="2b5ba-292">EntitySet</span></span>
-   <span data-ttu-id="2b5ba-293">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="2b5ba-293">AssociationSet</span></span>
-   <span data-ttu-id="2b5ba-294">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="2b5ba-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-295">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-295">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-296">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="2b5ba-297">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-297">Attribute Name</span></span> | <span data-ttu-id="2b5ba-298">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-298">Is Required</span></span> | <span data-ttu-id="2b5ba-299">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-300">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-300">**Name**</span></span>       | <span data-ttu-id="2b5ba-301">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-301">Yes</span></span>         | <span data-ttu-id="2b5ba-302">Nome del contenitore di entità.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-302">The name of the entity container.</span></span> <span data-ttu-id="2b5ba-303">Il nome non può contenere caratteri punto (.).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-304">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="2b5ba-305">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-306">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-307">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-307">Example</span></span>

<span data-ttu-id="2b5ba-308">Nell'esempio seguente viene illustrato un elemento **EntityContainer** che definisce due set di entità e un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="2b5ba-309">I nomi dei tipi di entità e associazione sono qualificati dal nome dello spazio dei nomi del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a><span data-ttu-id="2b5ba-310">Elemento EntitySet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-311">Un elemento **EntitySet** in Store Schema Definition Language (SSDL) rappresenta una tabella o una vista nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="2b5ba-312">Un elemento EntityType in SSDL rappresenta una riga nella tabella o nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="2b5ba-313">L'attributo **EntityType** di un elemento **EntitySet** specifica il particolare tipo di entità SSDL che rappresenta le righe in un set di entità SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="2b5ba-314">Il mapping tra un set di entità CSDL e un set di entità SSDL viene specificato in un elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="2b5ba-315">L'elemento **EntitySet** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-316">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-317">DefiningQuery (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-318">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="2b5ba-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-319">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-319">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-320">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="2b5ba-321">Alcuni attributi (non elencati qui) possono essere qualificati con l'alias del **negozio** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="2b5ba-322">Questi attributi vengono utilizzati dalla procedura guidata Aggiorna modello in caso di aggiornamento di un modello.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="2b5ba-323">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-323">Attribute Name</span></span> | <span data-ttu-id="2b5ba-324">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-324">Is Required</span></span> | <span data-ttu-id="2b5ba-325">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-326">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-326">**Name**</span></span>       | <span data-ttu-id="2b5ba-327">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-327">Yes</span></span>         | <span data-ttu-id="2b5ba-328">Nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="2b5ba-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-329">**EntityType**</span></span> | <span data-ttu-id="2b5ba-330">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-330">Yes</span></span>         | <span data-ttu-id="2b5ba-331">Nome completo del tipo di entità per il quale il set di entità contiene delle istanze.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="2b5ba-332">**Schema**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-332">**Schema**</span></span>     | <span data-ttu-id="2b5ba-333">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-333">No</span></span>          | <span data-ttu-id="2b5ba-334">Schema del database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="2b5ba-335">**Tabella**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-335">**Table**</span></span>      | <span data-ttu-id="2b5ba-336">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-336">No</span></span>          | <span data-ttu-id="2b5ba-337">Tabella del database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="2b5ba-338">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="2b5ba-339">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-340">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-341">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-341">Example</span></span>

<span data-ttu-id="2b5ba-342">Nell'esempio seguente viene illustrato un elemento **EntityContainer** che dispone di due elementi **EntitySet** e di un elemento di **associazione** :</span><span class="sxs-lookup"><span data-stu-id="2b5ba-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="2b5ba-343">Elemento EntityType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-344">Un elemento **EntityType** in Store Schema Definition Language (SSDL) rappresenta una riga in una tabella o vista del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="2b5ba-345">Un elemento EntitySet in SSDL rappresenta la tabella o la vista in cui si trova la riga.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="2b5ba-346">L'attributo **EntityType** di un elemento **EntitySet** specifica il particolare tipo di entità SSDL che rappresenta le righe in un set di entità SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="2b5ba-347">Il mapping tra un tipo di entità SSDL e un tipo di entità CSDL viene specificato in un elemento EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="2b5ba-348">L'elemento **EntityType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-349">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-350">Key (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="2b5ba-351">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="2b5ba-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-352">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-352">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-353">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="2b5ba-354">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-354">Attribute Name</span></span> | <span data-ttu-id="2b5ba-355">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-355">Is Required</span></span> | <span data-ttu-id="2b5ba-356">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-357">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-357">**Name**</span></span>       | <span data-ttu-id="2b5ba-358">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-358">Yes</span></span>         | <span data-ttu-id="2b5ba-359">Nome del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-359">The name of the entity type.</span></span> <span data-ttu-id="2b5ba-360">Questo valore corrisponde generalmente al nome della tabella in cui il tipo di entità rappresenta una riga.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="2b5ba-361">Questo valore non può contenere punti (.).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-362">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="2b5ba-363">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-364">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-365">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-365">Example</span></span>

<span data-ttu-id="2b5ba-366">Nell'esempio seguente viene illustrato un elemento **EntityType** con due proprietà:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-366">The following example shows an **EntityType** element with two properties:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a><span data-ttu-id="2b5ba-367">Elemento Function (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-367">Function Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-368">L'elemento **Function** in Store Schema Definition Language (SSDL) specifica una stored procedure esistente nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="2b5ba-369">L'elemento **Function** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-370">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-371">Parameter (zero o più)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="2b5ba-372">CommandText (zero o uno)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-373">ReturnType (zero o più)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="2b5ba-374">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="2b5ba-375">Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** o l'attributo **returnType** (vedere di seguito), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="2b5ba-376">È possibile importare stored procedure specificate nel modello di archiviazione nel modello concettuale di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="2b5ba-377">Per ulteriori informazioni, vedere [esecuzione di query con stored procedure](xref:ef6/modeling/designer/stored-procedures/query).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="2b5ba-378">L'elemento **Function** può essere utilizzato anche per definire funzioni personalizzate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-379">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-379">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-380">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Function** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="2b5ba-381">Alcuni attributi (non elencati qui) possono essere qualificati con l'alias del **negozio** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="2b5ba-382">Questi attributi vengono utilizzati dalla procedura guidata Aggiorna modello in caso di aggiornamento di un modello.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="2b5ba-383">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-383">Attribute Name</span></span>             | <span data-ttu-id="2b5ba-384">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-384">Is Required</span></span> | <span data-ttu-id="2b5ba-385">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-386">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-386">**Name**</span></span>                   | <span data-ttu-id="2b5ba-387">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-387">Yes</span></span>         | <span data-ttu-id="2b5ba-388">Nome della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="2b5ba-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-389">**ReturnType**</span></span>             | <span data-ttu-id="2b5ba-390">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-390">No</span></span>          | <span data-ttu-id="2b5ba-391">Tipo restituito della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="2b5ba-392">**Aggregata**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-392">**Aggregate**</span></span>              | <span data-ttu-id="2b5ba-393">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-393">No</span></span>          | <span data-ttu-id="2b5ba-394">**True** se il stored procedure restituisce un valore di aggregazione. in caso contrario, **false**.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="2b5ba-395">**BuiltIn**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-395">**BuiltIn**</span></span>                | <span data-ttu-id="2b5ba-396">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-396">No</span></span>          | <span data-ttu-id="2b5ba-397">**True** se la funzione è una funzione incorporata<sup>1</sup> . in caso contrario, **false**.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="2b5ba-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="2b5ba-399">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-399">No</span></span>          | <span data-ttu-id="2b5ba-400">Nome della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="2b5ba-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-401">**NiladicFunction**</span></span>        | <span data-ttu-id="2b5ba-402">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-402">No</span></span>          | <span data-ttu-id="2b5ba-403">**True** se la funzione è una funzione senza parametri<sup>2</sup> . In caso contrario, **false** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="2b5ba-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-404">**IsComposable**</span></span>           | <span data-ttu-id="2b5ba-405">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-405">No</span></span>          | <span data-ttu-id="2b5ba-406">**True** se la funzione è una funzione componibile<sup>3</sup> . In caso contrario, **false** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="2b5ba-407">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="2b5ba-408">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-408">No</span></span>          | <span data-ttu-id="2b5ba-409">Enumerazione che definisce la semantica dei tipi utilizzata per risolvere gli overload della funzione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="2b5ba-410">L'enumerazione è definita nel file manifesto del provider per ogni definizione di funzione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="2b5ba-411">Il valore predefinito è **AllowImplicitConversion**.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="2b5ba-412">**Schema**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-412">**Schema**</span></span>                 | <span data-ttu-id="2b5ba-413">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-413">No</span></span>          | <span data-ttu-id="2b5ba-414">Nome dello schema in cui viene definita la stored procedure.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="2b5ba-415"><sup>1</sup> una funzione predefinita è una funzione definita nel database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="2b5ba-416">Per informazioni sulle funzioni definite nel modello di archiviazione, vedere elemento CommandText (SSDL).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="2b5ba-417"><sup>2</sup> una funzione senza parametri è una funzione che non accetta parametri e, quando viene chiamato, non richiede le parentesi.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="2b5ba-418"><sup>3</sup> due funzioni sono componibili se l'output di una funzione può essere l'input per l'altra funzione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="2b5ba-419">All'elemento **Function** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="2b5ba-420">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-421">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-422">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-422">Example</span></span>

<span data-ttu-id="2b5ba-423">Nell'esempio seguente viene illustrato un elemento **Function** che corrisponde alla stored procedure **UpdateOrderQuantity** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="2b5ba-424">La stored procedure accetta due parametri e non restituisce alcun valore.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-424">The stored procedure accepts two parameters and does not return a value.</span></span>

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a><span data-ttu-id="2b5ba-425">Elemento Key (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-425">Key Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-426">L'elemento **Key** in Store Schema Definition Language (SSDL) rappresenta la chiave primaria di una tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="2b5ba-427">**Key** è un elemento figlio di un elemento EntityType, che rappresenta una riga in una tabella.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="2b5ba-428">La chiave primaria è definita nell'elemento **chiave** facendo riferimento a uno o più elementi Property definiti nell'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="2b5ba-429">L'elemento **Key** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-430">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="2b5ba-431">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="2b5ba-431">Annotation elements</span></span>

<span data-ttu-id="2b5ba-432">Nessun attributo applicabile all'elemento **chiave** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-433">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-433">Example</span></span>

<span data-ttu-id="2b5ba-434">Nell'esempio seguente viene illustrato un elemento **EntityType** con una chiave che fa riferimento a una proprietà:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="2b5ba-435">Elemento OnDelete (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-436">L'elemento **OnDelete** in Store Schema Definition Language (SSDL) riflette il comportamento del database quando viene eliminata una riga che fa parte di un vincolo FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="2b5ba-437">Se l'azione è impostata su **Cascade**, verranno eliminate anche le righe che fanno riferimento a una riga in fase di eliminazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="2b5ba-438">Se l'azione è impostata su **None**, non vengono eliminate anche le righe che fanno riferimento a una riga eliminata.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="2b5ba-439">Un elemento **OnDelete** è un elemento figlio di un elemento End.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="2b5ba-440">Un elemento **OnDelete** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-441">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-442">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-443">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-443">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-444">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="2b5ba-445">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-445">Attribute Name</span></span> | <span data-ttu-id="2b5ba-446">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-446">Is Required</span></span> | <span data-ttu-id="2b5ba-447">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-448">**Azione**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-448">**Action**</span></span>     | <span data-ttu-id="2b5ba-449">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-449">Yes</span></span>         | <span data-ttu-id="2b5ba-450">**Cascade** o **None**.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-450">**Cascade** or **None**.</span></span> <span data-ttu-id="2b5ba-451">(Il valore con **restrizioni** è valido ma ha lo stesso comportamento di **None**).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-452">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="2b5ba-453">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-454">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-455">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-455">Example</span></span>

<span data-ttu-id="2b5ba-456">Nell'esempio seguente viene illustrato un elemento **Association** che definisce il vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="2b5ba-457">L'elemento **OnDelete** indica che tutte le righe della tabella **Orders** che fanno riferimento a una determinata riga della tabella **Customers** verranno eliminate se la riga della tabella **Customers** viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a><span data-ttu-id="2b5ba-458">Elemento Parameter (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-459">L'elemento **Parameter** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento Function che specifica i parametri per un stored procedure nel database.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="2b5ba-460">L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-461">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-462">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-463">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-463">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-464">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="2b5ba-465">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-465">Attribute Name</span></span> | <span data-ttu-id="2b5ba-466">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-466">Is Required</span></span> | <span data-ttu-id="2b5ba-467">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-468">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-468">**Name**</span></span>       | <span data-ttu-id="2b5ba-469">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-469">Yes</span></span>         | <span data-ttu-id="2b5ba-470">Nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="2b5ba-471">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-471">**Type**</span></span>       | <span data-ttu-id="2b5ba-472">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-472">Yes</span></span>         | <span data-ttu-id="2b5ba-473">Tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="2b5ba-474">**Modalità**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-474">**Mode**</span></span>       | <span data-ttu-id="2b5ba-475">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-475">No</span></span>          | <span data-ttu-id="2b5ba-476">**In**, **out**o **InOut** a seconda che il parametro sia un parametro di input, di output o di input/output.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="2b5ba-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-477">**MaxLength**</span></span>  | <span data-ttu-id="2b5ba-478">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-478">No</span></span>          | <span data-ttu-id="2b5ba-479">Lunghezza massima del parametro.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="2b5ba-480">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-480">**Precision**</span></span>  | <span data-ttu-id="2b5ba-481">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-481">No</span></span>          | <span data-ttu-id="2b5ba-482">Precisione del parametro</span><span class="sxs-lookup"><span data-stu-id="2b5ba-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="2b5ba-483">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-483">**Scale**</span></span>      | <span data-ttu-id="2b5ba-484">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-484">No</span></span>          | <span data-ttu-id="2b5ba-485">Scalabilità del parametro</span><span class="sxs-lookup"><span data-stu-id="2b5ba-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="2b5ba-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-486">**SRID**</span></span>       | <span data-ttu-id="2b5ba-487">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-487">No</span></span>          | <span data-ttu-id="2b5ba-488">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="2b5ba-489">Valido solo per i parametri dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="2b5ba-490">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-491">All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="2b5ba-492">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-493">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-494">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-494">Example</span></span>

<span data-ttu-id="2b5ba-495">Nell'esempio seguente viene illustrato un elemento **Function** con due elementi **Parameter** che specificano i parametri di input:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a><span data-ttu-id="2b5ba-496">Elemento Principal (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-497">L'elemento **Principal** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale principale di un vincolo FOREIGN KEY (detto anche vincolo referenziale).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="2b5ba-498">L'elemento **Principal** specifica la colonna o le colonne di chiave primaria in una tabella a cui fa riferimento un'altra colonna o colonne.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="2b5ba-499">Gli elementi **PropertyRef** specificano le colonne a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="2b5ba-500">L'elemento dipendente specifica le colonne che fanno riferimento alle colonne di chiave primaria specificate nell'elemento **Principal** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="2b5ba-501">L'elemento **Principal** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="2b5ba-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="2b5ba-502">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="2b5ba-503">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-504">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-504">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-505">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Principal** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="2b5ba-506">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-506">Attribute Name</span></span> | <span data-ttu-id="2b5ba-507">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-507">Is Required</span></span> | <span data-ttu-id="2b5ba-508">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-509">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-509">**Role**</span></span>       | <span data-ttu-id="2b5ba-510">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-510">Yes</span></span>         | <span data-ttu-id="2b5ba-511">Lo stesso valore dell'attributo **Role** (se utilizzato) dell'elemento End corrispondente; in caso contrario, il nome della tabella che contiene la colonna a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-512">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **principale** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="2b5ba-513">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="2b5ba-514">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-515">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-515">Example</span></span>

<span data-ttu-id="2b5ba-516">Nell'esempio seguente viene illustrato un elemento Association che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="2b5ba-517">L'elemento **Principal** specifica la colonna **CustomerID** della tabella **Customer** come entità finale principale del vincolo.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a><span data-ttu-id="2b5ba-518">Elemento Property (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-518">Property Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-519">L'elemento **Property** in Store Schema Definition Language (SSDL) rappresenta una colonna in una tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="2b5ba-520">Gli elementi **Proprietà** sono elementi figlio degli elementi EntityType, che rappresentano le righe in una tabella.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="2b5ba-521">Ogni elemento **Property** definito in un elemento **EntityType** rappresenta una colonna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="2b5ba-522">Un elemento **Property** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-523">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-523">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-524">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="2b5ba-525">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-525">Attribute Name</span></span>            | <span data-ttu-id="2b5ba-526">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-526">Is Required</span></span> | <span data-ttu-id="2b5ba-527">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-528">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-528">**Name**</span></span>                  | <span data-ttu-id="2b5ba-529">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-529">Yes</span></span>         | <span data-ttu-id="2b5ba-530">Nome della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="2b5ba-531">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-531">**Type**</span></span>                  | <span data-ttu-id="2b5ba-532">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-532">Yes</span></span>         | <span data-ttu-id="2b5ba-533">Tipo della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="2b5ba-534">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-534">**Nullable**</span></span>              | <span data-ttu-id="2b5ba-535">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-535">No</span></span>          | <span data-ttu-id="2b5ba-536">**True** (valore predefinito) o **false** a seconda che la colonna corrispondente possa avere un valore null.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="2b5ba-537">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-537">**DefaultValue**</span></span>          | <span data-ttu-id="2b5ba-538">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-538">No</span></span>          | <span data-ttu-id="2b5ba-539">Valore predefinito della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="2b5ba-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-540">**MaxLength**</span></span>             | <span data-ttu-id="2b5ba-541">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-541">No</span></span>          | <span data-ttu-id="2b5ba-542">Lunghezza massima della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="2b5ba-543">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-543">**FixedLength**</span></span>           | <span data-ttu-id="2b5ba-544">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-544">No</span></span>          | <span data-ttu-id="2b5ba-545">**True** o **false** a seconda che il valore della colonna corrispondente venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="2b5ba-546">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-546">**Precision**</span></span>             | <span data-ttu-id="2b5ba-547">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-547">No</span></span>          | <span data-ttu-id="2b5ba-548">Precisione della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="2b5ba-549">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-549">**Scale**</span></span>                 | <span data-ttu-id="2b5ba-550">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-550">No</span></span>          | <span data-ttu-id="2b5ba-551">Scala della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="2b5ba-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-552">**Unicode**</span></span>               | <span data-ttu-id="2b5ba-553">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-553">No</span></span>          | <span data-ttu-id="2b5ba-554">**True** o **false** a seconda che il valore della colonna corrispondente venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="2b5ba-555">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-555">**Collation**</span></span>             | <span data-ttu-id="2b5ba-556">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-556">No</span></span>          | <span data-ttu-id="2b5ba-557">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="2b5ba-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-558">**SRID**</span></span>                  | <span data-ttu-id="2b5ba-559">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-559">No</span></span>          | <span data-ttu-id="2b5ba-560">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="2b5ba-561">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="2b5ba-562">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="2b5ba-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="2b5ba-564">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-564">No</span></span>          | <span data-ttu-id="2b5ba-565">**None**, **Identity** (se il valore della colonna corrispondente è un'identità generata nel database) o **calcolata** (se il valore della colonna corrispondente viene calcolato nel database).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="2b5ba-566">Non valido per le proprietà RowType.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-567">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="2b5ba-568">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-569">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-570">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-570">Example</span></span>

<span data-ttu-id="2b5ba-571">Nell'esempio seguente viene illustrato un elemento **EntityType** con due elementi **Proprietà** figlio:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="2b5ba-572">Elemento PropertyRef (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-573">L'elemento **PropertyRef** in Store Schema Definition Language (SSDL) fa riferimento a una proprietà definita in un elemento EntityType per indicare che la proprietà eseguirà uno dei ruoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="2b5ba-574">Far parte della chiave primaria della tabella rappresentata da **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="2b5ba-575">Per definire una chiave primaria, è possibile usare uno o più elementi **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="2b5ba-576">Per ulteriori informazioni, vedere Elemento Key.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="2b5ba-577">Rappresenterà l'entità finale dipendente o principale di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="2b5ba-578">Per ulteriori informazioni, vedere Elemento ReferentialConstraint.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="2b5ba-579">L'elemento **PropertyRef** può avere solo gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="2b5ba-580">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-581">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="2b5ba-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-582">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-582">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-583">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="2b5ba-584">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-584">Attribute Name</span></span> | <span data-ttu-id="2b5ba-585">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-585">Is Required</span></span> | <span data-ttu-id="2b5ba-586">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="2b5ba-587">**Nome**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-587">**Name**</span></span>       | <span data-ttu-id="2b5ba-588">Sì</span><span class="sxs-lookup"><span data-stu-id="2b5ba-588">Yes</span></span>         | <span data-ttu-id="2b5ba-589">Nome della proprietà alla quale viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="2b5ba-590">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="2b5ba-591">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="2b5ba-592">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-593">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-593">Example</span></span>

<span data-ttu-id="2b5ba-594">Nell'esempio seguente viene illustrato un elemento **PropertyRef** utilizzato per definire una chiave primaria facendo riferimento a una proprietà definita in un elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="2b5ba-595">Elemento ReferentialConstraint (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-596">L'elemento **ReferentialConstraint** in Store Schema Definition Language (SSDL) rappresenta un vincolo di chiave esterna (detto anche vincolo di integrità referenziale) nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="2b5ba-597">Le estremità principale e dipendente del vincolo vengono specificate rispettivamente dagli elementi figlio Principal e Dependent.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="2b5ba-598">Alle colonne che fanno parte delle estremità principale e dipendente viene fatto riferimento tramite gli elementi PropertyRef.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="2b5ba-599">L'elemento **ReferentialConstraint** è un elemento figlio facoltativo dell'elemento Association.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="2b5ba-600">Se non viene utilizzato un elemento **ReferentialConstraint** per eseguire il mapping del vincolo foreign key specificato nell'elemento **Association** , a tale scopo è necessario utilizzare un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="2b5ba-601">L'elemento **ReferentialConstraint** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="2b5ba-602">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="2b5ba-603">Principal (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="2b5ba-604">Dependent (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="2b5ba-605">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-606">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-606">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-607">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="2b5ba-608">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-609">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-610">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-610">Example</span></span>

<span data-ttu-id="2b5ba-611">Nell'esempio seguente viene illustrato un elemento **Association** che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* :</span><span class="sxs-lookup"><span data-stu-id="2b5ba-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a><span data-ttu-id="2b5ba-612">Elemento ReturnType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-613">L'elemento **returnType** in Store Schema Definition Language (SSDL) specifica il tipo restituito per una funzione definita in un elemento **Function** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="2b5ba-614">Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="2b5ba-615">Il tipo restituito di una funzione viene specificato con l'attributo **Type** o l'elemento **returnType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="2b5ba-616">L'elemento **returnType** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="2b5ba-617">CollectionType (uno)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="2b5ba-618">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="2b5ba-619">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-620">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-621">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-621">Example</span></span>

<span data-ttu-id="2b5ba-622">Nell'esempio seguente viene utilizzata una **funzione** che restituisce una raccolta di righe.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-622">The following example uses a **Function** that returns a collection of rows.</span></span>

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="2b5ba-623">Elemento RowType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-624">Un elemento **RowType** in Store Schema Definition Language (SSDL) definisce una struttura senza nome come tipo restituito per una funzione definita nell'archivio.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="2b5ba-625">Un elemento **RowType** è l'elemento figlio dell'elemento **CollectionType** :</span><span class="sxs-lookup"><span data-stu-id="2b5ba-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="2b5ba-626">Un elemento **RowType** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="2b5ba-627">Property (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="2b5ba-628">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-628">Example</span></span>

<span data-ttu-id="2b5ba-629">Nell'esempio seguente viene illustrata una funzione di archiviazione che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a><span data-ttu-id="2b5ba-630">Elemento Schema (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="2b5ba-631">L'elemento **schema** nel Store Schema Definition Language (SSDL) è l'elemento radice di una definizione del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="2b5ba-632">Contiene definizioni per gli oggetti, le funzioni e i contenitori che costituiscono un modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="2b5ba-633">L'elemento **schema** può contenere zero o più degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="2b5ba-634">Association Rules</span><span class="sxs-lookup"><span data-stu-id="2b5ba-634">Association</span></span>
-   <span data-ttu-id="2b5ba-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="2b5ba-635">EntityType</span></span>
-   <span data-ttu-id="2b5ba-636">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="2b5ba-636">EntityContainer</span></span>
-   <span data-ttu-id="2b5ba-637">Funzione</span><span class="sxs-lookup"><span data-stu-id="2b5ba-637">Function</span></span>

<span data-ttu-id="2b5ba-638">L'elemento **schema** utilizza l'attributo **namespace** per definire lo spazio dei nomi per il tipo di entità e gli oggetti di associazione in un modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="2b5ba-639">All'interno di uno spazio dei nomi due oggetti non possono avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="2b5ba-640">Uno spazio dei nomi del modello di archiviazione è diverso dallo spazio dei nomi XML dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="2b5ba-641">Uno spazio dei nomi del modello di archiviazione (definito dall'attributo **namespace** ) è un contenitore logico per tipi di entità e tipi di associazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="2b5ba-642">Lo spazio dei nomi XML, indicato dall'attributo **xmlns** , di un elemento **schema** è lo spazio dei nomi predefinito per gli elementi figlio e gli attributi dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="2b5ba-643">Gli spazi dei nomi XML del form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (dove aaaa e mm rappresentano rispettivamente l'anno e il mese) sono riservati per SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="2b5ba-644">Gli elementi e gli attributi personalizzati non possono essere in spazi dei nomi che hanno questo form.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="2b5ba-645">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="2b5ba-645">Applicable Attributes</span></span>

<span data-ttu-id="2b5ba-646">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="2b5ba-647">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="2b5ba-647">Attribute Name</span></span>            | <span data-ttu-id="2b5ba-648">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-648">Is Required</span></span> | <span data-ttu-id="2b5ba-649">Valore</span><span class="sxs-lookup"><span data-stu-id="2b5ba-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-650">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-650">**Namespace**</span></span>             | <span data-ttu-id="2b5ba-651">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-651">Yes</span></span>         | <span data-ttu-id="2b5ba-652">Spazio dei nomi del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-652">The namespace of the storage model.</span></span> <span data-ttu-id="2b5ba-653">Il valore dell'attributo **namespace** viene utilizzato per formare il nome completo di un tipo.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="2b5ba-654">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi ExampleModel. Store, il nome completo di **EntityType** sarà ExampleModel. Store. Customer.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="2b5ba-655">Non è possibile utilizzare le seguenti stringhe come valore per l'attributo **namespace** : **System**, **Transient**o **EDM**.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="2b5ba-656">Il valore dell'attributo **namespace** non può corrispondere al valore dell'attributo **namespace** nell'elemento schema CSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="2b5ba-657">**Alias**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-657">**Alias**</span></span>                 | <span data-ttu-id="2b5ba-658">No</span><span class="sxs-lookup"><span data-stu-id="2b5ba-658">No</span></span>          | <span data-ttu-id="2b5ba-659">Identificatore utilizzato al posto del nome dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="2b5ba-660">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi ExampleModel. Store e il valore dell'attributo **alias** è *StorageModel*, è possibile utilizzare StorageModel. Customer come nome completo dell'elemento **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="2b5ba-661">**Provider**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-661">**Provider**</span></span>              | <span data-ttu-id="2b5ba-662">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-662">Yes</span></span>         | <span data-ttu-id="2b5ba-663">Provider di dati.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="2b5ba-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="2b5ba-665">Yes</span><span class="sxs-lookup"><span data-stu-id="2b5ba-665">Yes</span></span>         | <span data-ttu-id="2b5ba-666">Token che indica al provider quale manifesto del provider restituire.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="2b5ba-667">Non è definito alcun formato per il token.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-667">No format for the token is defined.</span></span> <span data-ttu-id="2b5ba-668">I valori per il token sono definiti dal provider.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="2b5ba-669">Per informazioni sui token del manifesto del provider SQL Server, vedere SqlClient per Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="2b5ba-670">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-670">Example</span></span>

<span data-ttu-id="2b5ba-671">Nell'esempio seguente viene illustrato un elemento **dello schema** che contiene un elemento **EntityContainer** , due elementi **EntityType** e un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a><span data-ttu-id="2b5ba-672">Attributi di annotazione</span><span class="sxs-lookup"><span data-stu-id="2b5ba-672">Annotation Attributes</span></span>

<span data-ttu-id="2b5ba-673">Gli attributi di annotazione in Store Schema Definition Language (SSDL) sono attributi XML personalizzati nel modello di archiviazione che forniscono metadati aggiuntivi sugli elementi del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="2b5ba-674">Oltre ad avere una struttura XML valida, agli attributi di annotazione si applicano i vincoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="2b5ba-675">Gli attributi di annotazione non devono trovarsi in spazi dei nomi XML riservati a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="2b5ba-676">I nomi completi di due attributi di annotazione non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="2b5ba-677">È possibile applicare più attributi di annotazione a un determinato elemento SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="2b5ba-678">È possibile accedere ai metadati contenuti negli elementi Annotation in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-679">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-679">Example</span></span>

<span data-ttu-id="2b5ba-680">Nell'esempio seguente viene illustrato un elemento EntityType con un attributo annotation applicato alla proprietà **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="2b5ba-681">Nell'esempio viene inoltre mostrato un elemento Annotation aggiunto all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-681">The example also show an annotation element added to the **EntityType** element.</span></span>

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="2b5ba-682">Elementi Annotation (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="2b5ba-683">Gli elementi Annotation in SSDL (Store Schema Definition Language) sono elementi XML personalizzati nel modello di archiviazione che forniscono metadati aggiuntivi sul modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="2b5ba-684">Oltre ad avere una struttura XML valida, agli elementi Annotation si applicano i vincoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="2b5ba-685">Gli elementi Annotation non devono trovarsi in spazi dei nomi XML riservati a SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="2b5ba-686">I nomi completi di due elementi Annotation non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="2b5ba-687">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio di un dato elemento SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="2b5ba-688">Più elementi Annotation possono essere figli di un dato elemento SSDL.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="2b5ba-689">A partire da .NET Framework versione 4, è possibile accedere ai metadati contenuti negli elementi di annotazione in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="2b5ba-690">Esempio</span><span class="sxs-lookup"><span data-stu-id="2b5ba-690">Example</span></span>

<span data-ttu-id="2b5ba-691">Nell'esempio seguente viene illustrato un elemento EntityType con un elemento Annotation (**CustomElement**).</span><span class="sxs-lookup"><span data-stu-id="2b5ba-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="2b5ba-692">Nell'esempio viene inoltre illustrato un attributo di annotazione applicato alla proprietà **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a><span data-ttu-id="2b5ba-693">Facet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="2b5ba-693">Facets (SSDL)</span></span>

<span data-ttu-id="2b5ba-694">I facet in Store Schema Definition Language (SSDL) rappresentano dei vincoli sui tipi di colonna specificati in elementi Property.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="2b5ba-695">I facet vengono implementati come attributi XML sugli elementi della **Proprietà** .</span><span class="sxs-lookup"><span data-stu-id="2b5ba-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="2b5ba-696">Nella tabella seguente vengono descritti i facet supportati in SSDL:</span><span class="sxs-lookup"><span data-stu-id="2b5ba-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="2b5ba-697">Facet</span><span class="sxs-lookup"><span data-stu-id="2b5ba-697">Facet</span></span>           | <span data-ttu-id="2b5ba-698">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2b5ba-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2b5ba-699">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-699">**Collation**</span></span>   | <span data-ttu-id="2b5ba-700">Specifica la sequenza di ordinamento da usare quando si eseguono operazioni di confronto e di ordinamento su valori della proprietà.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="2b5ba-701">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-701">**FixedLength**</span></span> | <span data-ttu-id="2b5ba-702">Specifica se la lunghezza del valore della colonna può variare.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="2b5ba-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-703">**MaxLength**</span></span>   | <span data-ttu-id="2b5ba-704">Specifica la lunghezza massima del valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="2b5ba-705">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-705">**Precision**</span></span>   | <span data-ttu-id="2b5ba-706">Per le proprietà di tipo **Decimal**, specifica il numero di cifre che un valore della proprietà può avere.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="2b5ba-707">Per le proprietà di tipo **Time**, **DateTime**e **DateTimeOffset**, specifica il numero di cifre per la parte frazionaria dei secondi del valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="2b5ba-708">**Ridimensionamento**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-708">**Scale**</span></span>       | <span data-ttu-id="2b5ba-709">Specifica il numero di cifre a destra del separatore decimale per il valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="2b5ba-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="2b5ba-710">**Unicode**</span></span>     | <span data-ttu-id="2b5ba-711">Indica se il valore della colonna viene archiviato come Unicode.</span><span class="sxs-lookup"><span data-stu-id="2b5ba-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
