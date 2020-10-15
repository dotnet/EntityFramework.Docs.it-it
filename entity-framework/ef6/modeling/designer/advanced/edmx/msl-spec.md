---
title: Specifica MSL-EF6
description: Specifica MSL in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 0780a549d4cbb60bf84bbed927d52588e99148e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066343"
---
# <a name="msl-specification"></a><span data-ttu-id="d916f-103">Specifica MSL</span><span class="sxs-lookup"><span data-stu-id="d916f-103">MSL Specification</span></span>
<span data-ttu-id="d916f-104">MSL (Mapping Specification Language) è un linguaggio basato su XML che descrive il mapping tra il modello concettuale e il modello di archiviazione di un'applicazione Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="d916f-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="d916f-105">In un'applicazione Entity Framework, i metadati di mapping vengono caricati da un file con estensione MSL (scritto in MSL) in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="d916f-106">Entity Framework usa i metadati di mapping in fase di esecuzione per tradurre le query sul modello concettuale in comandi specifici dell'archivio.</span><span class="sxs-lookup"><span data-stu-id="d916f-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="d916f-107">Il Entity Framework Designer (EF designer) archivia le informazioni di mapping in un file con estensione edmx in fase di progettazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="d916f-108">In fase di compilazione, il Entity Designer utilizza le informazioni contenute in un file con estensione edmx per creare il file con estensione msl necessario per Entity Framework in fase di esecuzione</span><span class="sxs-lookup"><span data-stu-id="d916f-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="d916f-109">I nomi di tutti i tipi di modelli concettuale o di archiviazione a cui viene fatto riferimento in MSL devono essere qualificati dai rispettivi nomi dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="d916f-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="d916f-110">Per informazioni sul nome dello spazio dei nomi del modello concettuale, vedere [specifica CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="d916f-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="d916f-111">Per informazioni sul nome dello spazio dei nomi del modello di archiviazione, vedere [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="d916f-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="d916f-112">Le versioni di MSL sono differenziate in base agli spazi dei nomi XML.</span><span class="sxs-lookup"><span data-stu-id="d916f-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="d916f-113">Versione MSL</span><span class="sxs-lookup"><span data-stu-id="d916f-113">MSL Version</span></span> | <span data-ttu-id="d916f-114">Spazio dei nomi XML</span><span class="sxs-lookup"><span data-stu-id="d916f-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="d916f-115">MSL V1</span><span class="sxs-lookup"><span data-stu-id="d916f-115">MSL v1</span></span>      | <span data-ttu-id="d916f-116">urn: schemas-microsoft-com: Windows: archiviazione: mapping: CS</span><span class="sxs-lookup"><span data-stu-id="d916f-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="d916f-117">MSL V2</span><span class="sxs-lookup"><span data-stu-id="d916f-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="d916f-118">MSL V3</span><span class="sxs-lookup"><span data-stu-id="d916f-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="d916f-119">Elemento Alias (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-119">Alias Element (MSL)</span></span>

<span data-ttu-id="d916f-120">L'elemento **alias** in Mapping Specification Language (MSL) è un elemento figlio dell'elemento mapping utilizzato per definire gli alias per gli spazi dei nomi del modello concettuale e del modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="d916f-121">I nomi di tutti i tipi di modelli concettuale o di archiviazione a cui viene fatto riferimento in MSL devono essere qualificati dai rispettivi nomi dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="d916f-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="d916f-122">Per informazioni sul nome dello spazio dei nomi del modello concettuale, vedere elemento schema (CSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="d916f-123">Per informazioni sul nome dello spazio dei nomi del modello di archiviazione, vedere elemento schema (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="d916f-124">L'elemento **alias** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-125">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-125">Applicable Attributes</span></span>

<span data-ttu-id="d916f-126">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **alias** .</span><span class="sxs-lookup"><span data-stu-id="d916f-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="d916f-127">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-127">Attribute Name</span></span> | <span data-ttu-id="d916f-128">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-128">Is Required</span></span> | <span data-ttu-id="d916f-129">Valore</span><span class="sxs-lookup"><span data-stu-id="d916f-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="d916f-130">**Chiave**</span><span class="sxs-lookup"><span data-stu-id="d916f-130">**Key**</span></span>        | <span data-ttu-id="d916f-131">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-131">Yes</span></span>         | <span data-ttu-id="d916f-132">Alias per lo spazio dei nomi specificato dall'attributo **value** .</span><span class="sxs-lookup"><span data-stu-id="d916f-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="d916f-133">**Valore**</span><span class="sxs-lookup"><span data-stu-id="d916f-133">**Value**</span></span>      | <span data-ttu-id="d916f-134">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-134">Yes</span></span>         | <span data-ttu-id="d916f-135">Spazio dei nomi per il quale il valore dell'elemento **chiave** è un alias.</span><span class="sxs-lookup"><span data-stu-id="d916f-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="d916f-136">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-136">Example</span></span>

<span data-ttu-id="d916f-137">Nell'esempio seguente viene illustrato un elemento **alias** che definisce un alias, `c` , per i tipi definiti nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a><span data-ttu-id="d916f-138">Elemento AssociationEnd (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="d916f-139">L'elemento **AssociationEnd** in Mapping Specification Language (MSL) viene utilizzato quando viene eseguito il mapping delle funzioni di modifica di un tipo di entità nel modello concettuale alle stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="d916f-140">Se una modifica stored procedure accetta un parametro il cui valore è contenuto in una proprietà di associazione, l'elemento **AssociationEnd** esegue il mapping del valore della proprietà al parametro.</span><span class="sxs-lookup"><span data-stu-id="d916f-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="d916f-141">Per ulteriori informazioni, vedere l'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="d916f-141">For more information, see the example below.</span></span>

<span data-ttu-id="d916f-142">Per ulteriori informazioni sul mapping delle funzioni di modifica dei tipi di entità alle stored procedure, vedere elemento ModificationFunctionMapping (MSL) e procedura dettagliata: mapping di un'entità alle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="d916f-143">L'elemento **AssociationEnd** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-145">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-145">Applicable Attributes</span></span>

<span data-ttu-id="d916f-146">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **AssociationEnd** .</span><span class="sxs-lookup"><span data-stu-id="d916f-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="d916f-147">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-147">Attribute Name</span></span>     | <span data-ttu-id="d916f-148">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-148">Is Required</span></span> | <span data-ttu-id="d916f-149">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-150">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="d916f-150">**AssociationSet**</span></span> | <span data-ttu-id="d916f-151">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-151">Yes</span></span>         | <span data-ttu-id="d916f-152">Nome dell'associazione di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="d916f-153">**From**</span><span class="sxs-lookup"><span data-stu-id="d916f-153">**From**</span></span>           | <span data-ttu-id="d916f-154">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-154">Yes</span></span>         | <span data-ttu-id="d916f-155">Valore dell'attributo **FromRole** della proprietà di navigazione che corrisponde all'associazione di cui viene eseguito il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="d916f-156">Per ulteriori informazioni, vedere elemento NavigationProperty (CSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="d916f-157">**To**</span><span class="sxs-lookup"><span data-stu-id="d916f-157">**To**</span></span>             | <span data-ttu-id="d916f-158">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-158">Yes</span></span>         | <span data-ttu-id="d916f-159">Valore dell'attributo **ToRole** della proprietà di navigazione che corrisponde all'associazione di cui viene eseguito il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="d916f-160">Per ulteriori informazioni, vedere elemento NavigationProperty (CSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="d916f-161">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-161">Example</span></span>

<span data-ttu-id="d916f-162">Si consideri il tipo di entità del modello concettuale seguente:</span><span class="sxs-lookup"><span data-stu-id="d916f-162">Consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

<span data-ttu-id="d916f-163">Si consideri inoltre la seguente stored procedure:</span><span class="sxs-lookup"><span data-stu-id="d916f-163">Also consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

<span data-ttu-id="d916f-164">Per eseguire il mapping della funzione di aggiornamento dell' `Course` entità a questa stored procedure, è necessario fornire un valore al parametro **DepartmentID** .</span><span class="sxs-lookup"><span data-stu-id="d916f-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="d916f-165">Il valore di `DepartmentID` non corrisponde a una proprietà sul tipo di entità. È contenuto in un'associazione indipendente il cui mapping è illustrato di seguito:</span><span class="sxs-lookup"><span data-stu-id="d916f-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

<span data-ttu-id="d916f-166">Il codice seguente illustra l'elemento **AssociationEnd** usato per eseguire il mapping della proprietà **DepartmentID** dell'associazione del \*\* \_ \_ reparto Course FK\*\* al stored procedure **UpdateCourse** (a cui viene mappata la funzione di aggiornamento del tipo di entità **Course** ):</span><span class="sxs-lookup"><span data-stu-id="d916f-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="d916f-167">Elemento AssociationSetMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-168">L'elemento **AssociationSetMapping** in Mapping Specification Language (MSL) definisce il mapping tra un'associazione nel modello concettuale e le colonne della tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="d916f-169">Le associazioni del modello concettuale sono tipi le cui proprietà rappresentano colonne di chiavi primarie ed esterne nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="d916f-170">L'elemento **AssociationSetMapping** utilizza due elementi EndProperty per definire i mapping tra le proprietà del tipo di associazione e le colonne nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="d916f-171">È possibile definire condizioni su questi mapping con l'elemento Condition.</span><span class="sxs-lookup"><span data-stu-id="d916f-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="d916f-172">Per eseguire il mapping delle funzioni di inserimento, aggiornamento ed eliminazione per le associazioni alle stored procedure del database, utilizzare l'elemento ModificationFunctionMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="d916f-173">Definire mapping di sola lettura tra le associazioni e le colonne della tabella usando una stringa Entity SQL in un elemento QueryView.</span><span class="sxs-lookup"><span data-stu-id="d916f-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-174">Se per un'associazione nel modello concettuale è definito un vincolo referenziale, non è necessario eseguire il mapping dell'associazione con un elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="d916f-175">Se è presente un elemento **AssociationSetMapping** per un'associazione con un vincolo referenziale, i mapping definiti nell'elemento **AssociationSetMapping** verranno ignorati.</span><span class="sxs-lookup"><span data-stu-id="d916f-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="d916f-176">Per ulteriori informazioni, vedere elemento ReferentialConstraint (CSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="d916f-177">L'elemento **AssociationSetMapping** può avere gli elementi figlio seguenti</span><span class="sxs-lookup"><span data-stu-id="d916f-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="d916f-178">QueryView (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="d916f-179">EndProperty (zero o due elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="d916f-180">Condition (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="d916f-181">ModificationFunctionMapping (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-182">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-182">Applicable Attributes</span></span>

<span data-ttu-id="d916f-183">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="d916f-184">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-184">Attribute Name</span></span>     | <span data-ttu-id="d916f-185">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-185">Is Required</span></span> | <span data-ttu-id="d916f-186">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-187">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-187">**Name**</span></span>           | <span data-ttu-id="d916f-188">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-188">Yes</span></span>         | <span data-ttu-id="d916f-189">Nome del set di associazioni del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="d916f-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="d916f-190">**TypeName**</span></span>       | <span data-ttu-id="d916f-191">No</span><span class="sxs-lookup"><span data-stu-id="d916f-191">No</span></span>          | <span data-ttu-id="d916f-192">Nome completo dello spazio dei nomi del tipo di associazione del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="d916f-193">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="d916f-193">**StoreEntitySet**</span></span> | <span data-ttu-id="d916f-194">No</span><span class="sxs-lookup"><span data-stu-id="d916f-194">No</span></span>          | <span data-ttu-id="d916f-195">Nome della tabella di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="d916f-196">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-196">Example</span></span>

<span data-ttu-id="d916f-197">Nell'esempio seguente viene illustrato un elemento **AssociationSetMapping** in cui viene eseguito il mapping dell'associazione del \*\* \_ \_ reparto dei corsi FK\*\* nel modello concettuale alla tabella **Course** nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="d916f-198">I mapping tra le proprietà del tipo di associazione e le colonne della tabella sono specificati negli elementi **EndProperty** figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a><span data-ttu-id="d916f-199">Elemento ComplexProperty (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="d916f-200">Un elemento **ComplexProperty** in Mapping Specification Language (MSL) definisce il mapping tra una proprietà di tipo complesso in un tipo di entità del modello concettuale e le colonne della tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="d916f-201">I mapping delle colonne delle proprietà sono specificati in elementi ScalarProperty figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="d916f-202">L'elemento della proprietà **complexType** può presentare gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-203">ScalarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="d916f-204">**ComplexProperty** (zero o più)</span><span class="sxs-lookup"><span data-stu-id="d916f-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="d916f-205">ComplextTypeMapping (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="d916f-206">Condition (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-207">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-207">Applicable Attributes</span></span>

<span data-ttu-id="d916f-208">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **ComplexProperty** :</span><span class="sxs-lookup"><span data-stu-id="d916f-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="d916f-209">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-209">Attribute Name</span></span> | <span data-ttu-id="d916f-210">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-210">Is Required</span></span> | <span data-ttu-id="d916f-211">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-212">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-212">**Name**</span></span>       | <span data-ttu-id="d916f-213">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-213">Yes</span></span>         | <span data-ttu-id="d916f-214">Nome della proprietà complessa di un tipo di entità nel modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="d916f-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="d916f-215">**TypeName**</span></span>   | <span data-ttu-id="d916f-216">No</span><span class="sxs-lookup"><span data-stu-id="d916f-216">No</span></span>          | <span data-ttu-id="d916f-217">Nome qualificato di spazio dei nomi del tipo di proprietà del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="d916f-218">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-218">Example</span></span>

<span data-ttu-id="d916f-219">Di seguito viene riportato un esempio basato sul modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-219">The following example is based on the School model.</span></span> <span data-ttu-id="d916f-220">Il tipo complesso seguente è stato aggiunto al modello concettuale:</span><span class="sxs-lookup"><span data-stu-id="d916f-220">The following complex type has been added to the conceptual model:</span></span>

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

<span data-ttu-id="d916f-221">Le proprietà **LastName** e **FirstName** del tipo di entità **Person** sono state sostituite con una proprietà complessa, **Name**:</span><span class="sxs-lookup"><span data-stu-id="d916f-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

<span data-ttu-id="d916f-222">Il MSL seguente mostra l'elemento **ComplexProperty** usato per eseguire il mapping della proprietà **Name** alle colonne nel database sottostante:</span><span class="sxs-lookup"><span data-stu-id="d916f-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="d916f-223">Elemento ComplexTypeMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-224">L'elemento **ComplexTypeMapping** in Mapping Specification Language (MSL) è un elemento figlio dell'elemento ResultMapping e definisce il mapping tra un'importazione di funzioni nel modello concettuale e una stored procedure nel database sottostante quando sono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="d916f-225">Un tipo complesso concettuale viene restituito dall'importazione di funzioni.</span><span class="sxs-lookup"><span data-stu-id="d916f-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="d916f-226">I nomi delle colonne restituite dalla stored procedure non corrispondono esattamente ai nomi delle proprietà del tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="d916f-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="d916f-227">Per impostazione predefinita, il mapping tra le colonne restituite da una stored procedure e un tipo complesso è basato sui nomi delle colonne e delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="d916f-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="d916f-228">Se i nomi delle colonne non corrispondono esattamente ai nomi delle proprietà, è necessario utilizzare l'elemento **ComplexTypeMapping** per definire il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="d916f-229">Per un esempio del mapping predefinito, vedere elemento FunctionImportMapping (MSL).</span><span class="sxs-lookup"><span data-stu-id="d916f-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="d916f-230">L'elemento **ComplexTypeMapping** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-231">ScalarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-232">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-232">Applicable Attributes</span></span>

<span data-ttu-id="d916f-233">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **ComplexTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="d916f-234">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-234">Attribute Name</span></span> | <span data-ttu-id="d916f-235">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-235">Is Required</span></span> | <span data-ttu-id="d916f-236">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="d916f-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="d916f-237">**TypeName**</span></span>   | <span data-ttu-id="d916f-238">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-238">Yes</span></span>         | <span data-ttu-id="d916f-239">Nome completo dello spazio dei nomi del tipo complesso di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-240">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-240">Example</span></span>

<span data-ttu-id="d916f-241">Si consideri la stored procedure seguente:</span><span class="sxs-lookup"><span data-stu-id="d916f-241">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="d916f-242">Si consideri anche il tipo complesso del modello concettuale seguente:</span><span class="sxs-lookup"><span data-stu-id="d916f-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="d916f-243">Per creare un'importazione di funzioni che restituisce istanze del tipo complesso precedente, il mapping tra le colonne restituite dal stored procedure e il tipo di entità deve essere definito in un elemento **ComplexTypeMapping** :</span><span class="sxs-lookup"><span data-stu-id="d916f-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a><span data-ttu-id="d916f-244">Elemento Condition (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-244">Condition Element (MSL)</span></span>

<span data-ttu-id="d916f-245">L'elemento **Condition** in Mapping Specification Language (MSL) posiziona le condizioni sui mapping tra il modello concettuale e il database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="d916f-246">Il mapping definito all'interno di un nodo XML è valido se vengono soddisfatte tutte le condizioni, come specificato negli elementi della **condizione** figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="d916f-247">In caso contrario, il mapping non è valido.</span><span class="sxs-lookup"><span data-stu-id="d916f-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="d916f-248">Se, ad esempio, un elemento MappingFragment contiene uno o più elementi figlio **Condition** , il mapping definito all'interno del nodo **MappingFragment** sarà valido solo se vengono soddisfatte tutte le condizioni degli elementi della **condizione** figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="d916f-249">Ogni condizione può essere applicata a un **nome** (il nome di una proprietà dell'entità del modello concettuale, specificata dall'attributo **Name** ) o a un **ColumnName** (il nome di una colonna nel database, specificato dall'attributo **ColumnName** ).</span><span class="sxs-lookup"><span data-stu-id="d916f-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="d916f-250">Quando viene impostato l'attributo **Name** , la condizione viene verificata rispetto al valore della proprietà di un'entità.</span><span class="sxs-lookup"><span data-stu-id="d916f-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="d916f-251">Quando viene impostato l'attributo **ColumnName** , la condizione viene verificata rispetto a un valore di colonna.</span><span class="sxs-lookup"><span data-stu-id="d916f-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="d916f-252">È possibile specificare solo uno degli attributi **Name** o **ColumnName** in un elemento **Condition** .</span><span class="sxs-lookup"><span data-stu-id="d916f-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-253">Quando l'elemento **Condition** viene utilizzato all'interno di un elemento FunctionImportMapping, solo l'attributo **Name** non è applicabile.</span><span class="sxs-lookup"><span data-stu-id="d916f-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="d916f-254">L'elemento **Condition** può essere figlio dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="d916f-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="d916f-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="d916f-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-256">ComplexProperty</span></span>
-   <span data-ttu-id="d916f-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-257">EntitySetMapping</span></span>
-   <span data-ttu-id="d916f-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="d916f-258">MappingFragment</span></span>
-   <span data-ttu-id="d916f-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-259">EntityTypeMapping</span></span>

<span data-ttu-id="d916f-260">L'elemento **Condition** non può avere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-261">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-261">Applicable Attributes</span></span>

<span data-ttu-id="d916f-262">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **Condition** :</span><span class="sxs-lookup"><span data-stu-id="d916f-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="d916f-263">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-263">Attribute Name</span></span> | <span data-ttu-id="d916f-264">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-264">Is Required</span></span> | <span data-ttu-id="d916f-265">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-266">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="d916f-266">**ColumnName**</span></span> | <span data-ttu-id="d916f-267">No</span><span class="sxs-lookup"><span data-stu-id="d916f-267">No</span></span>          | <span data-ttu-id="d916f-268">Nome della colonna della tabella il cui valore viene utilizzato per valutare la condizione.</span><span class="sxs-lookup"><span data-stu-id="d916f-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="d916f-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="d916f-269">**IsNull**</span></span>     | <span data-ttu-id="d916f-270">No</span><span class="sxs-lookup"><span data-stu-id="d916f-270">No</span></span>          | <span data-ttu-id="d916f-271">**True** o **false**.</span><span class="sxs-lookup"><span data-stu-id="d916f-271">**True** or **False**.</span></span> <span data-ttu-id="d916f-272">Se il valore è **true** e il valore della colonna è **null**o se il valore è **false** e il valore della colonna non è **null**, la condizione è true.</span><span class="sxs-lookup"><span data-stu-id="d916f-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="d916f-273">In caso contrario, la condizione è false.</span><span class="sxs-lookup"><span data-stu-id="d916f-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="d916f-274">Impossibile utilizzare contemporaneamente gli attributi **IsNull** e **value** .</span><span class="sxs-lookup"><span data-stu-id="d916f-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="d916f-275">**Valore**</span><span class="sxs-lookup"><span data-stu-id="d916f-275">**Value**</span></span>      | <span data-ttu-id="d916f-276">No</span><span class="sxs-lookup"><span data-stu-id="d916f-276">No</span></span>          | <span data-ttu-id="d916f-277">Valore con cui viene confrontato il valore della colonna.</span><span class="sxs-lookup"><span data-stu-id="d916f-277">The value with which the column value is compared.</span></span> <span data-ttu-id="d916f-278">Se i valori sono uguali, la condizione è true.</span><span class="sxs-lookup"><span data-stu-id="d916f-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="d916f-279">In caso contrario, la condizione è false.</span><span class="sxs-lookup"><span data-stu-id="d916f-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="d916f-280">Impossibile utilizzare contemporaneamente gli attributi **IsNull** e **value** .</span><span class="sxs-lookup"><span data-stu-id="d916f-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="d916f-281">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-281">**Name**</span></span>       | <span data-ttu-id="d916f-282">No</span><span class="sxs-lookup"><span data-stu-id="d916f-282">No</span></span>          | <span data-ttu-id="d916f-283">Nome della proprietà dell'entità del modello concettuale il cui valore viene utilizzato per valutare la condizione.</span><span class="sxs-lookup"><span data-stu-id="d916f-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="d916f-284">Questo attributo non è applicabile se l'elemento **Condition** viene utilizzato all'interno di un elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="d916f-285">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-285">Example</span></span>

<span data-ttu-id="d916f-286">Nell'esempio seguente vengono mostrati gli elementi **Condition** come elementi figlio di elementi **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="d916f-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="d916f-287">Quando **Hiret** non è null e **EnrollmentDate** è null, viene eseguito il mapping dei dati tra il tipo **SchoolModel. Instructor** e le colonne **PersonID** e **Hired** della tabella **Person** .</span><span class="sxs-lookup"><span data-stu-id="d916f-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="d916f-288">Quando **EnrollmentDate** non è null e il valore di **Hiret** è null, viene eseguito il mapping dei dati tra il tipo **SchoolModel. Student** e le colonne **PersonID** e di **registrazione** della tabella **Person** .</span><span class="sxs-lookup"><span data-stu-id="d916f-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a><span data-ttu-id="d916f-289">Elemento DeleteFunction (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="d916f-290">L'elemento **DeleteFunction** in Mapping Specification Language (MSL) esegue il mapping della funzione Delete di un tipo di entità o di un'associazione nel modello concettuale a una stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="d916f-291">Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="d916f-292">Per ulteriori informazioni, vedere elemento Function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-293">Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.</span><span class="sxs-lookup"><span data-stu-id="d916f-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="d916f-294">DeleteFunction applicato a EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="d916f-295">Quando applicato all'elemento EntityTypeMapping, l'elemento **DeleteFunction** esegue il mapping della funzione di eliminazione di un tipo di entità nel modello concettuale a un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="d916f-296">Quando viene applicato a un elemento **EntityTypeMapping** , l'elemento **DeleteFunction** può includere i seguenti elementi figlio:</span><span class="sxs-lookup"><span data-stu-id="d916f-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="d916f-297">AssociationEnd (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="d916f-298">ComplexProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="d916f-299">ScarlarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="d916f-300">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-300">Applicable Attributes</span></span>

<span data-ttu-id="d916f-301">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **DeleteFunction** quando viene applicato a un elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="d916f-302">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-302">Attribute Name</span></span>            | <span data-ttu-id="d916f-303">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-303">Is Required</span></span> | <span data-ttu-id="d916f-304">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-305">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="d916f-305">**FunctionName**</span></span>          | <span data-ttu-id="d916f-306">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-306">Yes</span></span>         | <span data-ttu-id="d916f-307">Nome completo dello spazio dei nomi della stored procedure a cui la funzione di eliminazione viene mappata.</span><span class="sxs-lookup"><span data-stu-id="d916f-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="d916f-308">La stored procedure deve essere dichiarata nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="d916f-309">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="d916f-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="d916f-310">No</span><span class="sxs-lookup"><span data-stu-id="d916f-310">No</span></span>          | <span data-ttu-id="d916f-311">Nome del parametro di output che restituisce il numero di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="d916f-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="d916f-312">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-312">Example</span></span>

<span data-ttu-id="d916f-313">L'esempio seguente è basato sul modello School e Mostra l'elemento **DeleteFunction** che mappa la funzione Delete del tipo di entità **Person** alla stored procedure **DeletePerson** .</span><span class="sxs-lookup"><span data-stu-id="d916f-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="d916f-314">Il stored procedure **DeletePerson** viene dichiarato nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="d916f-315">DeleteFunction applicato ad AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="d916f-316">Quando applicato all'elemento AssociationSetMapping, l'elemento **DeleteFunction** esegue il mapping della funzione Delete di un'associazione nel modello concettuale a un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="d916f-317">L'elemento **DeleteFunction** può avere gli elementi figlio seguenti quando viene applicato all'elemento **AssociationSetMapping** :</span><span class="sxs-lookup"><span data-stu-id="d916f-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="d916f-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="d916f-319">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-319">Applicable Attributes</span></span>

<span data-ttu-id="d916f-320">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **DeleteFunction** quando viene applicato all'elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="d916f-321">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-321">Attribute Name</span></span>            | <span data-ttu-id="d916f-322">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-322">Is Required</span></span> | <span data-ttu-id="d916f-323">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-324">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="d916f-324">**FunctionName**</span></span>          | <span data-ttu-id="d916f-325">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-325">Yes</span></span>         | <span data-ttu-id="d916f-326">Nome completo dello spazio dei nomi della stored procedure a cui la funzione di eliminazione viene mappata.</span><span class="sxs-lookup"><span data-stu-id="d916f-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="d916f-327">La stored procedure deve essere dichiarata nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="d916f-328">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="d916f-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="d916f-329">No</span><span class="sxs-lookup"><span data-stu-id="d916f-329">No</span></span>          | <span data-ttu-id="d916f-330">Nome del parametro di output che restituisce il numero di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="d916f-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="d916f-331">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-331">Example</span></span>

<span data-ttu-id="d916f-332">L'esempio seguente è basato sul modello School e Mostra l'elemento **DeleteFunction** usato per eseguire il mapping della funzione Delete dell'associazione **CourseInstructor** a **DeleteCourseInstructor** stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="d916f-333">Il stored procedure **DeleteCourseInstructor** viene dichiarato nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a><span data-ttu-id="d916f-334">Elemento EndProperty (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="d916f-335">L'elemento **EndProperty** in Mapping Specification Language (MSL) definisce il mapping tra un'entità finale o una funzione di modifica di un'associazione del modello concettuale e il database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="d916f-336">Il mapping delle colonne delle proprietà viene specificato in un elemento ScalarProperty figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="d916f-337">Quando si usa un elemento **EndProperty** per definire il mapping per la fine di un'associazione del modello concettuale, è un elemento figlio di un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="d916f-338">Quando l'elemento **EndProperty** viene usato per definire il mapping per una funzione di modifica di un'associazione del modello concettuale, è un elemento figlio di un elemento InsertFunction o DeleteFunction.</span><span class="sxs-lookup"><span data-stu-id="d916f-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="d916f-339">L'elemento **EndProperty** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-340">ScalarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-341">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-341">Applicable Attributes</span></span>

<span data-ttu-id="d916f-342">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **EndProperty** :</span><span class="sxs-lookup"><span data-stu-id="d916f-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="d916f-343">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-343">Attribute Name</span></span> | <span data-ttu-id="d916f-344">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-344">Is Required</span></span> | <span data-ttu-id="d916f-345">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="d916f-346">Nome</span><span class="sxs-lookup"><span data-stu-id="d916f-346">Name</span></span>           | <span data-ttu-id="d916f-347">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-347">Yes</span></span>         | <span data-ttu-id="d916f-348">Nome dell'entità finale dell'associazione di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-349">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-349">Example</span></span>

<span data-ttu-id="d916f-350">Nell'esempio seguente viene illustrato un elemento **AssociationSetMapping** in cui viene eseguito il mapping dell'associazione del \*\* \_ \_ reparto Course FK\*\* nel modello concettuale alla tabella **Course** nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="d916f-351">I mapping tra le proprietà del tipo di associazione e le colonne della tabella sono specificati negli elementi **EndProperty** figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a><span data-ttu-id="d916f-352">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-352">Example</span></span>

<span data-ttu-id="d916f-353">Nell'esempio seguente viene illustrato l'elemento **EndProperty** che consente di eseguire il mapping delle funzioni Insert e Delete di un'associazione (**CourseInstructor**) alle stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="d916f-354">Le funzioni a cui viene eseguito il mapping vengono dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-354">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="d916f-355">Elemento EntityContainerMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-356">L'elemento **EntityContainerMapping** in Mapping Specification Language (MSL) esegue il mapping del contenitore di entità nel modello concettuale al contenitore di entità nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="d916f-357">L'elemento **EntityContainerMapping** è un elemento figlio dell'elemento mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="d916f-358">L'elemento **EntityContainerMapping** può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="d916f-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="d916f-359">EntitySetMapping (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="d916f-360">AssociationSetMapping (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="d916f-361">FunctionImportMapping (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-362">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-362">Applicable Attributes</span></span>

<span data-ttu-id="d916f-363">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityContainerMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="d916f-364">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-364">Attribute Name</span></span>            | <span data-ttu-id="d916f-365">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-365">Is Required</span></span> | <span data-ttu-id="d916f-366">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-367">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="d916f-367">**StorageModelContainer**</span></span> | <span data-ttu-id="d916f-368">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-368">Yes</span></span>         | <span data-ttu-id="d916f-369">Nome del contenitore di entità del modello di archiviazione di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="d916f-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="d916f-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="d916f-371">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-371">Yes</span></span>         | <span data-ttu-id="d916f-372">Nome del contenitore di entità del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="d916f-373">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="d916f-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="d916f-374">No</span><span class="sxs-lookup"><span data-stu-id="d916f-374">No</span></span>          | <span data-ttu-id="d916f-375">**True** o **false**.</span><span class="sxs-lookup"><span data-stu-id="d916f-375">**True** or **False**.</span></span> <span data-ttu-id="d916f-376">Se **false**, non viene generata alcuna vista aggiornamento.</span><span class="sxs-lookup"><span data-stu-id="d916f-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="d916f-377">Questo attributo deve essere impostato su **false** quando si dispone di un mapping di sola lettura che non sarebbe valido perché i dati non possono essere completati correttamente.</span><span class="sxs-lookup"><span data-stu-id="d916f-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="d916f-378">Il valore predefinito è **True**.</span><span class="sxs-lookup"><span data-stu-id="d916f-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-379">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-379">Example</span></span>

<span data-ttu-id="d916f-380">Nell'esempio seguente viene illustrato un elemento **EntityContainerMapping** che esegue il mapping del contenitore **SchoolModelEntities** (il contenitore di entità del modello concettuale) al contenitore **SchoolModelStoreContainer** (il contenitore di entità del modello di archiviazione):</span><span class="sxs-lookup"><span data-stu-id="d916f-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="d916f-381">Elemento EntitySetMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-382">L'elemento **EntitySetMapping** in Mapping Specification Language (MSL) esegue il mapping di tutti i tipi in un set di entità del modello concettuale ai set di entità nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="d916f-383">Un set di entità nel modello concettuale è un contenitore logico per istanze di entità dello stesso tipo (e tipi derivati).</span><span class="sxs-lookup"><span data-stu-id="d916f-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="d916f-384">Un set di entità nel modello di archiviazione rappresenta una tabella o visualizzazione nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="d916f-385">Il set di entità del modello concettuale viene specificato dal valore dell'attributo **Name** dell'elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="d916f-386">La tabella o la vista di cui è stato eseguito il mapping viene specificata dall'attributo **StoreEntitySet** in ogni elemento MappingFragment figlio o nell'elemento **EntitySetMapping** stesso.</span><span class="sxs-lookup"><span data-stu-id="d916f-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="d916f-387">L'elemento **EntitySetMapping** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-388">EntityTypeMapping (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="d916f-389">QueryView (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="d916f-390">MappingFragment (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-391">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-391">Applicable Attributes</span></span>

<span data-ttu-id="d916f-392">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="d916f-393">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-393">Attribute Name</span></span>           | <span data-ttu-id="d916f-394">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-394">Is Required</span></span> | <span data-ttu-id="d916f-395">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-396">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-396">**Name**</span></span>                 | <span data-ttu-id="d916f-397">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-397">Yes</span></span>         | <span data-ttu-id="d916f-398">Nome del set di entità del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="d916f-399">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="d916f-399">**TypeName** **1**</span></span>       | <span data-ttu-id="d916f-400">No</span><span class="sxs-lookup"><span data-stu-id="d916f-400">No</span></span>          | <span data-ttu-id="d916f-401">Nome del tipo di entità del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="d916f-402">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="d916f-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="d916f-403">No</span><span class="sxs-lookup"><span data-stu-id="d916f-403">No</span></span>          | <span data-ttu-id="d916f-404">Nome del set di entità del modello di archiviazione a cui viene eseguito il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="d916f-405">**Makecolumnsdistinct impostato**</span><span class="sxs-lookup"><span data-stu-id="d916f-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="d916f-406">No</span><span class="sxs-lookup"><span data-stu-id="d916f-406">No</span></span>          | <span data-ttu-id="d916f-407">**True** o **false** , a seconda che vengano restituite solo righe distinte.</span><span class="sxs-lookup"><span data-stu-id="d916f-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="d916f-408">Se questo attributo è impostato su **true**, l'attributo **GenerateUpdateViews** dell'elemento EntityContainerMapping deve essere impostato su **false**.</span><span class="sxs-lookup"><span data-stu-id="d916f-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="d916f-409">**1** gli attributi **typeName** e **StoreEntitySet** possono essere utilizzati al posto degli elementi figlio EntityTypeMapping e MappingFragment per eseguire il mapping di un singolo tipo di entità a una singola tabella.</span><span class="sxs-lookup"><span data-stu-id="d916f-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="d916f-410">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-410">Example</span></span>

<span data-ttu-id="d916f-411">Nell'esempio seguente viene illustrato un elemento **EntitySetMapping** che esegue il mapping di tre tipi (un tipo di base e due tipi derivati) nel set di entità **Courses** del modello concettuale a tre tabelle diverse del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="d916f-412">Le tabelle vengono specificate dall'attributo **StoreEntitySet** in ogni elemento **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="d916f-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="d916f-413">Elemento EntityTypeMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-414">L'elemento **EntityTypeMapping** in Mapping Specification Language (MSL) definisce il mapping tra un tipo di entità nel modello concettuale e le tabelle o le viste nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="d916f-415">Per informazioni sui tipi di entità del modello concettuale e sulle tabelle o visualizzazioni del database sottostante, vedere Elemento EntityType (CSDL) e Elemento EntitySet (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="d916f-416">Il tipo di entità del modello concettuale di cui è in corso il mapping viene specificato dall'attributo **typeName** dell'elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="d916f-417">La tabella o la vista di cui viene eseguito il mapping viene specificata dall'attributo **StoreEntitySet** dell'elemento MappingFragment figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="d916f-418">L'elemento figlio ModificationFunctionMapping può essere utilizzato per eseguire il mapping delle funzioni di inserimento, aggiornamento o eliminazione dei tipi di entità alle stored procedure nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="d916f-419">L'elemento **EntityTypeMapping** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-420">MappingFragment (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="d916f-421">ModificationFunctionMapping (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="d916f-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-422">ScalarProperty</span></span>
-   <span data-ttu-id="d916f-423">Condizione</span><span class="sxs-lookup"><span data-stu-id="d916f-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-424">Gli elementi **MappingFragment** e **ModificationFunctionMapping** non possono essere elementi figlio dell'elemento **EntityTypeMapping** nello stesso momento.</span><span class="sxs-lookup"><span data-stu-id="d916f-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="d916f-425">Gli elementi **ScalarProperty** e **Condition** possono essere solo elementi figlio dell'elemento **EntityTypeMapping** quando vengono usati all'interno di un elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-426">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-426">Applicable Attributes</span></span>

<span data-ttu-id="d916f-427">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="d916f-428">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-428">Attribute Name</span></span> | <span data-ttu-id="d916f-429">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-429">Is Required</span></span> | <span data-ttu-id="d916f-430">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="d916f-431">**TypeName**</span></span>   | <span data-ttu-id="d916f-432">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-432">Yes</span></span>         | <span data-ttu-id="d916f-433">Nome completo dello spazio dei nomi del tipo di entità del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="d916f-434">Se il tipo è astratto o un tipo derivato, il valore deve essere `IsOfType(Namespace-qualified_type_name)`.</span><span class="sxs-lookup"><span data-stu-id="d916f-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-435">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-435">Example</span></span>

<span data-ttu-id="d916f-436">Nell'esempio seguente viene illustrato un elemento EntitySetMapping con due elementi **EntityTypeMapping** figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="d916f-437">Nel primo elemento **EntityTypeMapping** , il tipo di entità **SchoolModel. Person** viene mappato alla tabella **Person** .</span><span class="sxs-lookup"><span data-stu-id="d916f-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="d916f-438">Nel secondo elemento **EntityTypeMapping** , viene eseguito il mapping della funzionalità di aggiornamento del tipo **SchoolModel. Person** a un stored procedure, **UpdatePerson**, nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="d916f-439">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-439">Example</span></span>

<span data-ttu-id="d916f-440">Nell'esempio successivo viene mostrato il mapping di una gerarchia di tipo in cui il tipo radice è astratto.</span><span class="sxs-lookup"><span data-stu-id="d916f-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="d916f-441">Si noti l'uso della `IsOfType` sintassi per gli attributi **typeName** .</span><span class="sxs-lookup"><span data-stu-id="d916f-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="d916f-442">Elemento FunctionImportMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-443">L'elemento **FunctionImportMapping** in Mapping Specification Language (MSL) definisce il mapping tra un'importazione di funzioni nel modello concettuale e una stored procedure o una funzione nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="d916f-444">Le importazioni di funzioni devono essere dichiarate nel modello concettuale mentre le stored procedure nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="d916f-445">Per ulteriori informazioni, vedere elemento FunctionImport (CSDL) e elemento Function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-446">Per impostazione predefinita, se un'importazione di funzioni restituisce un tipo di entità del modello concettuale o un tipo complesso, i nomi delle colonne restituiti dalla stored procedure sottostante devono corrispondere esattamente ai nomi delle proprietà nel tipo di modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="d916f-447">Se i nomi delle colonne non corrispondono esattamente ai nomi delle proprietà, il mapping deve essere definito in un elemento ResultMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="d916f-448">L'elemento **FunctionImportMapping** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-449">ResultMapping (zero o più)</span><span class="sxs-lookup"><span data-stu-id="d916f-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-450">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-450">Applicable Attributes</span></span>

<span data-ttu-id="d916f-451">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **FunctionImportMapping** :</span><span class="sxs-lookup"><span data-stu-id="d916f-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="d916f-452">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-452">Attribute Name</span></span>         | <span data-ttu-id="d916f-453">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-453">Is Required</span></span> | <span data-ttu-id="d916f-454">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="d916f-455">**FunctionImportName**</span></span> | <span data-ttu-id="d916f-456">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-456">Yes</span></span>         | <span data-ttu-id="d916f-457">Nome dell'importazione di funzioni nel modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="d916f-458">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="d916f-458">**FunctionName**</span></span>       | <span data-ttu-id="d916f-459">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-459">Yes</span></span>         | <span data-ttu-id="d916f-460">Nome completo dello spazio dei nomi della funzione nel modello di archiviazione di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-461">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-461">Example</span></span>

<span data-ttu-id="d916f-462">Di seguito viene riportato un esempio basato sul modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-462">The following example is based on the School model.</span></span> <span data-ttu-id="d916f-463">Si consideri la funzione seguente nel modello di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="d916f-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="d916f-464">Si consideri anche questa importazione di funzioni nel modello concettuale:</span><span class="sxs-lookup"><span data-stu-id="d916f-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="d916f-465">Nell'esempio seguente viene illustrato un elemento **FunctionImportMapping** usato per eseguire il mapping tra la funzione e l'importazione di funzioni sopra elencate:</span><span class="sxs-lookup"><span data-stu-id="d916f-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="d916f-466">Elemento InsertFunction (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="d916f-467">L'elemento **InsertFunction** in Mapping Specification Language (MSL) esegue il mapping della funzione di inserimento di un tipo di entità o di un'associazione nel modello concettuale a una stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="d916f-468">Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="d916f-469">Per ulteriori informazioni, vedere elemento Function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-470">Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.</span><span class="sxs-lookup"><span data-stu-id="d916f-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="d916f-471">L'elemento **InsertFunction** può essere un elemento figlio dell'elemento ModificationFunctionMapping e applicato all'elemento EntityTypeMapping o AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="d916f-472">InsertFunction applicato a EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="d916f-473">Quando applicato all'elemento EntityTypeMapping, l'elemento **InsertFunction** esegue il mapping della funzione di inserimento di un tipo di entità nel modello concettuale a un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="d916f-474">Quando viene applicato a un elemento **EntityTypeMapping** , l'elemento **InsertFunction** può includere i seguenti elementi figlio:</span><span class="sxs-lookup"><span data-stu-id="d916f-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="d916f-475">AssociationEnd (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="d916f-476">ComplexProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="d916f-477">ResultBinding (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="d916f-478">ScarlarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="d916f-479">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-479">Applicable Attributes</span></span>

<span data-ttu-id="d916f-480">Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **InsertFunction** quando vengono applicati a un elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="d916f-481">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-481">Attribute Name</span></span>            | <span data-ttu-id="d916f-482">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-482">Is Required</span></span> | <span data-ttu-id="d916f-483">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-484">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="d916f-484">**FunctionName**</span></span>          | <span data-ttu-id="d916f-485">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-485">Yes</span></span>         | <span data-ttu-id="d916f-486">Nome completo dello spazio dei nomi della stored procedure a cui la funzione di inserimento viene mappata.</span><span class="sxs-lookup"><span data-stu-id="d916f-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="d916f-487">La stored procedure deve essere dichiarata nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="d916f-488">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="d916f-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="d916f-489">No</span><span class="sxs-lookup"><span data-stu-id="d916f-489">No</span></span>          | <span data-ttu-id="d916f-490">Nome del parametro di output che restituisce il numero di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="d916f-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="d916f-491">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-491">Example</span></span>

<span data-ttu-id="d916f-492">L'esempio seguente è basato sul modello School e Mostra l'elemento **InsertFunction** usato per eseguire il mapping della funzione di inserimento del tipo di entità Person alla stored procedure **InsertPerson** .</span><span class="sxs-lookup"><span data-stu-id="d916f-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="d916f-493">Il stored procedure **InsertPerson** viene dichiarato nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="d916f-494">InsertFunction applicato ad AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="d916f-495">Quando applicato all'elemento AssociationSetMapping, l'elemento **InsertFunction** esegue il mapping della funzione di inserimento di un'associazione nel modello concettuale a un stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="d916f-496">L'elemento **InsertFunction** può avere gli elementi figlio seguenti quando viene applicato all'elemento **AssociationSetMapping** :</span><span class="sxs-lookup"><span data-stu-id="d916f-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="d916f-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="d916f-498">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-498">Applicable Attributes</span></span>

<span data-ttu-id="d916f-499">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **InsertFunction** quando viene applicato all'elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="d916f-500">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-500">Attribute Name</span></span>            | <span data-ttu-id="d916f-501">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-501">Is Required</span></span> | <span data-ttu-id="d916f-502">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-503">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="d916f-503">**FunctionName**</span></span>          | <span data-ttu-id="d916f-504">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-504">Yes</span></span>         | <span data-ttu-id="d916f-505">Nome completo dello spazio dei nomi della stored procedure a cui la funzione di inserimento viene mappata.</span><span class="sxs-lookup"><span data-stu-id="d916f-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="d916f-506">La stored procedure deve essere dichiarata nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="d916f-507">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="d916f-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="d916f-508">No</span><span class="sxs-lookup"><span data-stu-id="d916f-508">No</span></span>          | <span data-ttu-id="d916f-509">Nome del parametro di output che restituisce il numero di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="d916f-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="d916f-510">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-510">Example</span></span>

<span data-ttu-id="d916f-511">L'esempio seguente è basato sul modello School e Mostra l'elemento **InsertFunction** usato per eseguire il mapping della funzione di inserimento dell'associazione **CourseInstructor** a **InsertCourseInstructor** stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="d916f-512">Il stored procedure **InsertCourseInstructor** viene dichiarato nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a><span data-ttu-id="d916f-513">Elemento Mapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="d916f-514">L'elemento **mapping** in Mapping Specification Language (MSL) contiene informazioni per il mapping di oggetti definiti in un modello concettuale a un database (come descritto in un modello di archiviazione).</span><span class="sxs-lookup"><span data-stu-id="d916f-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="d916f-515">Per ulteriori informazioni, vedere Specification CSDL e SSDL Specification.</span><span class="sxs-lookup"><span data-stu-id="d916f-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="d916f-516">L'elemento **mapping** è l'elemento radice per una specifica di mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="d916f-517">Lo spazio dei nomi XML per le specifiche di mapping è https://schemas.microsoft.com/ado/2009/11/mapping/cs .</span><span class="sxs-lookup"><span data-stu-id="d916f-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="d916f-518">L'elemento Mapping può includere i seguenti elementi figlio (nell'ordine elencato):</span><span class="sxs-lookup"><span data-stu-id="d916f-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="d916f-519">Alias (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="d916f-520">EntityContainerMapping (esattamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="d916f-521">I nomi di tipi di modelli concettuale e di archiviazione a cui viene fatto riferimento in MSL devono essere qualificati dai rispettivi nomi dello spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="d916f-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="d916f-522">Per informazioni sul nome dello spazio dei nomi del modello concettuale, vedere elemento schema (CSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="d916f-523">Per informazioni sul nome dello spazio dei nomi del modello di archiviazione, vedere elemento schema (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="d916f-524">Gli alias per gli spazi dei nomi utilizzati in MSL possono essere definiti con l'elemento Alias.</span><span class="sxs-lookup"><span data-stu-id="d916f-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-525">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-525">Applicable Attributes</span></span>

<span data-ttu-id="d916f-526">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **mapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="d916f-527">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-527">Attribute Name</span></span> | <span data-ttu-id="d916f-528">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-528">Is Required</span></span> | <span data-ttu-id="d916f-529">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="d916f-530">**Spazio**</span><span class="sxs-lookup"><span data-stu-id="d916f-530">**Space**</span></span>      | <span data-ttu-id="d916f-531">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-531">Yes</span></span>         | <span data-ttu-id="d916f-532">**C-S**.</span><span class="sxs-lookup"><span data-stu-id="d916f-532">**C-S**.</span></span> <span data-ttu-id="d916f-533">Si tratta di un valore fisso e non può essere modificato.</span><span class="sxs-lookup"><span data-stu-id="d916f-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-534">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-534">Example</span></span>

<span data-ttu-id="d916f-535">Nell'esempio seguente viene illustrato un elemento di **mapping** basato su una parte del modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="d916f-536">Per ulteriori informazioni sul modello School, vedere Guida introduttiva (Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="d916f-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="d916f-537">Elemento MappingFragment (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="d916f-538">L'elemento **MappingFragment** in Mapping Specification Language (MSL) definisce il mapping tra le proprietà di un tipo di entità del modello concettuale e una tabella o una vista nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="d916f-539">Per informazioni sui tipi di entità del modello concettuale e sulle tabelle o visualizzazioni del database sottostante, vedere Elemento EntityType (CSDL) e Elemento EntitySet (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="d916f-540">**MappingFragment** può essere un elemento figlio dell'elemento EntityTypeMapping o EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="d916f-541">L'elemento **MappingFragment** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-542">ComplexType (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="d916f-543">ScalarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="d916f-544">Condition (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-545">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-545">Applicable Attributes</span></span>

<span data-ttu-id="d916f-546">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="d916f-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="d916f-547">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-547">Attribute Name</span></span>          | <span data-ttu-id="d916f-548">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-548">Is Required</span></span> | <span data-ttu-id="d916f-549">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-550">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="d916f-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="d916f-551">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-551">Yes</span></span>         | <span data-ttu-id="d916f-552">Nome della tabella o visualizzazione di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="d916f-553">**Makecolumnsdistinct impostato**</span><span class="sxs-lookup"><span data-stu-id="d916f-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="d916f-554">No</span><span class="sxs-lookup"><span data-stu-id="d916f-554">No</span></span>          | <span data-ttu-id="d916f-555">**True** o **false** , a seconda che vengano restituite solo righe distinte.</span><span class="sxs-lookup"><span data-stu-id="d916f-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="d916f-556">Se questo attributo è impostato su **true**, l'attributo **GenerateUpdateViews** dell'elemento EntityContainerMapping deve essere impostato su **false**.</span><span class="sxs-lookup"><span data-stu-id="d916f-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-557">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-557">Example</span></span>

<span data-ttu-id="d916f-558">Nell'esempio seguente viene illustrato un elemento **MappingFragment** come figlio di un elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="d916f-559">In questo esempio, le proprietà del tipo **Course** nel modello concettuale vengono mappate alle colonne della tabella **Course** nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="d916f-560">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-560">Example</span></span>

<span data-ttu-id="d916f-561">Nell'esempio seguente viene illustrato un elemento **MappingFragment** come figlio di un elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="d916f-562">Come nell'esempio precedente, le proprietà del tipo **Course** nel modello concettuale vengono mappate alle colonne della tabella **Course** nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="d916f-563">Elemento ModificationFunctionMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-564">L'elemento **ModificationFunctionMapping** in Mapping Specification Language (MSL) esegue il mapping delle funzioni di inserimento, aggiornamento ed eliminazione di un tipo di entità del modello concettuale alle stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="d916f-565">L'elemento **ModificationFunctionMapping** può inoltre eseguire il mapping delle funzioni Insert e DELETE per le associazioni molti-a-molti nel modello concettuale alle stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="d916f-566">Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="d916f-567">Per ulteriori informazioni, vedere elemento Function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-568">Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.</span><span class="sxs-lookup"><span data-stu-id="d916f-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="d916f-569">Se esiste un mapping tra le funzioni di modifica per un'entità in una gerarchia di ereditarietà e stored procedure, è necessario eseguire il mapping delle funzioni di modifica per tutti i tipi nella gerarchia a stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="d916f-570">L'elemento **ModificationFunctionMapping** può essere un elemento figlio dell'elemento EntityTypeMapping o AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="d916f-571">L'elemento **ModificationFunctionMapping** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-572">DeleteFunction (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="d916f-573">InsertFunction (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="d916f-574">UpdateFunction (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="d916f-575">Nessun attributo applicabile all'elemento **ModificationFunctionMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="d916f-576">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-576">Example</span></span>

<span data-ttu-id="d916f-577">Nell'esempio seguente viene illustrato il mapping del set di entità per il set di entità **people** nel modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="d916f-578">Oltre al mapping delle colonne per il tipo di entità **Person** , viene visualizzato il mapping delle funzioni di inserimento, aggiornamento ed eliminazione del tipo **Person** .</span><span class="sxs-lookup"><span data-stu-id="d916f-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="d916f-579">Le funzioni a cui viene eseguito il mapping vengono dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-579">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="d916f-580">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-580">Example</span></span>

<span data-ttu-id="d916f-581">Nell'esempio seguente viene illustrato il mapping del set di associazioni per il set di associazioni **CourseInstructor** nel modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="d916f-582">Oltre al mapping delle colonne per l'associazione **CourseInstructor** , viene visualizzato il mapping delle funzioni di inserimento ed eliminazione dell'associazione **CourseInstructor** .</span><span class="sxs-lookup"><span data-stu-id="d916f-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="d916f-583">Le funzioni a cui viene eseguito il mapping vengono dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-583">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="d916f-584">Elemento QueryView (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="d916f-585">L'elemento **QueryView** in Mapping Specification Language (MSL) definisce un mapping di sola lettura tra un tipo di entità o un'associazione nel modello concettuale e una tabella nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="d916f-586">Il mapping viene definito con una query Entity SQL valutata rispetto al modello di archiviazione e il set di risultati viene espresso in termini di entità o associazione nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="d916f-587">Poiché le visualizzazioni di query sono di sola lettura, non è possibile utilizzare comandi di aggiornamento standard per aggiornare i tipi definiti da tali visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="d916f-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="d916f-588">A tale scopo è possibile utilizzare le funzioni di modifica.</span><span class="sxs-lookup"><span data-stu-id="d916f-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="d916f-589">Per altre informazioni, vedere Procedura: eseguire il mapping delle funzioni di modifica alle stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-590">Nell'elemento **QueryView** , le espressioni Entity SQL che contengono **GroupBy**, aggregazioni di gruppo o proprietà di navigazione non sono supportate.</span><span class="sxs-lookup"><span data-stu-id="d916f-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="d916f-591">L'elemento **QueryView** può essere un elemento figlio dell'elemento EntitySetMapping o AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="d916f-592">Nel primo caso, la visualizzazione di query definisce un mapping di sola lettura per un'entità nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="d916f-593">Nel secondo caso, la visualizzazione di query definisce un mapping di sola lettura per un'associazione nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-594">Se l'elemento **AssociationSetMapping** è per un'associazione con un vincolo referenziale, l'elemento **AssociationSetMapping** viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="d916f-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="d916f-595">Per ulteriori informazioni, vedere elemento ReferentialConstraint (CSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="d916f-596">L'elemento **QueryView** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-597">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-597">Applicable Attributes</span></span>

<span data-ttu-id="d916f-598">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **QueryView** .</span><span class="sxs-lookup"><span data-stu-id="d916f-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="d916f-599">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-599">Attribute Name</span></span> | <span data-ttu-id="d916f-600">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-600">Is Required</span></span> | <span data-ttu-id="d916f-601">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="d916f-602">**TypeName**</span></span>   | <span data-ttu-id="d916f-603">No</span><span class="sxs-lookup"><span data-stu-id="d916f-603">No</span></span>          | <span data-ttu-id="d916f-604">Nome del tipo di modello concettuale mappato dalla visualizzazione di query.</span><span class="sxs-lookup"><span data-stu-id="d916f-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-605">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-605">Example</span></span>

<span data-ttu-id="d916f-606">Nell'esempio seguente viene illustrato l'elemento **QueryView** come figlio dell'elemento **EntitySetMapping** e viene definito un mapping della visualizzazione di query per il tipo di entità **Department** nel modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

<span data-ttu-id="d916f-607">Poiché la query restituisce solo un subset dei membri del tipo **Department** nel modello di archiviazione, il tipo **Department** del modello School è stato modificato in base a questo mapping come segue:</span><span class="sxs-lookup"><span data-stu-id="d916f-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a><span data-ttu-id="d916f-608">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-608">Example</span></span>

<span data-ttu-id="d916f-609">Nell'esempio seguente viene illustrato l'elemento **QueryView** come figlio di un elemento **AssociationSetMapping** e viene definito un mapping di sola lettura per l' `FK_Course_Department` associazione nel modello School.</span><span class="sxs-lookup"><span data-stu-id="d916f-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a><span data-ttu-id="d916f-610">Commenti</span><span class="sxs-lookup"><span data-stu-id="d916f-610">Comments</span></span>

<span data-ttu-id="d916f-611">È possibile definire le visualizzazioni di query per consentire gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="d916f-612">Definire un'entità nel modello concettuale che non includa tutte le proprietà dell'entità nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="d916f-613">Sono incluse le proprietà che non dispongono di valori predefiniti e non supportano valori **null** .</span><span class="sxs-lookup"><span data-stu-id="d916f-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="d916f-614">Eseguire il mapping delle colonne calcolate nel modello di archiviazione alle proprietà dei tipi di entità nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="d916f-615">Definire un mapping in cui le condizioni utilizzate per partizionare le entità nel modello concettuale non siano basate sull'uguaglianza.</span><span class="sxs-lookup"><span data-stu-id="d916f-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="d916f-616">Quando si specifica un mapping condizionale usando l'elemento **Condition** , la condizione fornita deve corrispondere al valore specificato.</span><span class="sxs-lookup"><span data-stu-id="d916f-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="d916f-617">Per ulteriori informazioni, vedere elemento Condition (MSL).</span><span class="sxs-lookup"><span data-stu-id="d916f-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="d916f-618">Eseguire il mapping della stessa colonna nel modello di archiviazione a più tipi nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="d916f-619">Eseguire il mapping di più tipi alla stessa tabella.</span><span class="sxs-lookup"><span data-stu-id="d916f-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="d916f-620">Definire associazioni nel modello concettuale che non siano basate sulle chiavi esterne dello schema relazionale.</span><span class="sxs-lookup"><span data-stu-id="d916f-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="d916f-621">Utilizzare la logica di business personalizzata per impostare il valore delle proprietà nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="d916f-622">Ad esempio, è possibile eseguire il mapping del valore stringa "T" nell'origine dati a un valore **true**, un valore booleano, nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="d916f-623">Definire filtri condizionali per i risultati della query.</span><span class="sxs-lookup"><span data-stu-id="d916f-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="d916f-624">Applicare ai dati del modello concettuale un numero di restrizioni minore di quelle applicate al modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="d916f-625">Ad esempio, è possibile creare una proprietà nel modello concettuale Nullable anche se la colonna a cui è stato eseguito il mapping non supporta valori **null**.</span><span class="sxs-lookup"><span data-stu-id="d916f-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="d916f-626">Le considerazioni seguenti riguardano la definizione delle visualizzazioni di query per le entità:</span><span class="sxs-lookup"><span data-stu-id="d916f-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="d916f-627">Le visualizzazioni di query sono di sola lettura.</span><span class="sxs-lookup"><span data-stu-id="d916f-627">Query views are read-only.</span></span> <span data-ttu-id="d916f-628">È possibile applicare aggiornamenti alle entità solo utilizzando le funzioni di modifica.</span><span class="sxs-lookup"><span data-stu-id="d916f-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="d916f-629">Quando viene definito un tipo di entità da una visualizzazione di query, è necessario definire anche tutte le entità correlate dalle visualizzazioni di query.</span><span class="sxs-lookup"><span data-stu-id="d916f-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="d916f-630">Quando si esegue il mapping di un'associazione many-to-many a un'entità nel modello di archiviazione che rappresenta una tabella dei collegamenti nello schema relazionale, è necessario definire un elemento **QueryView** nell'elemento **AssociationSetMapping** per la tabella dei collegamenti.</span><span class="sxs-lookup"><span data-stu-id="d916f-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="d916f-631">È necessario definire le visualizzazioni di query per tutti i tipi di una gerarchia di tipi.</span><span class="sxs-lookup"><span data-stu-id="d916f-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="d916f-632">È possibile eseguire questa operazione nei modi seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="d916f-633">Con un singolo elemento **QueryView** che specifica una singola query di Entity SQL che restituisce un'Unione di tutti i tipi di entità nella gerarchia.</span><span class="sxs-lookup"><span data-stu-id="d916f-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="d916f-634">Con un singolo elemento **QueryView** che specifica una singola query di Entity SQL che usa l'operatore case per restituire un tipo di entità specifico nella gerarchia in base a una condizione specifica.</span><span class="sxs-lookup"><span data-stu-id="d916f-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="d916f-635">Con un elemento **QueryView** aggiuntivo per un tipo specifico nella gerarchia.</span><span class="sxs-lookup"><span data-stu-id="d916f-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="d916f-636">In questo caso, usare l'attributo **typeName** dell'elemento **QueryView** per specificare il tipo di entità per ogni visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="d916f-637">Quando viene definita una visualizzazione di query, non è possibile specificare l'attributo **StorageSetName** nell'elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="d916f-638">Quando viene definita una visualizzazione di query, l'elemento **EntitySetMapping**non può contenere anche mapping di **Proprietà** .</span><span class="sxs-lookup"><span data-stu-id="d916f-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="d916f-639">Elemento ResultBinding (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="d916f-640">L'elemento **risultante** in Mapping Specification Language (MSL) esegue il mapping dei valori di colonna restituiti dalle stored procedure alle proprietà dell'entità nel modello concettuale quando viene eseguito il mapping delle funzioni di modifica del tipo di entità alle stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="d916f-641">Quando, ad esempio, il valore di una colonna Identity viene restituito da un stored procedure di inserimento, l'elemento **risultante** esegue il mapping del valore restituito a una proprietà del tipo di entità nel modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="d916f-642">L'elemento **risultante** può essere figlio dell'elemento InsertFunction o dell'elemento UpdateFunction.</span><span class="sxs-lookup"><span data-stu-id="d916f-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="d916f-643">L'elemento **risultante** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-644">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-644">Applicable Attributes</span></span>

<span data-ttu-id="d916f-645">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **risultante** :</span><span class="sxs-lookup"><span data-stu-id="d916f-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="d916f-646">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-646">Attribute Name</span></span> | <span data-ttu-id="d916f-647">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-647">Is Required</span></span> | <span data-ttu-id="d916f-648">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-649">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-649">**Name**</span></span>       | <span data-ttu-id="d916f-650">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-650">Yes</span></span>         | <span data-ttu-id="d916f-651">Nome della proprietà di entità nel modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="d916f-652">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="d916f-652">**ColumnName**</span></span> | <span data-ttu-id="d916f-653">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-653">Yes</span></span>         | <span data-ttu-id="d916f-654">Nome della colonna di cui viene eseguito il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="d916f-655">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-655">Example</span></span>

<span data-ttu-id="d916f-656">L'esempio seguente è basato sul modello School e Mostra un elemento **InsertFunction** usato per eseguire il mapping della funzione di inserimento del tipo di entità **Person** alla stored procedure **InsertPerson** .</span><span class="sxs-lookup"><span data-stu-id="d916f-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="d916f-657">Il stored procedure **InsertPerson** è illustrato di seguito e viene dichiarato nel modello di archiviazione. Un elemento **risultante** viene utilizzato per eseguire il mapping di un valore di colonna restituito dal stored procedure (**NewPersonID**) a una proprietà del tipo di entità (**PersonID**).</span><span class="sxs-lookup"><span data-stu-id="d916f-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

<span data-ttu-id="d916f-658">L'istruzione Transact-SQL seguente descrive il stored procedure **InsertPerson** :</span><span class="sxs-lookup"><span data-stu-id="d916f-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a><span data-ttu-id="d916f-659">Elemento ResultMapping (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="d916f-660">L'elemento **ResultMapping** in Mapping Specification Language (MSL) definisce il mapping tra un'importazione di funzioni nel modello concettuale e una stored procedure nel database sottostante quando sono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="d916f-661">L'operazione di importazione di funzioni restituisce un tipo di entità del modello concettuale.</span><span class="sxs-lookup"><span data-stu-id="d916f-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="d916f-662">I nomi delle colonne restituite dalla stored procedure non corrispondono esattamente ai nomi delle proprietà del tipo di entità o del tipo complesso.</span><span class="sxs-lookup"><span data-stu-id="d916f-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="d916f-663">Per impostazione predefinita, il mapping tra le colonne restituite da una stored procedure e un tipo di entità o un tipo complesso è basato sui nomi delle colonne e delle proprietà.</span><span class="sxs-lookup"><span data-stu-id="d916f-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="d916f-664">Se i nomi delle colonne non corrispondono esattamente ai nomi delle proprietà, è necessario utilizzare l'elemento **ResultMapping** per definire il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="d916f-665">Per un esempio del mapping predefinito, vedere elemento FunctionImportMapping (MSL).</span><span class="sxs-lookup"><span data-stu-id="d916f-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="d916f-666">L'elemento **ResultMapping** è un elemento figlio dell'elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="d916f-667">L'elemento **ResultMapping** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-668">EntityTypeMapping (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="d916f-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-669">ComplexTypeMapping</span></span>

<span data-ttu-id="d916f-670">Nessun attributo applicabile all'elemento **ResultMapping** .</span><span class="sxs-lookup"><span data-stu-id="d916f-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="d916f-671">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-671">Example</span></span>

<span data-ttu-id="d916f-672">Si consideri la stored procedure seguente:</span><span class="sxs-lookup"><span data-stu-id="d916f-672">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="d916f-673">Si consideri inoltre il tipo di entità del modello concettuale seguente:</span><span class="sxs-lookup"><span data-stu-id="d916f-673">Also consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

<span data-ttu-id="d916f-674">Per creare un'importazione di funzioni che restituisce istanze del tipo di entità precedente, il mapping tra le colonne restituite dal stored procedure e il tipo di entità deve essere definito in un elemento **ResultMapping** :</span><span class="sxs-lookup"><span data-stu-id="d916f-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="d916f-675">Elemento ScalarProperty (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="d916f-676">L'elemento **ScalarProperty** in Mapping Specification Language (MSL) esegue il mapping di una proprietà su un tipo di entità del modello concettuale, un tipo complesso o un'associazione a una colonna della tabella o a un parametro di stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="d916f-677">Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="d916f-678">Per ulteriori informazioni, vedere elemento Function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="d916f-679">L'elemento **ScalarProperty** può essere figlio dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="d916f-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="d916f-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="d916f-680">MappingFragment</span></span>
-   <span data-ttu-id="d916f-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="d916f-681">InsertFunction</span></span>
-   <span data-ttu-id="d916f-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="d916f-682">UpdateFunction</span></span>
-   <span data-ttu-id="d916f-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="d916f-683">DeleteFunction</span></span>
-   <span data-ttu-id="d916f-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-684">EndProperty</span></span>
-   <span data-ttu-id="d916f-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="d916f-685">ComplexProperty</span></span>
-   <span data-ttu-id="d916f-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="d916f-686">ResultMapping</span></span>

<span data-ttu-id="d916f-687">Come figlio dell'elemento **MappingFragment**, **ComplexProperty**o **EndProperty** , l'elemento **ScalarProperty** esegue il mapping di una proprietà nel modello concettuale a una colonna nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="d916f-688">Come figlio dell'elemento **InsertFunction**, **UpdateFunction**o **DeleteFunction** , l'elemento **ScalarProperty** esegue il mapping di una proprietà nel modello concettuale a un parametro stored procedure.</span><span class="sxs-lookup"><span data-stu-id="d916f-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="d916f-689">L'elemento **ScalarProperty** non può contenere elementi figlio.</span><span class="sxs-lookup"><span data-stu-id="d916f-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-690">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-690">Applicable Attributes</span></span>

<span data-ttu-id="d916f-691">Gli attributi che si applicano all'elemento **ScalarProperty** variano a seconda del ruolo dell'elemento.</span><span class="sxs-lookup"><span data-stu-id="d916f-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="d916f-692">Nella tabella seguente vengono descritti gli attributi applicabili quando si utilizza l'elemento **ScalarProperty** per eseguire il mapping di una proprietà del modello concettuale a una colonna nel database:</span><span class="sxs-lookup"><span data-stu-id="d916f-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="d916f-693">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-693">Attribute Name</span></span> | <span data-ttu-id="d916f-694">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-694">Is Required</span></span> | <span data-ttu-id="d916f-695">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="d916f-696">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-696">**Name**</span></span>       | <span data-ttu-id="d916f-697">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-697">Yes</span></span>         | <span data-ttu-id="d916f-698">Nome della proprietà del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="d916f-699">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="d916f-699">**ColumnName**</span></span> | <span data-ttu-id="d916f-700">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-700">Yes</span></span>         | <span data-ttu-id="d916f-701">Nome della colonna della tabella di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="d916f-702">Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **ScalarProperty** quando viene utilizzato per eseguire il mapping di una proprietà del modello concettuale a un parametro stored procedure:</span><span class="sxs-lookup"><span data-stu-id="d916f-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="d916f-703">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-703">Attribute Name</span></span>    | <span data-ttu-id="d916f-704">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-704">Is Required</span></span> | <span data-ttu-id="d916f-705">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-706">**Nome**</span><span class="sxs-lookup"><span data-stu-id="d916f-706">**Name**</span></span>          | <span data-ttu-id="d916f-707">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-707">Yes</span></span>         | <span data-ttu-id="d916f-708">Nome della proprietà del modello concettuale di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="d916f-709">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="d916f-709">**ParameterName**</span></span> | <span data-ttu-id="d916f-710">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-710">Yes</span></span>         | <span data-ttu-id="d916f-711">Nome del parametro di cui è in corso il mapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="d916f-712">**Version**</span><span class="sxs-lookup"><span data-stu-id="d916f-712">**Version**</span></span>       | <span data-ttu-id="d916f-713">No</span><span class="sxs-lookup"><span data-stu-id="d916f-713">No</span></span>          | <span data-ttu-id="d916f-714">**Corrente** o **originale** a seconda che il valore corrente o il valore originale della proprietà debba essere usato per i controlli di concorrenza.</span><span class="sxs-lookup"><span data-stu-id="d916f-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="d916f-715">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-715">Example</span></span>

<span data-ttu-id="d916f-716">Nell'esempio seguente viene illustrato l'elemento **ScalarProperty** usato in due modi:</span><span class="sxs-lookup"><span data-stu-id="d916f-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="d916f-717">Per eseguire il mapping delle proprietà del tipo di entità **Person** alle colonne della tabella **Person**.</span><span class="sxs-lookup"><span data-stu-id="d916f-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="d916f-718">Per eseguire il mapping delle proprietà del tipo di entità **Person** ai parametri del stored procedure **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="d916f-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="d916f-719">Le stored procedure vengono dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-719">The stored procedures are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="d916f-720">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-720">Example</span></span>

<span data-ttu-id="d916f-721">Nell'esempio seguente viene illustrato l'elemento **ScalarProperty** utilizzato per eseguire il mapping delle funzioni Insert e Delete di un'associazione del modello concettuale alle stored procedure nel database.</span><span class="sxs-lookup"><span data-stu-id="d916f-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="d916f-722">Le stored procedure vengono dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-722">The stored procedures are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a><span data-ttu-id="d916f-723">Elemento UpdateFunction (MSL)</span><span class="sxs-lookup"><span data-stu-id="d916f-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="d916f-724">L'elemento **UpdateFunction** in Mapping Specification Language (MSL) esegue il mapping della funzione di aggiornamento di un tipo di entità nel modello concettuale a una stored procedure nel database sottostante.</span><span class="sxs-lookup"><span data-stu-id="d916f-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="d916f-725">Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="d916f-726">Per ulteriori informazioni, vedere elemento Function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="d916f-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="d916f-727">Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.</span><span class="sxs-lookup"><span data-stu-id="d916f-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="d916f-728">L'elemento **UpdateFunction** può essere un elemento figlio dell'elemento ModificationFunctionMapping e applicato all'elemento EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="d916f-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="d916f-729">L'elemento **UpdateFunction** può avere gli elementi figlio seguenti:</span><span class="sxs-lookup"><span data-stu-id="d916f-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="d916f-730">AssociationEnd (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="d916f-731">ComplexProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="d916f-732">ResultBinding (zero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="d916f-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="d916f-733">ScarlarProperty (zero o più elementi)</span><span class="sxs-lookup"><span data-stu-id="d916f-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="d916f-734">Attributi applicabili</span><span class="sxs-lookup"><span data-stu-id="d916f-734">Applicable Attributes</span></span>

<span data-ttu-id="d916f-735">Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **UpdateFunction** .</span><span class="sxs-lookup"><span data-stu-id="d916f-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="d916f-736">Nome attributo</span><span class="sxs-lookup"><span data-stu-id="d916f-736">Attribute Name</span></span>            | <span data-ttu-id="d916f-737">Obbligatorio</span><span class="sxs-lookup"><span data-stu-id="d916f-737">Is Required</span></span> | <span data-ttu-id="d916f-738">valore</span><span class="sxs-lookup"><span data-stu-id="d916f-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="d916f-739">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="d916f-739">**FunctionName**</span></span>          | <span data-ttu-id="d916f-740">Sì</span><span class="sxs-lookup"><span data-stu-id="d916f-740">Yes</span></span>         | <span data-ttu-id="d916f-741">Nome completo dello spazio dei nomi della stored procedure a cui la funzione di aggiornamento viene mappata.</span><span class="sxs-lookup"><span data-stu-id="d916f-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="d916f-742">La stored procedure deve essere dichiarata nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="d916f-743">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="d916f-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="d916f-744">No</span><span class="sxs-lookup"><span data-stu-id="d916f-744">No</span></span>          | <span data-ttu-id="d916f-745">Nome del parametro di output che restituisce il numero di righe interessate.</span><span class="sxs-lookup"><span data-stu-id="d916f-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="d916f-746">Esempio</span><span class="sxs-lookup"><span data-stu-id="d916f-746">Example</span></span>

<span data-ttu-id="d916f-747">L'esempio seguente è basato sul modello School e Mostra l'elemento **UpdateFunction** usato per eseguire il mapping della funzione di aggiornamento del tipo di entità **Person** alla stored procedure **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="d916f-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="d916f-748">Il stored procedure **UpdatePerson** viene dichiarato nel modello di archiviazione.</span><span class="sxs-lookup"><span data-stu-id="d916f-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
