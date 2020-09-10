---
title: Specifica SSDL-EF6
description: Specifica SSDL in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: ab50579649c2e1b19d113cd127e52be995516e27
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620594"
---
# <a name="ssdl-specification"></a><span data-ttu-id="39baa-103">Specifica SSDL</span><span class="sxs-lookup"><span data-stu-id="39baa-103">SSDL Specification</span></span>
<span data-ttu-id="39baa-104">Store Schema Definition Language (SSDL) è un linguaggio basato su XML che descrive il modello di archiviazione di un'applicazione Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="39baa-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="39baa-105">In un'applicazione Entity Framework i metadati del modello di archiviazione vengono caricati da un file con estensione SSDL (scritto in SSDL) in un'istanza di System. Data. Metadata. Edm. StoreItemCollection ed è possibile accedervi usando i metodi della classe System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="39baa-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="39baa-106">Entity Framework usa i metadati del modello di archiviazione per tradurre le query sul modello concettuale in comandi specifici dell'archivio.</span><span class="sxs-lookup"><span data-stu-id="39baa-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="39baa-107">Il Entity Framework Designer (EF designer) archivia le informazioni sul modello di archiviazione in un file con estensione edmx in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="39baa-108">In fase di compilazione il Entity Designer utilizza le informazioni contenute in un file con estensione edmx per creare il file con estensione SSDL necessario per Entity Framework in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="39baa-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="39baa-109">Le versioni di SSDL si differenziano tra loro per gli spazi dei nomi XML.</span><span class="sxs-lookup"><span data-stu-id="39baa-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="39baa-110">Versione SSDL</span><span class="sxs-lookup"><span data-stu-id="39baa-110">SSDL Version</span></span> | <span data-ttu-id="39baa-111">Spazio dei nomi XML</span><span class="sxs-lookup"><span data-stu-id="39baa-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="39baa-112">SSDL V1</span><span class="sxs-lookup"><span data-stu-id="39baa-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="39baa-113">SSDL V2</span><span class="sxs-lookup"><span data-stu-id="39baa-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="39baa-114">SSDL V3</span><span class="sxs-lookup"><span data-stu-id="39baa-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="39baa-115">Elemento Association (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-115">Association Element (SSDL)</span></span>

<span data-ttu-id="39baa-116">Un elemento **Association** in Store Schema Definition Language (SSDL) specifica le colonne della tabella che fanno parte di un vincolo FOREIGN KEY nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="39baa-117">Due elementi End figlio obbligatori specificano le tabelle che costituiscono le entità finali dell'associazione e la molteplicità di ciascuna entità finale.</span><span class="sxs-lookup"><span data-stu-id="39baa-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="39baa-118">Un elemento ReferentialConstraint facoltativo specifica le entità finali principale e dipendente dell'associazione, nonché le colonne coinvolte.</span><span class="sxs-lookup"><span data-stu-id="39baa-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="39baa-119">Se non è presente alcun elemento **ReferentialConstraint** , è necessario utilizzare un elemento AssociationSetMapping per specificare i mapping delle colonne per l'associazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="39baa-120">L'elemento **Association** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-121">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-122">End (esattamente due elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-122">End (exactly two)</span></span>
-   <span data-ttu-id="39baa-123">ReferentialConstraint (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="39baa-124">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-125">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-125">Applicable Attributes</span></span>

<span data-ttu-id="39baa-126">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="39baa-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="39baa-127">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-127">Attribute Name</span></span> | <span data-ttu-id="39baa-128">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-128">Is Required</span></span> | <span data-ttu-id="39baa-129">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-130">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-130">**Name**</span></span>       | <span data-ttu-id="39baa-131">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-131">Yes</span></span>         | <span data-ttu-id="39baa-132">Il nome del vincolo di chiave esterna corrispondente nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-133">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="39baa-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="39baa-134">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-135">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-136">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-136">Example</span></span>

<span data-ttu-id="39baa-137">Nell'esempio seguente viene illustrato un elemento **Association** che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* :</span><span class="sxs-lookup"><span data-stu-id="39baa-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="39baa-138">Elemento AssociationSet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="39baa-139">L'elemento **associativo** in Store Schema Definition Language (SSDL) rappresenta un vincolo di chiave esterna tra due tabelle nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="39baa-140">Le colonne della tabella che fanno parte del vincolo di chiave esterna vengono specificate in un elemento Association.</span><span class="sxs-lookup"><span data-stu-id="39baa-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="39baa-141">L'elemento **Association** che corrisponde a **un elemento di associazione specificato è** specificato nell'attributo **Association** dell'elemento associationname. **AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="39baa-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="39baa-142">Il mapping dei set di associazioni SSDL viene eseguito in set di associazioni CSDL da un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="39baa-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="39baa-143">Tuttavia, se l'associazione CSDL per un determinato set di associazioni CSDL viene definita utilizzando un elemento ReferentialConstraint, non è necessario alcun elemento **AssociationSetMapping** corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="39baa-144">In questo caso, se è presente un elemento **AssociationSetMapping** , i mapping che definisce verranno sottoposti a override dall'elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="39baa-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="39baa-145">L'elemento **associationname** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-146">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-147">End (zero o due elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-147">End (zero or two)</span></span>
-   <span data-ttu-id="39baa-148">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-149">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-149">Applicable Attributes</span></span>

<span data-ttu-id="39baa-150">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="39baa-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="39baa-151">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-151">Attribute Name</span></span>  | <span data-ttu-id="39baa-152">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-152">Is Required</span></span> | <span data-ttu-id="39baa-153">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-154">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-154">**Name**</span></span>        | <span data-ttu-id="39baa-155">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-155">Yes</span></span>         | <span data-ttu-id="39baa-156">Nome del vincolo di chiave esterna rappresentato dal set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="39baa-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="39baa-157">**Associazione**</span><span class="sxs-lookup"><span data-stu-id="39baa-157">**Association**</span></span> | <span data-ttu-id="39baa-158">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-158">Yes</span></span>         | <span data-ttu-id="39baa-159">Nome dell'associazione che definisce le colonne che fanno parte del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="39baa-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-160">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="39baa-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="39baa-161">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-162">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-163">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-163">Example</span></span>

<span data-ttu-id="39baa-164">Nell'esempio seguente viene illustrato un elemento di **associazione** che rappresenta il `FK_CustomerOrders` vincolo di chiave esterna nel database sottostante:</span><span class="sxs-lookup"><span data-stu-id="39baa-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="39baa-165">Elemento CollectionType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="39baa-166">L'elemento **CollectionType** in Store Schema Definition Language (SSDL) specifica che il tipo restituito di una funzione è una raccolta.</span><span class="sxs-lookup"><span data-stu-id="39baa-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="39baa-167">L'elemento **CollectionType** è figlio dell'elemento ReturnType.</span><span class="sxs-lookup"><span data-stu-id="39baa-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="39baa-168">Il tipo di raccolta viene specificato tramite l'elemento figlio RowType:</span><span class="sxs-lookup"><span data-stu-id="39baa-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="39baa-169">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="39baa-170">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-171">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-172">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-172">Example</span></span>

<span data-ttu-id="39baa-173">Nell'esempio seguente viene illustrata una funzione che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe.</span><span class="sxs-lookup"><span data-stu-id="39baa-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="39baa-174">Elemento CommandText (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="39baa-175">L'elemento **CommandText** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento Function che consente di definire un'istruzione SQL che viene eseguita nel database.</span><span class="sxs-lookup"><span data-stu-id="39baa-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="39baa-176">L'elemento **CommandText** consente di aggiungere una funzionalità simile a una stored procedure nel database, ma l'elemento **CommandText** viene definito nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="39baa-177">L'elemento **CommandText** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="39baa-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="39baa-178">Il corpo dell'elemento **CommandText** deve essere un'istruzione SQL valida per il database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="39baa-179">Nessun attributo applicabile all'elemento **CommandText** .</span><span class="sxs-lookup"><span data-stu-id="39baa-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-180">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-180">Example</span></span>

<span data-ttu-id="39baa-181">Nell'esempio seguente viene illustrato un elemento **Function** con un elemento **CommandText** figlio.</span><span class="sxs-lookup"><span data-stu-id="39baa-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="39baa-182">Esporre la funzione **UpdateProductInOrder** come metodo in ObjectContext mediante l'importazione nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="39baa-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="39baa-183">Elemento DefiningQuery (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="39baa-184">L'elemento **DefiningQuery** in Store Schema Definition Language (SSDL) consente di eseguire un'istruzione SQL direttamente nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="39baa-185">L'elemento **DefiningQuery** viene comunemente utilizzato come una vista di database, ma la vista è definita nel modello di archiviazione anziché nel database.</span><span class="sxs-lookup"><span data-stu-id="39baa-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="39baa-186">La vista definita in un elemento **DefiningQuery** può essere mappata a un tipo di entità nel modello concettuale tramite un elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="39baa-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="39baa-187">Questi mapping sono di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="39baa-187">These mappings are read-only.</span></span>  

<span data-ttu-id="39baa-188">Nella sintassi SSDL seguente viene illustrata la dichiarazione di un oggetto **EntitySet** seguito dall'elemento **DefiningQuery** che contiene una query utilizzata per recuperare la visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="39baa-189">È possibile utilizzare le stored procedure nel Entity Framework per abilitare gli scenari di lettura e scrittura sulle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="39baa-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="39baa-190">È possibile utilizzare una vista origine dati o una vista Entity SQL come tabella di base per il recupero dei dati e per l'elaborazione delle modifiche da parte delle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="39baa-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="39baa-191">È possibile usare l'elemento **DefiningQuery** per la destinazione Microsoft SQL Server Compact 3,5.</span><span class="sxs-lookup"><span data-stu-id="39baa-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="39baa-192">Sebbene SQL Server Compact 3,5 non supporti stored procedure, è possibile implementare una funzionalità simile con l'elemento **DefiningQuery** .</span><span class="sxs-lookup"><span data-stu-id="39baa-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="39baa-193">Un'altra situazione in cui può essere utile è nella creazione di stored procedure per risolvere una mancata corrispondenza tra i tipi di dati utilizzati nel linguaggio di programmazione e quelli dell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="39baa-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="39baa-194">È possibile scrivere un **DefiningQuery** che accetta un determinato set di parametri e quindi chiama un stored procedure con un set di parametri diverso, ad esempio un stored procedure che elimina i dati.</span><span class="sxs-lookup"><span data-stu-id="39baa-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="39baa-195">Elemento Dependent (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="39baa-196">L'elemento **dipendente** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale dipendente di un vincolo di chiave esterna (detto anche vincolo referenziale).</span><span class="sxs-lookup"><span data-stu-id="39baa-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="39baa-197">L'elemento **dipendente** specifica la colonna (o le colonne) in una tabella che fa riferimento a una colonna di chiave primaria (o a colonne).</span><span class="sxs-lookup"><span data-stu-id="39baa-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="39baa-198">Gli elementi **PropertyRef** specificano le colonne a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="39baa-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="39baa-199">L'elemento Principal specifica le colonne chiave primaria a cui fanno riferimento le colonne specificate nell'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="39baa-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="39baa-200">L'elemento **dipendente** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-201">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="39baa-202">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-203">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-203">Applicable Attributes</span></span>

<span data-ttu-id="39baa-204">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="39baa-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="39baa-205">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-205">Attribute Name</span></span> | <span data-ttu-id="39baa-206">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-206">Is Required</span></span> | <span data-ttu-id="39baa-207">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-208">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="39baa-208">**Role**</span></span>       | <span data-ttu-id="39baa-209">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-209">Yes</span></span>         | <span data-ttu-id="39baa-210">Lo stesso valore dell'attributo **Role** (se utilizzato) dell'elemento End corrispondente; in caso contrario, il nome della tabella che contiene la colonna di riferimento.</span><span class="sxs-lookup"><span data-stu-id="39baa-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-211">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **dipendente** .</span><span class="sxs-lookup"><span data-stu-id="39baa-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="39baa-212">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="39baa-213">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-214">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-214">Example</span></span>

<span data-ttu-id="39baa-215">Nell'esempio seguente viene illustrato un elemento Association che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="39baa-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="39baa-216">L'elemento **dipendente** specifica la colonna **CustomerID** della tabella **Order** come entità finale dipendente del vincolo.</span><span class="sxs-lookup"><span data-stu-id="39baa-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="39baa-217">Elemento Documentation (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="39baa-218">L'elemento **Documentation** in Store Schema Definition Language (SSDL) può essere utilizzato per fornire informazioni su un oggetto definito in un elemento padre.</span><span class="sxs-lookup"><span data-stu-id="39baa-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="39baa-219">L'elemento **Documentation** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-220">**Riepilogo**: breve descrizione dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="39baa-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="39baa-221">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="39baa-221">(zero or one element)</span></span>
-   <span data-ttu-id="39baa-222">**LongDescription**: Descrizione completa dell'elemento padre.</span><span class="sxs-lookup"><span data-stu-id="39baa-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="39baa-223">Zero o un elemento.</span><span class="sxs-lookup"><span data-stu-id="39baa-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-224">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-224">Applicable Attributes</span></span>

<span data-ttu-id="39baa-225">All'elemento **Documentation** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="39baa-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="39baa-226">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="39baa-227">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-228">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-228">Example</span></span>

<span data-ttu-id="39baa-229">Nell'esempio seguente viene illustrato l'elemento **Documentation** come elemento figlio di un elemento EntityType.</span><span class="sxs-lookup"><span data-stu-id="39baa-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="39baa-230">Elemento End (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-230">End Element (SSDL)</span></span>

<span data-ttu-id="39baa-231">L'elemento **end** in Store Schema Definition Language (SSDL) specifica la tabella e il numero di righe a un'estremità di un vincolo FOREIGN KEY nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="39baa-232">L'elemento **end** può essere un elemento figlio dell'elemento Association o dell'elemento associationname.</span><span class="sxs-lookup"><span data-stu-id="39baa-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="39baa-233">In entrambi i casi, gli elementi figlio possibili e gli attributi applicabili sono diversi.</span><span class="sxs-lookup"><span data-stu-id="39baa-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="39baa-234">Elemento End come figlio dell'elemento Association</span><span class="sxs-lookup"><span data-stu-id="39baa-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="39baa-235">Un elemento **end** (come figlio dell'elemento **Association** ) specifica la tabella e il numero di righe alla fine di un vincolo FOREIGN KEY con rispettivamente il **tipo** e gli attributi di **molteplicità** .</span><span class="sxs-lookup"><span data-stu-id="39baa-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="39baa-236">Le entità finali del vincolo di chiave esterna sono definite come parte dell'associazione SSDL. L'associazione SSDL deve disporre esattamente di due entità finali.</span><span class="sxs-lookup"><span data-stu-id="39baa-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="39baa-237">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-238">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="39baa-239">OnDelete (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="39baa-240">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="39baa-241">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-241">Applicable Attributes</span></span>

<span data-ttu-id="39baa-242">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **finale** quando è figlio di un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="39baa-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="39baa-243">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-243">Attribute Name</span></span>   | <span data-ttu-id="39baa-244">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-244">Is Required</span></span> | <span data-ttu-id="39baa-245">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-246">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="39baa-246">**Type**</span></span>         | <span data-ttu-id="39baa-247">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-247">Yes</span></span>         | <span data-ttu-id="39baa-248">Il nome completo del set di entità SSDL che si trova in corrispondenza dell'entità finale del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="39baa-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="39baa-249">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="39baa-249">**Role**</span></span>         | <span data-ttu-id="39baa-250">No</span><span class="sxs-lookup"><span data-stu-id="39baa-250">No</span></span>          | <span data-ttu-id="39baa-251">Valore dell'attributo **Role** nell'elemento Principal o dipendente dell'elemento ReferentialConstraint corrispondente (se usato).</span><span class="sxs-lookup"><span data-stu-id="39baa-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="39baa-252">**Molteplicità**</span><span class="sxs-lookup"><span data-stu-id="39baa-252">**Multiplicity**</span></span> | <span data-ttu-id="39baa-253">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-253">Yes</span></span>         | <span data-ttu-id="39baa-254">**1**, **0.. 1**o a **\*** seconda del numero di righe che possono trovarsi alla fine del vincolo FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="39baa-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="39baa-255">**1** indica che esiste esattamente una riga nell'entità finale del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="39baa-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="39baa-256">**0.. 1** indica che l'entità finale del vincolo di chiave esterna è pari a zero o a una riga.</span><span class="sxs-lookup"><span data-stu-id="39baa-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="39baa-257">**\*** indica che nell'entità finale del vincolo di chiave esterna sono presenti zero, una o più righe.</span><span class="sxs-lookup"><span data-stu-id="39baa-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-258">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="39baa-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="39baa-259">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="39baa-260">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="39baa-261">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-261">Example</span></span>

<span data-ttu-id="39baa-262">Nell'esempio seguente viene illustrato un elemento **Association** che definisce il vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="39baa-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="39baa-263">I valori di **molteplicità** specificati in ogni elemento **end** indicano che molte righe della tabella **Orders** possono essere associate a una riga nella tabella **Customers** , ma solo una riga nella tabella **Customers** può essere associata a una riga nella tabella **Orders** .</span><span class="sxs-lookup"><span data-stu-id="39baa-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="39baa-264">Inoltre, l'elemento **OnDelete** indica che tutte le righe della tabella **Orders** che fanno riferimento a una determinata riga nella tabella **Customers** verranno eliminate se la riga della tabella **Customers** viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="39baa-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="39baa-265">Elemento End come figlio dell'elemento AssociationSet</span><span class="sxs-lookup"><span data-stu-id="39baa-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="39baa-266">L'elemento **end** (come figlio dell'elemento **associationname** ) specifica una tabella in corrispondenza di un'entità finale di un vincolo FOREIGN KEY nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="39baa-267">Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-268">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-269">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="39baa-270">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-270">Applicable Attributes</span></span>

<span data-ttu-id="39baa-271">Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **finale** quando è figlio di un elemento di **associazione** .</span><span class="sxs-lookup"><span data-stu-id="39baa-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="39baa-272">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-272">Attribute Name</span></span> | <span data-ttu-id="39baa-273">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-273">Is Required</span></span> | <span data-ttu-id="39baa-274">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-275">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="39baa-275">**EntitySet**</span></span>  | <span data-ttu-id="39baa-276">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-276">Yes</span></span>         | <span data-ttu-id="39baa-277">Il nome del set di entità SSDL in corrispondenza dell'entità finale del vincolo di chiave esterna.</span><span class="sxs-lookup"><span data-stu-id="39baa-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="39baa-278">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="39baa-278">**Role**</span></span>       | <span data-ttu-id="39baa-279">No</span><span class="sxs-lookup"><span data-stu-id="39baa-279">No</span></span>          | <span data-ttu-id="39baa-280">Valore di uno degli attributi **Role** specificati in un elemento **end** dell'elemento Association corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-281">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** .</span><span class="sxs-lookup"><span data-stu-id="39baa-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="39baa-282">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="39baa-283">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="39baa-284">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-284">Example</span></span>

<span data-ttu-id="39baa-285">Nell'esempio seguente viene illustrato un elemento **EntityContainer** con un elemento di **associazione** con due elementi **end** :</span><span class="sxs-lookup"><span data-stu-id="39baa-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="39baa-286">Elemento EntityContainer (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="39baa-287">Un elemento **EntityContainer** in Store Schema Definition Language (SSDL) descrive la struttura dell'origine dati sottostante in un'applicazione Entity Framework: i set di entità SSDL (definiti negli elementi EntitySet) rappresentano le tabelle in un database, i tipi di entità SSDL (definiti negli elementi EntityType) rappresentano le righe in una tabella e i set di associazioni (definiti in elementi di associazioni) rappresentano vincoli di chiave esterna in un database.</span><span class="sxs-lookup"><span data-stu-id="39baa-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="39baa-288">Un contenitore di entità del modello di archiviazione esegue il mapping a un contenitore di entità del modello concettuale attraverso l'elemento EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="39baa-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="39baa-289">Un elemento **EntityContainer** può avere uno o più elementi di documentazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="39baa-290">Se è presente un elemento di **documentazione** , deve precedere tutti gli altri elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="39baa-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="39baa-291">Un elemento **EntityContainer** può avere zero o più degli elementi figlio seguenti (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-292">EntitySet</span><span class="sxs-lookup"><span data-stu-id="39baa-292">EntitySet</span></span>
-   <span data-ttu-id="39baa-293">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="39baa-293">AssociationSet</span></span>
-   <span data-ttu-id="39baa-294">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="39baa-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-295">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-295">Applicable Attributes</span></span>

<span data-ttu-id="39baa-296">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="39baa-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="39baa-297">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-297">Attribute Name</span></span> | <span data-ttu-id="39baa-298">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-298">Is Required</span></span> | <span data-ttu-id="39baa-299">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="39baa-300">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-300">**Name**</span></span>       | <span data-ttu-id="39baa-301">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-301">Yes</span></span>         | <span data-ttu-id="39baa-302">Nome del contenitore di entità.</span><span class="sxs-lookup"><span data-stu-id="39baa-302">The name of the entity container.</span></span> <span data-ttu-id="39baa-303">Il nome non può contenere caratteri punto (.).</span><span class="sxs-lookup"><span data-stu-id="39baa-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-304">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="39baa-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="39baa-305">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-306">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-307">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-307">Example</span></span>

<span data-ttu-id="39baa-308">Nell'esempio seguente viene illustrato un elemento **EntityContainer** che definisce due set di entità e un set di associazioni.</span><span class="sxs-lookup"><span data-stu-id="39baa-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="39baa-309">I nomi dei tipi di entità e associazione sono qualificati dal nome dello spazio dei nomi del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="39baa-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="39baa-310">Elemento EntitySet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="39baa-311">Un elemento **EntitySet** in Store Schema Definition Language (SSDL) rappresenta una tabella o una vista nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="39baa-312">Un elemento EntityType in SSDL rappresenta una riga nella tabella o nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="39baa-313">L'attributo **EntityType** di un elemento **EntitySet** specifica il particolare tipo di entità SSDL che rappresenta le righe in un set di entità SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="39baa-314">Il mapping tra un set di entità CSDL e un set di entità SSDL viene specificato in un elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="39baa-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="39baa-315">L'elemento **EntitySet** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-316">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="39baa-317">DefiningQuery (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="39baa-318">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="39baa-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-319">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-319">Applicable Attributes</span></span>

<span data-ttu-id="39baa-320">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="39baa-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="39baa-321">Alcuni attributi (non elencati qui) possono essere qualificati con l'alias del **negozio** .</span><span class="sxs-lookup"><span data-stu-id="39baa-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="39baa-322">Questi attributi vengono utilizzati dalla procedura guidata Aggiorna modello in caso di aggiornamento di un modello.</span><span class="sxs-lookup"><span data-stu-id="39baa-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="39baa-323">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-323">Attribute Name</span></span> | <span data-ttu-id="39baa-324">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-324">Is Required</span></span> | <span data-ttu-id="39baa-325">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-326">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-326">**Name**</span></span>       | <span data-ttu-id="39baa-327">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-327">Yes</span></span>         | <span data-ttu-id="39baa-328">Nome del set di entità.</span><span class="sxs-lookup"><span data-stu-id="39baa-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="39baa-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="39baa-329">**EntityType**</span></span> | <span data-ttu-id="39baa-330">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-330">Yes</span></span>         | <span data-ttu-id="39baa-331">Nome completo del tipo di entità per il quale il set di entità contiene delle istanze.</span><span class="sxs-lookup"><span data-stu-id="39baa-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="39baa-332">**Schema**</span><span class="sxs-lookup"><span data-stu-id="39baa-332">**Schema**</span></span>     | <span data-ttu-id="39baa-333">No</span><span class="sxs-lookup"><span data-stu-id="39baa-333">No</span></span>          | <span data-ttu-id="39baa-334">Schema del database.</span><span class="sxs-lookup"><span data-stu-id="39baa-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="39baa-335">**Tabella**</span><span class="sxs-lookup"><span data-stu-id="39baa-335">**Table**</span></span>      | <span data-ttu-id="39baa-336">No</span><span class="sxs-lookup"><span data-stu-id="39baa-336">No</span></span>          | <span data-ttu-id="39baa-337">Tabella del database.</span><span class="sxs-lookup"><span data-stu-id="39baa-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="39baa-338">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="39baa-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="39baa-339">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-340">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-341">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-341">Example</span></span>

<span data-ttu-id="39baa-342">Nell'esempio seguente viene illustrato un elemento **EntityContainer** che dispone di due elementi **EntitySet** e di un elemento di **associazione** :</span><span class="sxs-lookup"><span data-stu-id="39baa-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="39baa-343">Elemento EntityType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="39baa-344">Un elemento **EntityType** in Store Schema Definition Language (SSDL) rappresenta una riga in una tabella o vista del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="39baa-345">Un elemento EntitySet in SSDL rappresenta la tabella o la vista in cui si trova la riga.</span><span class="sxs-lookup"><span data-stu-id="39baa-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="39baa-346">L'attributo **EntityType** di un elemento **EntitySet** specifica il particolare tipo di entità SSDL che rappresenta le righe in un set di entità SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="39baa-347">Il mapping tra un tipo di entità SSDL e un tipo di entità CSDL viene specificato in un elemento EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="39baa-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="39baa-348">L'elemento **EntityType** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-349">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="39baa-350">Key (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="39baa-351">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="39baa-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-352">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-352">Applicable Attributes</span></span>

<span data-ttu-id="39baa-353">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="39baa-354">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-354">Attribute Name</span></span> | <span data-ttu-id="39baa-355">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-355">Is Required</span></span> | <span data-ttu-id="39baa-356">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-357">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-357">**Name**</span></span>       | <span data-ttu-id="39baa-358">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-358">Yes</span></span>         | <span data-ttu-id="39baa-359">Nome del tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="39baa-359">The name of the entity type.</span></span> <span data-ttu-id="39baa-360">Questo valore corrisponde generalmente al nome della tabella in cui il tipo di entità rappresenta una riga.</span><span class="sxs-lookup"><span data-stu-id="39baa-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="39baa-361">Questo valore non può contenere punti (.).</span><span class="sxs-lookup"><span data-stu-id="39baa-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-362">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="39baa-363">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-364">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-365">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-365">Example</span></span>

<span data-ttu-id="39baa-366">Nell'esempio seguente viene illustrato un elemento **EntityType** con due proprietà:</span><span class="sxs-lookup"><span data-stu-id="39baa-366">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="39baa-367">Elemento Function (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-367">Function Element (SSDL)</span></span>

<span data-ttu-id="39baa-368">L'elemento **Function** in Store Schema Definition Language (SSDL) specifica una stored procedure esistente nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="39baa-369">L'elemento **Function** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-370">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-371">Parameter (zero o più)</span><span class="sxs-lookup"><span data-stu-id="39baa-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="39baa-372">CommandText (zero o uno)</span><span class="sxs-lookup"><span data-stu-id="39baa-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="39baa-373">ReturnType (zero o più)</span><span class="sxs-lookup"><span data-stu-id="39baa-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="39baa-374">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="39baa-375">Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** o l'attributo **returnType** (vedere di seguito), ma non entrambi.</span><span class="sxs-lookup"><span data-stu-id="39baa-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="39baa-376">È possibile importare stored procedure specificate nel modello di archiviazione nel modello concettuale di un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="39baa-377">Per ulteriori informazioni, vedere [esecuzione di query con stored procedure](xref:ef6/modeling/designer/stored-procedures/query).</span><span class="sxs-lookup"><span data-stu-id="39baa-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="39baa-378">L'elemento **Function** può essere utilizzato anche per definire funzioni personalizzate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-379">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-379">Applicable Attributes</span></span>

<span data-ttu-id="39baa-380">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Function** .</span><span class="sxs-lookup"><span data-stu-id="39baa-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="39baa-381">Alcuni attributi (non elencati qui) possono essere qualificati con l'alias del **negozio** .</span><span class="sxs-lookup"><span data-stu-id="39baa-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="39baa-382">Questi attributi vengono utilizzati dalla procedura guidata Aggiorna modello in caso di aggiornamento di un modello.</span><span class="sxs-lookup"><span data-stu-id="39baa-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="39baa-383">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-383">Attribute Name</span></span>             | <span data-ttu-id="39baa-384">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-384">Is Required</span></span> | <span data-ttu-id="39baa-385">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-386">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-386">**Name**</span></span>                   | <span data-ttu-id="39baa-387">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-387">Yes</span></span>         | <span data-ttu-id="39baa-388">Nome della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="39baa-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="39baa-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="39baa-389">**ReturnType**</span></span>             | <span data-ttu-id="39baa-390">No</span><span class="sxs-lookup"><span data-stu-id="39baa-390">No</span></span>          | <span data-ttu-id="39baa-391">Tipo restituito della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="39baa-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="39baa-392">**Aggregata**</span><span class="sxs-lookup"><span data-stu-id="39baa-392">**Aggregate**</span></span>              | <span data-ttu-id="39baa-393">No</span><span class="sxs-lookup"><span data-stu-id="39baa-393">No</span></span>          | <span data-ttu-id="39baa-394">**True** se il stored procedure restituisce un valore di aggregazione. in caso contrario, **false**.</span><span class="sxs-lookup"><span data-stu-id="39baa-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="39baa-395">**BuiltIn**</span><span class="sxs-lookup"><span data-stu-id="39baa-395">**BuiltIn**</span></span>                | <span data-ttu-id="39baa-396">No</span><span class="sxs-lookup"><span data-stu-id="39baa-396">No</span></span>          | <span data-ttu-id="39baa-397">**True** se la funzione è una funzione incorporata<sup>1</sup> . in caso contrario, **false**.</span><span class="sxs-lookup"><span data-stu-id="39baa-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="39baa-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="39baa-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="39baa-399">No</span><span class="sxs-lookup"><span data-stu-id="39baa-399">No</span></span>          | <span data-ttu-id="39baa-400">Nome della stored procedure.</span><span class="sxs-lookup"><span data-stu-id="39baa-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="39baa-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="39baa-401">**NiladicFunction**</span></span>        | <span data-ttu-id="39baa-402">No</span><span class="sxs-lookup"><span data-stu-id="39baa-402">No</span></span>          | <span data-ttu-id="39baa-403">**True** se la funzione è una funzione senza parametri<sup>2</sup> . In caso contrario, **false** .</span><span class="sxs-lookup"><span data-stu-id="39baa-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="39baa-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="39baa-404">**IsComposable**</span></span>           | <span data-ttu-id="39baa-405">No</span><span class="sxs-lookup"><span data-stu-id="39baa-405">No</span></span>          | <span data-ttu-id="39baa-406">**True** se la funzione è una funzione componibile<sup>3</sup> . In caso contrario, **false** .</span><span class="sxs-lookup"><span data-stu-id="39baa-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="39baa-407">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="39baa-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="39baa-408">No</span><span class="sxs-lookup"><span data-stu-id="39baa-408">No</span></span>          | <span data-ttu-id="39baa-409">Enumerazione che definisce la semantica dei tipi utilizzata per risolvere gli overload della funzione.</span><span class="sxs-lookup"><span data-stu-id="39baa-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="39baa-410">L'enumerazione è definita nel file manifesto del provider per ogni definizione di funzione.</span><span class="sxs-lookup"><span data-stu-id="39baa-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="39baa-411">Il valore predefinito è **AllowImplicitConversion**.</span><span class="sxs-lookup"><span data-stu-id="39baa-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="39baa-412">**Schema**</span><span class="sxs-lookup"><span data-stu-id="39baa-412">**Schema**</span></span>                 | <span data-ttu-id="39baa-413">No</span><span class="sxs-lookup"><span data-stu-id="39baa-413">No</span></span>          | <span data-ttu-id="39baa-414">Nome dello schema in cui viene definita la stored procedure.</span><span class="sxs-lookup"><span data-stu-id="39baa-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="39baa-415"><sup>1</sup> una funzione predefinita è una funzione definita nel database.</span><span class="sxs-lookup"><span data-stu-id="39baa-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="39baa-416">Per informazioni sulle funzioni definite nel modello di archiviazione, vedere elemento CommandText (SSDL).</span><span class="sxs-lookup"><span data-stu-id="39baa-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="39baa-417"><sup>2</sup> una funzione senza parametri è una funzione che non accetta parametri e, quando viene chiamato, non richiede le parentesi.</span><span class="sxs-lookup"><span data-stu-id="39baa-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="39baa-418"><sup>3</sup> due funzioni sono componibili se l'output di una funzione può essere l'input per l'altra funzione.</span><span class="sxs-lookup"><span data-stu-id="39baa-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="39baa-419">All'elemento **Function** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="39baa-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="39baa-420">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-421">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-422">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-422">Example</span></span>

<span data-ttu-id="39baa-423">Nell'esempio seguente viene illustrato un elemento **Function** che corrisponde alla stored procedure **UpdateOrderQuantity** .</span><span class="sxs-lookup"><span data-stu-id="39baa-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="39baa-424">La stored procedure accetta due parametri e non restituisce alcun valore.</span><span class="sxs-lookup"><span data-stu-id="39baa-424">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="39baa-425">Elemento Key (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-425">Key Element (SSDL)</span></span>

<span data-ttu-id="39baa-426">L'elemento **Key** in Store Schema Definition Language (SSDL) rappresenta la chiave primaria di una tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="39baa-427">**Key** è un elemento figlio di un elemento EntityType, che rappresenta una riga in una tabella.</span><span class="sxs-lookup"><span data-stu-id="39baa-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="39baa-428">La chiave primaria è definita nell'elemento **chiave** facendo riferimento a uno o più elementi Property definiti nell'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="39baa-429">L'elemento **Key** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-430">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="39baa-431">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="39baa-431">Annotation elements</span></span>

<span data-ttu-id="39baa-432">Nessun attributo applicabile all'elemento **chiave** .</span><span class="sxs-lookup"><span data-stu-id="39baa-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-433">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-433">Example</span></span>

<span data-ttu-id="39baa-434">Nell'esempio seguente viene illustrato un elemento **EntityType** con una chiave che fa riferimento a una proprietà:</span><span class="sxs-lookup"><span data-stu-id="39baa-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="39baa-435">Elemento OnDelete (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="39baa-436">L'elemento **OnDelete** in Store Schema Definition Language (SSDL) riflette il comportamento del database quando viene eliminata una riga che fa parte di un vincolo FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="39baa-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="39baa-437">Se l'azione è impostata su **Cascade**, verranno eliminate anche le righe che fanno riferimento a una riga in fase di eliminazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="39baa-438">Se l'azione è impostata su **None**, non vengono eliminate anche le righe che fanno riferimento a una riga eliminata.</span><span class="sxs-lookup"><span data-stu-id="39baa-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="39baa-439">Un elemento **OnDelete** è un elemento figlio di un elemento End.</span><span class="sxs-lookup"><span data-stu-id="39baa-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="39baa-440">Un elemento **OnDelete** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-441">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-442">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-443">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-443">Applicable Attributes</span></span>

<span data-ttu-id="39baa-444">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="39baa-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="39baa-445">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-445">Attribute Name</span></span> | <span data-ttu-id="39baa-446">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-446">Is Required</span></span> | <span data-ttu-id="39baa-447">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-448">**Azione**</span><span class="sxs-lookup"><span data-stu-id="39baa-448">**Action**</span></span>     | <span data-ttu-id="39baa-449">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-449">Yes</span></span>         | <span data-ttu-id="39baa-450">**Cascade** o **None**.</span><span class="sxs-lookup"><span data-stu-id="39baa-450">**Cascade** or **None**.</span></span> <span data-ttu-id="39baa-451">(Il valore con **restrizioni** è valido ma ha lo stesso comportamento di **None**).</span><span class="sxs-lookup"><span data-stu-id="39baa-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-452">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **OnDelete** .</span><span class="sxs-lookup"><span data-stu-id="39baa-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="39baa-453">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-454">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-455">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-455">Example</span></span>

<span data-ttu-id="39baa-456">Nell'esempio seguente viene illustrato un elemento **Association** che definisce il vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="39baa-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="39baa-457">L'elemento **OnDelete** indica che tutte le righe della tabella **Orders** che fanno riferimento a una determinata riga della tabella **Customers** verranno eliminate se la riga della tabella **Customers** viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="39baa-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="39baa-458">Elemento Parameter (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="39baa-459">L'elemento **Parameter** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento Function che specifica i parametri per un stored procedure nel database.</span><span class="sxs-lookup"><span data-stu-id="39baa-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="39baa-460">L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-461">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-462">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-463">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-463">Applicable Attributes</span></span>

<span data-ttu-id="39baa-464">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="39baa-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="39baa-465">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-465">Attribute Name</span></span> | <span data-ttu-id="39baa-466">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-466">Is Required</span></span> | <span data-ttu-id="39baa-467">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-468">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-468">**Name**</span></span>       | <span data-ttu-id="39baa-469">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-469">Yes</span></span>         | <span data-ttu-id="39baa-470">Nome del parametro.</span><span class="sxs-lookup"><span data-stu-id="39baa-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="39baa-471">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="39baa-471">**Type**</span></span>       | <span data-ttu-id="39baa-472">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-472">Yes</span></span>         | <span data-ttu-id="39baa-473">Tipo di parametro.</span><span class="sxs-lookup"><span data-stu-id="39baa-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="39baa-474">**Modalità**</span><span class="sxs-lookup"><span data-stu-id="39baa-474">**Mode**</span></span>       | <span data-ttu-id="39baa-475">No</span><span class="sxs-lookup"><span data-stu-id="39baa-475">No</span></span>          | <span data-ttu-id="39baa-476">**In**, **out**o **InOut** a seconda che il parametro sia un parametro di input, di output o di input/output.</span><span class="sxs-lookup"><span data-stu-id="39baa-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="39baa-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="39baa-477">**MaxLength**</span></span>  | <span data-ttu-id="39baa-478">No</span><span class="sxs-lookup"><span data-stu-id="39baa-478">No</span></span>          | <span data-ttu-id="39baa-479">Lunghezza massima del parametro.</span><span class="sxs-lookup"><span data-stu-id="39baa-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="39baa-480">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="39baa-480">**Precision**</span></span>  | <span data-ttu-id="39baa-481">No</span><span class="sxs-lookup"><span data-stu-id="39baa-481">No</span></span>          | <span data-ttu-id="39baa-482">Precisione del parametro</span><span class="sxs-lookup"><span data-stu-id="39baa-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="39baa-483">**Ridimensionare**</span><span class="sxs-lookup"><span data-stu-id="39baa-483">**Scale**</span></span>      | <span data-ttu-id="39baa-484">No</span><span class="sxs-lookup"><span data-stu-id="39baa-484">No</span></span>          | <span data-ttu-id="39baa-485">Scalabilità del parametro</span><span class="sxs-lookup"><span data-stu-id="39baa-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="39baa-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="39baa-486">**SRID**</span></span>       | <span data-ttu-id="39baa-487">No</span><span class="sxs-lookup"><span data-stu-id="39baa-487">No</span></span>          | <span data-ttu-id="39baa-488">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="39baa-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="39baa-489">Valido solo per i parametri dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="39baa-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="39baa-490">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="39baa-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-491">All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati).</span><span class="sxs-lookup"><span data-stu-id="39baa-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="39baa-492">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-493">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-494">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-494">Example</span></span>

<span data-ttu-id="39baa-495">Nell'esempio seguente viene illustrato un elemento **Function** con due elementi **Parameter** che specificano i parametri di input:</span><span class="sxs-lookup"><span data-stu-id="39baa-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="39baa-496">Elemento Principal (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="39baa-497">L'elemento **Principal** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale principale di un vincolo FOREIGN KEY (detto anche vincolo referenziale).</span><span class="sxs-lookup"><span data-stu-id="39baa-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="39baa-498">L'elemento **Principal** specifica la colonna o le colonne di chiave primaria in una tabella a cui fa riferimento un'altra colonna o colonne.</span><span class="sxs-lookup"><span data-stu-id="39baa-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="39baa-499">Gli elementi **PropertyRef** specificano le colonne a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="39baa-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="39baa-500">L'elemento dipendente specifica le colonne che fanno riferimento alle colonne di chiave primaria specificate nell'elemento **Principal** .</span><span class="sxs-lookup"><span data-stu-id="39baa-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="39baa-501">L'elemento **Principal** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="39baa-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="39baa-502">PropertyRef (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="39baa-503">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-504">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-504">Applicable Attributes</span></span>

<span data-ttu-id="39baa-505">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Principal** .</span><span class="sxs-lookup"><span data-stu-id="39baa-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="39baa-506">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-506">Attribute Name</span></span> | <span data-ttu-id="39baa-507">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-507">Is Required</span></span> | <span data-ttu-id="39baa-508">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-509">**Ruolo**</span><span class="sxs-lookup"><span data-stu-id="39baa-509">**Role**</span></span>       | <span data-ttu-id="39baa-510">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-510">Yes</span></span>         | <span data-ttu-id="39baa-511">Lo stesso valore dell'attributo **Role** (se utilizzato) dell'elemento End corrispondente; in caso contrario, il nome della tabella che contiene la colonna a cui si fa riferimento.</span><span class="sxs-lookup"><span data-stu-id="39baa-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-512">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **principale** .</span><span class="sxs-lookup"><span data-stu-id="39baa-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="39baa-513">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="39baa-514">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-515">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-515">Example</span></span>

<span data-ttu-id="39baa-516">Nell'esempio seguente viene illustrato un elemento Association che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* .</span><span class="sxs-lookup"><span data-stu-id="39baa-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="39baa-517">L'elemento **Principal** specifica la colonna **CustomerID** della tabella **Customer** come entità finale principale del vincolo.</span><span class="sxs-lookup"><span data-stu-id="39baa-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="39baa-518">Elemento Property (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-518">Property Element (SSDL)</span></span>

<span data-ttu-id="39baa-519">L'elemento **Property** in Store Schema Definition Language (SSDL) rappresenta una colonna in una tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="39baa-520">Gli elementi **Proprietà** sono elementi figlio degli elementi EntityType, che rappresentano le righe in una tabella.</span><span class="sxs-lookup"><span data-stu-id="39baa-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="39baa-521">Ogni elemento **Property** definito in un elemento **EntityType** rappresenta una colonna.</span><span class="sxs-lookup"><span data-stu-id="39baa-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="39baa-522">Un elemento **Property** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="39baa-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-523">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-523">Applicable Attributes</span></span>

<span data-ttu-id="39baa-524">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="39baa-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="39baa-525">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-525">Attribute Name</span></span>            | <span data-ttu-id="39baa-526">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-526">Is Required</span></span> | <span data-ttu-id="39baa-527">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-528">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-528">**Name**</span></span>                  | <span data-ttu-id="39baa-529">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-529">Yes</span></span>         | <span data-ttu-id="39baa-530">Nome della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="39baa-531">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="39baa-531">**Type**</span></span>                  | <span data-ttu-id="39baa-532">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-532">Yes</span></span>         | <span data-ttu-id="39baa-533">Tipo della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="39baa-534">**Ammette i valori Null**</span><span class="sxs-lookup"><span data-stu-id="39baa-534">**Nullable**</span></span>              | <span data-ttu-id="39baa-535">No</span><span class="sxs-lookup"><span data-stu-id="39baa-535">No</span></span>          | <span data-ttu-id="39baa-536">**True** (valore predefinito) o **false** a seconda che la colonna corrispondente possa avere un valore null.</span><span class="sxs-lookup"><span data-stu-id="39baa-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="39baa-537">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="39baa-537">**DefaultValue**</span></span>          | <span data-ttu-id="39baa-538">No</span><span class="sxs-lookup"><span data-stu-id="39baa-538">No</span></span>          | <span data-ttu-id="39baa-539">Valore predefinito della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="39baa-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="39baa-540">**MaxLength**</span></span>             | <span data-ttu-id="39baa-541">No</span><span class="sxs-lookup"><span data-stu-id="39baa-541">No</span></span>          | <span data-ttu-id="39baa-542">Lunghezza massima della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="39baa-543">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="39baa-543">**FixedLength**</span></span>           | <span data-ttu-id="39baa-544">No</span><span class="sxs-lookup"><span data-stu-id="39baa-544">No</span></span>          | <span data-ttu-id="39baa-545">**True** o **false** a seconda che il valore della colonna corrispondente venga archiviato come stringa a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="39baa-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="39baa-546">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="39baa-546">**Precision**</span></span>             | <span data-ttu-id="39baa-547">No</span><span class="sxs-lookup"><span data-stu-id="39baa-547">No</span></span>          | <span data-ttu-id="39baa-548">Precisione della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="39baa-549">**Ridimensionare**</span><span class="sxs-lookup"><span data-stu-id="39baa-549">**Scale**</span></span>                 | <span data-ttu-id="39baa-550">No</span><span class="sxs-lookup"><span data-stu-id="39baa-550">No</span></span>          | <span data-ttu-id="39baa-551">Scala della colonna corrispondente.</span><span class="sxs-lookup"><span data-stu-id="39baa-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="39baa-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="39baa-552">**Unicode**</span></span>               | <span data-ttu-id="39baa-553">No</span><span class="sxs-lookup"><span data-stu-id="39baa-553">No</span></span>          | <span data-ttu-id="39baa-554">**True** o **false** a seconda che il valore della colonna corrispondente venga archiviato come stringa Unicode.</span><span class="sxs-lookup"><span data-stu-id="39baa-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="39baa-555">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="39baa-555">**Collation**</span></span>             | <span data-ttu-id="39baa-556">No</span><span class="sxs-lookup"><span data-stu-id="39baa-556">No</span></span>          | <span data-ttu-id="39baa-557">Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.</span><span class="sxs-lookup"><span data-stu-id="39baa-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="39baa-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="39baa-558">**SRID**</span></span>                  | <span data-ttu-id="39baa-559">No</span><span class="sxs-lookup"><span data-stu-id="39baa-559">No</span></span>          | <span data-ttu-id="39baa-560">Identificatore di riferimento del sistema spaziale.</span><span class="sxs-lookup"><span data-stu-id="39baa-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="39baa-561">Valido solo per le proprietà dei tipi spaziali.</span><span class="sxs-lookup"><span data-stu-id="39baa-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="39baa-562">Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="39baa-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="39baa-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="39baa-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="39baa-564">No</span><span class="sxs-lookup"><span data-stu-id="39baa-564">No</span></span>          | <span data-ttu-id="39baa-565">**None**, **Identity** (se il valore della colonna corrispondente è un'identità generata nel database) o **calcolata** (se il valore della colonna corrispondente viene calcolato nel database).</span><span class="sxs-lookup"><span data-stu-id="39baa-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="39baa-566">Non valido per le proprietà RowType.</span><span class="sxs-lookup"><span data-stu-id="39baa-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-567">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="39baa-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="39baa-568">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-569">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-570">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-570">Example</span></span>

<span data-ttu-id="39baa-571">Nell'esempio seguente viene illustrato un elemento **EntityType** con due elementi **Proprietà** figlio:</span><span class="sxs-lookup"><span data-stu-id="39baa-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="39baa-572">Elemento PropertyRef (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="39baa-573">L'elemento **PropertyRef** in Store Schema Definition Language (SSDL) fa riferimento a una proprietà definita in un elemento EntityType per indicare che la proprietà eseguirà uno dei ruoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="39baa-574">Far parte della chiave primaria della tabella rappresentata da **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="39baa-575">Per definire una chiave primaria, è possibile usare uno o più elementi **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="39baa-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="39baa-576">Per ulteriori informazioni, vedere Elemento Key.</span><span class="sxs-lookup"><span data-stu-id="39baa-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="39baa-577">Rappresenterà l'entità finale dipendente o principale di un vincolo referenziale.</span><span class="sxs-lookup"><span data-stu-id="39baa-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="39baa-578">Per ulteriori informazioni, vedere Elemento ReferentialConstraint.</span><span class="sxs-lookup"><span data-stu-id="39baa-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="39baa-579">L'elemento **PropertyRef** può avere solo gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="39baa-580">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-581">Elementi Annotation</span><span class="sxs-lookup"><span data-stu-id="39baa-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-582">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-582">Applicable Attributes</span></span>

<span data-ttu-id="39baa-583">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="39baa-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="39baa-584">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-584">Attribute Name</span></span> | <span data-ttu-id="39baa-585">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-585">Is Required</span></span> | <span data-ttu-id="39baa-586">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="39baa-587">**Nome**</span><span class="sxs-lookup"><span data-stu-id="39baa-587">**Name**</span></span>       | <span data-ttu-id="39baa-588">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-588">Yes</span></span>         | <span data-ttu-id="39baa-589">Nome della proprietà alla quale viene fatto riferimento.</span><span class="sxs-lookup"><span data-stu-id="39baa-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="39baa-590">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="39baa-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="39baa-591">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="39baa-592">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-593">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-593">Example</span></span>

<span data-ttu-id="39baa-594">Nell'esempio seguente viene illustrato un elemento **PropertyRef** utilizzato per definire una chiave primaria facendo riferimento a una proprietà definita in un elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="39baa-595">Elemento ReferentialConstraint (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="39baa-596">L'elemento **ReferentialConstraint** in Store Schema Definition Language (SSDL) rappresenta un vincolo di chiave esterna (detto anche vincolo di integrità referenziale) nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="39baa-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="39baa-597">Le estremità principale e dipendente del vincolo vengono specificate rispettivamente dagli elementi figlio Principal e Dependent.</span><span class="sxs-lookup"><span data-stu-id="39baa-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="39baa-598">Alle colonne che fanno parte delle estremità principale e dipendente viene fatto riferimento tramite gli elementi PropertyRef.</span><span class="sxs-lookup"><span data-stu-id="39baa-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="39baa-599">L'elemento **ReferentialConstraint** è un elemento figlio facoltativo dell'elemento Association.</span><span class="sxs-lookup"><span data-stu-id="39baa-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="39baa-600">Se non viene utilizzato un elemento **ReferentialConstraint** per eseguire il mapping del vincolo foreign key specificato nell'elemento **Association** , a tale scopo è necessario utilizzare un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="39baa-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="39baa-601">L'elemento **ReferentialConstraint** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="39baa-602">Documentation (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="39baa-603">Principal (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="39baa-604">Dependent (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="39baa-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="39baa-605">Elementi Annotation (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-606">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-606">Applicable Attributes</span></span>

<span data-ttu-id="39baa-607">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="39baa-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="39baa-608">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-609">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-610">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-610">Example</span></span>

<span data-ttu-id="39baa-611">Nell'esempio seguente viene illustrato un elemento **Association** che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna \*\* \_ CustomerOrders FK\*\* :</span><span class="sxs-lookup"><span data-stu-id="39baa-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="39baa-612">Elemento ReturnType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="39baa-613">L'elemento **returnType** in Store Schema Definition Language (SSDL) specifica il tipo restituito per una funzione definita in un elemento **Function** .</span><span class="sxs-lookup"><span data-stu-id="39baa-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="39baa-614">Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="39baa-615">Il tipo restituito di una funzione viene specificato con l'attributo **Type** o l'elemento **returnType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="39baa-616">L'elemento **returnType** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="39baa-617">CollectionType (uno)</span><span class="sxs-lookup"><span data-stu-id="39baa-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="39baa-618">Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="39baa-619">Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="39baa-620">I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-621">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-621">Example</span></span>

<span data-ttu-id="39baa-622">Nell'esempio seguente viene utilizzata una **funzione** che restituisce una raccolta di righe.</span><span class="sxs-lookup"><span data-stu-id="39baa-622">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="39baa-623">Elemento RowType (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="39baa-624">Un elemento **RowType** in Store Schema Definition Language (SSDL) definisce una struttura senza nome come tipo restituito per una funzione definita nell'archivio.</span><span class="sxs-lookup"><span data-stu-id="39baa-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="39baa-625">Un elemento **RowType** è l'elemento figlio dell'elemento **CollectionType** :</span><span class="sxs-lookup"><span data-stu-id="39baa-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="39baa-626">Un elemento **RowType** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="39baa-627">Property (uno o più elementi)</span><span class="sxs-lookup"><span data-stu-id="39baa-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="39baa-628">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-628">Example</span></span>

<span data-ttu-id="39baa-629">Nell'esempio seguente viene illustrata una funzione di archiviazione che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="39baa-630">Elemento Schema (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="39baa-631">L'elemento **schema** nel Store Schema Definition Language (SSDL) è l'elemento radice di una definizione del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="39baa-632">Contiene definizioni per gli oggetti, le funzioni e i contenitori che costituiscono un modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="39baa-633">L'elemento **schema** può contenere zero o più degli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="39baa-634">Association Rules</span><span class="sxs-lookup"><span data-stu-id="39baa-634">Association</span></span>
-   <span data-ttu-id="39baa-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="39baa-635">EntityType</span></span>
-   <span data-ttu-id="39baa-636">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="39baa-636">EntityContainer</span></span>
-   <span data-ttu-id="39baa-637">Function</span><span class="sxs-lookup"><span data-stu-id="39baa-637">Function</span></span>

<span data-ttu-id="39baa-638">L'elemento **schema** utilizza l'attributo **namespace** per definire lo spazio dei nomi per il tipo di entità e gli oggetti di associazione in un modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="39baa-639">All'interno di uno spazio dei nomi due oggetti non possono avere lo stesso nome.</span><span class="sxs-lookup"><span data-stu-id="39baa-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="39baa-640">Uno spazio dei nomi del modello di archiviazione è diverso dallo spazio dei nomi XML dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="39baa-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="39baa-641">Uno spazio dei nomi del modello di archiviazione (definito dall'attributo **namespace** ) è un contenitore logico per tipi di entità e tipi di associazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="39baa-642">Lo spazio dei nomi XML, indicato dall'attributo **xmlns** , di un elemento **schema** è lo spazio dei nomi predefinito per gli elementi figlio e gli attributi dell'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="39baa-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="39baa-643">Gli spazi dei nomi XML del form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (dove aaaa e mm rappresentano rispettivamente l'anno e il mese) sono riservati per SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="39baa-644">Gli elementi e gli attributi personalizzati non possono essere in spazi dei nomi che hanno questo form.</span><span class="sxs-lookup"><span data-stu-id="39baa-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="39baa-645">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="39baa-645">Applicable Attributes</span></span>

<span data-ttu-id="39baa-646">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **schema** .</span><span class="sxs-lookup"><span data-stu-id="39baa-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="39baa-647">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="39baa-647">Attribute Name</span></span>            | <span data-ttu-id="39baa-648">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="39baa-648">Is Required</span></span> | <span data-ttu-id="39baa-649">Valore</span><span class="sxs-lookup"><span data-stu-id="39baa-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-650">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="39baa-650">**Namespace**</span></span>             | <span data-ttu-id="39baa-651">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-651">Yes</span></span>         | <span data-ttu-id="39baa-652">Spazio dei nomi del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-652">The namespace of the storage model.</span></span> <span data-ttu-id="39baa-653">Il valore dell'attributo **namespace** viene utilizzato per formare il nome completo di un tipo.</span><span class="sxs-lookup"><span data-stu-id="39baa-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="39baa-654">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi ExampleModel. Store, il nome completo di **EntityType** sarà ExampleModel. Store. Customer.</span><span class="sxs-lookup"><span data-stu-id="39baa-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="39baa-655">Non è possibile utilizzare le seguenti stringhe come valore per l'attributo **namespace** : **System**, **Transient**o **EDM**.</span><span class="sxs-lookup"><span data-stu-id="39baa-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="39baa-656">Il valore dell'attributo **namespace** non può corrispondere al valore dell'attributo **namespace** nell'elemento schema CSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="39baa-657">**Alias**</span><span class="sxs-lookup"><span data-stu-id="39baa-657">**Alias**</span></span>                 | <span data-ttu-id="39baa-658">No</span><span class="sxs-lookup"><span data-stu-id="39baa-658">No</span></span>          | <span data-ttu-id="39baa-659">Identificatore utilizzato al posto del nome dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="39baa-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="39baa-660">Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi ExampleModel. Store e il valore dell'attributo **alias** è *StorageModel*, è possibile utilizzare StorageModel. Customer come nome completo dell'elemento **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="39baa-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="39baa-661">**Provider**</span><span class="sxs-lookup"><span data-stu-id="39baa-661">**Provider**</span></span>              | <span data-ttu-id="39baa-662">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-662">Yes</span></span>         | <span data-ttu-id="39baa-663">Provider di dati.</span><span class="sxs-lookup"><span data-stu-id="39baa-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="39baa-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="39baa-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="39baa-665">Sì</span><span class="sxs-lookup"><span data-stu-id="39baa-665">Yes</span></span>         | <span data-ttu-id="39baa-666">Token che indica al provider quale manifesto del provider restituire.</span><span class="sxs-lookup"><span data-stu-id="39baa-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="39baa-667">Non è definito alcun formato per il token.</span><span class="sxs-lookup"><span data-stu-id="39baa-667">No format for the token is defined.</span></span> <span data-ttu-id="39baa-668">I valori per il token sono definiti dal provider.</span><span class="sxs-lookup"><span data-stu-id="39baa-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="39baa-669">Per informazioni sui token del manifesto del provider SQL Server, vedere SqlClient per Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="39baa-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="39baa-670">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-670">Example</span></span>

<span data-ttu-id="39baa-671">Nell'esempio seguente viene illustrato un elemento **dello schema** che contiene un elemento **EntityContainer** , due elementi **EntityType** e un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="39baa-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="39baa-672">Attributi di annotazione</span><span class="sxs-lookup"><span data-stu-id="39baa-672">Annotation Attributes</span></span>

<span data-ttu-id="39baa-673">Gli attributi di annotazione in Store Schema Definition Language (SSDL) sono attributi XML personalizzati nel modello di archiviazione che forniscono metadati aggiuntivi sugli elementi del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="39baa-674">Oltre ad avere una struttura XML valida, agli attributi di annotazione si applicano i vincoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="39baa-675">Gli attributi di annotazione non devono trovarsi in spazi dei nomi XML riservati a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="39baa-676">I nomi completi di due attributi di annotazione non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="39baa-677">È possibile applicare più attributi di annotazione a un determinato elemento SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="39baa-678">È possibile accedere ai metadati contenuti negli elementi Annotation in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="39baa-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-679">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-679">Example</span></span>

<span data-ttu-id="39baa-680">Nell'esempio seguente viene illustrato un elemento EntityType con un attributo annotation applicato alla proprietà **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="39baa-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="39baa-681">Nell'esempio viene inoltre mostrato un elemento Annotation aggiunto all'elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="39baa-681">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="39baa-682">Elementi Annotation (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="39baa-683">Gli elementi Annotation in SSDL (Store Schema Definition Language) sono elementi XML personalizzati nel modello di archiviazione che forniscono metadati aggiuntivi sul modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="39baa-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="39baa-684">Oltre ad avere una struttura XML valida, agli elementi Annotation si applicano i vincoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="39baa-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="39baa-685">Gli elementi Annotation non devono trovarsi in spazi dei nomi XML riservati a SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="39baa-686">I nomi completi di due elementi Annotation non devono essere uguali.</span><span class="sxs-lookup"><span data-stu-id="39baa-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="39baa-687">Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio di un dato elemento SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="39baa-688">Più elementi Annotation possono essere figli di un dato elemento SSDL.</span><span class="sxs-lookup"><span data-stu-id="39baa-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="39baa-689">A partire da .NET Framework versione 4, è possibile accedere ai metadati contenuti negli elementi di annotazione in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="39baa-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="39baa-690">Esempio</span><span class="sxs-lookup"><span data-stu-id="39baa-690">Example</span></span>

<span data-ttu-id="39baa-691">Nell'esempio seguente viene illustrato un elemento EntityType con un elemento Annotation (**CustomElement**).</span><span class="sxs-lookup"><span data-stu-id="39baa-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="39baa-692">Nell'esempio viene inoltre illustrato un attributo di annotazione applicato alla proprietà **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="39baa-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="39baa-693">Facet (SSDL)</span><span class="sxs-lookup"><span data-stu-id="39baa-693">Facets (SSDL)</span></span>

<span data-ttu-id="39baa-694">I facet in Store Schema Definition Language (SSDL) rappresentano dei vincoli sui tipi di colonna specificati in elementi Property.</span><span class="sxs-lookup"><span data-stu-id="39baa-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="39baa-695">I facet vengono implementati come attributi XML sugli elementi della **Proprietà** .</span><span class="sxs-lookup"><span data-stu-id="39baa-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="39baa-696">Nella tabella seguente vengono descritti i facet supportati in SSDL:</span><span class="sxs-lookup"><span data-stu-id="39baa-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="39baa-697">Facet</span><span class="sxs-lookup"><span data-stu-id="39baa-697">Facet</span></span>           | <span data-ttu-id="39baa-698">Descrizione</span><span class="sxs-lookup"><span data-stu-id="39baa-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="39baa-699">**Regole di confronto**</span><span class="sxs-lookup"><span data-stu-id="39baa-699">**Collation**</span></span>   | <span data-ttu-id="39baa-700">Specifica la sequenza di ordinamento da usare quando si eseguono operazioni di confronto e di ordinamento su valori della proprietà.</span><span class="sxs-lookup"><span data-stu-id="39baa-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="39baa-701">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="39baa-701">**FixedLength**</span></span> | <span data-ttu-id="39baa-702">Specifica se la lunghezza del valore della colonna può variare.</span><span class="sxs-lookup"><span data-stu-id="39baa-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="39baa-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="39baa-703">**MaxLength**</span></span>   | <span data-ttu-id="39baa-704">Specifica la lunghezza massima del valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="39baa-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="39baa-705">**Precisione**</span><span class="sxs-lookup"><span data-stu-id="39baa-705">**Precision**</span></span>   | <span data-ttu-id="39baa-706">Per le proprietà di tipo **Decimal**, specifica il numero di cifre che un valore della proprietà può avere.</span><span class="sxs-lookup"><span data-stu-id="39baa-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="39baa-707">Per le proprietà di tipo **Time**, **DateTime**e **DateTimeOffset**, specifica il numero di cifre per la parte frazionaria dei secondi del valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="39baa-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="39baa-708">**Ridimensionare**</span><span class="sxs-lookup"><span data-stu-id="39baa-708">**Scale**</span></span>       | <span data-ttu-id="39baa-709">Specifica il numero di cifre a destra del separatore decimale per il valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="39baa-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="39baa-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="39baa-710">**Unicode**</span></span>     | <span data-ttu-id="39baa-711">Indica se il valore della colonna viene archiviato come Unicode.</span><span class="sxs-lookup"><span data-stu-id="39baa-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
