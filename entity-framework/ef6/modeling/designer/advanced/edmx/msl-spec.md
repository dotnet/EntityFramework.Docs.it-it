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
# <a name="msl-specification"></a>Specifica MSL
MSL (Mapping Specification Language) è un linguaggio basato su XML che descrive il mapping tra il modello concettuale e il modello di archiviazione di un'applicazione Entity Framework.

In un'applicazione Entity Framework, i metadati di mapping vengono caricati da un file con estensione MSL (scritto in MSL) in fase di compilazione. Entity Framework usa i metadati di mapping in fase di esecuzione per tradurre le query sul modello concettuale in comandi specifici dell'archivio.

Il Entity Framework Designer (EF designer) archivia le informazioni di mapping in un file con estensione edmx in fase di progettazione. In fase di compilazione, il Entity Designer utilizza le informazioni contenute in un file con estensione edmx per creare il file con estensione msl necessario per Entity Framework in fase di esecuzione

I nomi di tutti i tipi di modelli concettuale o di archiviazione a cui viene fatto riferimento in MSL devono essere qualificati dai rispettivi nomi dello spazio dei nomi. Per informazioni sul nome dello spazio dei nomi del modello concettuale, vedere [specifica CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec). Per informazioni sul nome dello spazio dei nomi del modello di archiviazione, vedere [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Le versioni di MSL sono differenziate in base agli spazi dei nomi XML.

| Versione MSL | Spazio dei nomi XML                                        |
|:------------|:-----------------------------------------------------|
| MSL V1      | urn: schemas-microsoft-com: Windows: archiviazione: mapping: CS |
| MSL V2      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| MSL V3      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Elemento Alias (MSL)

L'elemento **alias** in Mapping Specification Language (MSL) è un elemento figlio dell'elemento mapping utilizzato per definire gli alias per gli spazi dei nomi del modello concettuale e del modello di archiviazione. I nomi di tutti i tipi di modelli concettuale o di archiviazione a cui viene fatto riferimento in MSL devono essere qualificati dai rispettivi nomi dello spazio dei nomi. Per informazioni sul nome dello spazio dei nomi del modello concettuale, vedere elemento schema (CSDL). Per informazioni sul nome dello spazio dei nomi del modello di archiviazione, vedere elemento schema (SSDL).

L'elemento **alias** non può contenere elementi figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **alias** .

| Nome attributo | Obbligatorio | Valore                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **Chiave**        | Sì         | Alias per lo spazio dei nomi specificato dall'attributo **value** . |
| **Valore**      | Sì         | Spazio dei nomi per il quale il valore dell'elemento **chiave** è un alias.     |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **alias** che definisce un alias, `c` , per i tipi definiti nel modello concettuale.

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

## <a name="associationend-element-msl"></a>Elemento AssociationEnd (MSL)

L'elemento **AssociationEnd** in Mapping Specification Language (MSL) viene utilizzato quando viene eseguito il mapping delle funzioni di modifica di un tipo di entità nel modello concettuale alle stored procedure nel database sottostante. Se una modifica stored procedure accetta un parametro il cui valore è contenuto in una proprietà di associazione, l'elemento **AssociationEnd** esegue il mapping del valore della proprietà al parametro. Per ulteriori informazioni, vedere l'esempio seguente.

Per ulteriori informazioni sul mapping delle funzioni di modifica dei tipi di entità alle stored procedure, vedere elemento ModificationFunctionMapping (MSL) e procedura dettagliata: mapping di un'entità alle stored procedure.

L'elemento **AssociationEnd** può avere gli elementi figlio seguenti:

-   ScalarProperty

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **AssociationEnd** .

| Nome attributo     | Obbligatorio | valore                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | Sì         | Nome dell'associazione di cui è in corso il mapping.                                                                                                                                 |
| **From**           | Sì         | Valore dell'attributo **FromRole** della proprietà di navigazione che corrisponde all'associazione di cui viene eseguito il mapping. Per ulteriori informazioni, vedere elemento NavigationProperty (CSDL). |
| **To**             | Sì         | Valore dell'attributo **ToRole** della proprietà di navigazione che corrisponde all'associazione di cui viene eseguito il mapping. Per ulteriori informazioni, vedere elemento NavigationProperty (CSDL).   |

### <a name="example"></a>Esempio

Si consideri il tipo di entità del modello concettuale seguente:

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

Si consideri inoltre la seguente stored procedure:

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

Per eseguire il mapping della funzione di aggiornamento dell' `Course` entità a questa stored procedure, è necessario fornire un valore al parametro **DepartmentID** . Il valore di `DepartmentID` non corrisponde a una proprietà sul tipo di entità. È contenuto in un'associazione indipendente il cui mapping è illustrato di seguito:

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

Il codice seguente illustra l'elemento **AssociationEnd** usato per eseguire il mapping della proprietà **DepartmentID** dell'associazione del ** \_ \_ reparto Course FK** al stored procedure **UpdateCourse** (a cui viene mappata la funzione di aggiornamento del tipo di entità **Course** ):

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

## <a name="associationsetmapping-element-msl"></a>Elemento AssociationSetMapping (MSL)

L'elemento **AssociationSetMapping** in Mapping Specification Language (MSL) definisce il mapping tra un'associazione nel modello concettuale e le colonne della tabella nel database sottostante.

Le associazioni del modello concettuale sono tipi le cui proprietà rappresentano colonne di chiavi primarie ed esterne nel database sottostante. L'elemento **AssociationSetMapping** utilizza due elementi EndProperty per definire i mapping tra le proprietà del tipo di associazione e le colonne nel database. È possibile definire condizioni su questi mapping con l'elemento Condition. Per eseguire il mapping delle funzioni di inserimento, aggiornamento ed eliminazione per le associazioni alle stored procedure del database, utilizzare l'elemento ModificationFunctionMapping. Definire mapping di sola lettura tra le associazioni e le colonne della tabella usando una stringa Entity SQL in un elemento QueryView.

> [!NOTE]
> Se per un'associazione nel modello concettuale è definito un vincolo referenziale, non è necessario eseguire il mapping dell'associazione con un elemento **AssociationSetMapping** . Se è presente un elemento **AssociationSetMapping** per un'associazione con un vincolo referenziale, i mapping definiti nell'elemento **AssociationSetMapping** verranno ignorati. Per ulteriori informazioni, vedere elemento ReferentialConstraint (CSDL).

L'elemento **AssociationSetMapping** può avere gli elementi figlio seguenti

-   QueryView (zero o un elemento)
-   EndProperty (zero o due elementi)
-   Condition (zero o più elementi)
-   ModificationFunctionMapping (zero o un elemento)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **AssociationSetMapping** .

| Nome attributo     | Obbligatorio | valore                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **Nome**           | Sì         | Nome del set di associazioni del modello concettuale di cui è in corso il mapping.                      |
| **TypeName**       | No          | Nome completo dello spazio dei nomi del tipo di associazione del modello concettuale di cui è in corso il mapping. |
| **StoreEntitySet** | No          | Nome della tabella di cui è in corso il mapping.                                                 |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **AssociationSetMapping** in cui viene eseguito il mapping dell'associazione del ** \_ \_ reparto dei corsi FK** nel modello concettuale alla tabella **Course** nel database. I mapping tra le proprietà del tipo di associazione e le colonne della tabella sono specificati negli elementi **EndProperty** figlio.

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

## <a name="complexproperty-element-msl"></a>Elemento ComplexProperty (MSL)

Un elemento **ComplexProperty** in Mapping Specification Language (MSL) definisce il mapping tra una proprietà di tipo complesso in un tipo di entità del modello concettuale e le colonne della tabella nel database sottostante. I mapping delle colonne delle proprietà sono specificati in elementi ScalarProperty figlio.

L'elemento della proprietà **complexType** può presentare gli elementi figlio seguenti:

-   ScalarProperty (zero o più elementi)
-   **ComplexProperty** (zero o più)
-   ComplextTypeMapping (zero o più elementi)
-   Condition (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **ComplexProperty** :

| Nome attributo | Obbligatorio | valore                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome della proprietà complessa di un tipo di entità nel modello concettuale di cui è in corso il mapping. |
| **TypeName**   | No          | Nome qualificato di spazio dei nomi del tipo di proprietà del modello concettuale.                              |

### <a name="example"></a>Esempio

Di seguito viene riportato un esempio basato sul modello School. Il tipo complesso seguente è stato aggiunto al modello concettuale:

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

Le proprietà **LastName** e **FirstName** del tipo di entità **Person** sono state sostituite con una proprietà complessa, **Name**:

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

Il MSL seguente mostra l'elemento **ComplexProperty** usato per eseguire il mapping della proprietà **Name** alle colonne nel database sottostante:

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

## <a name="complextypemapping-element-msl"></a>Elemento ComplexTypeMapping (MSL)

L'elemento **ComplexTypeMapping** in Mapping Specification Language (MSL) è un elemento figlio dell'elemento ResultMapping e definisce il mapping tra un'importazione di funzioni nel modello concettuale e una stored procedure nel database sottostante quando sono soddisfatte le condizioni seguenti:

-   Un tipo complesso concettuale viene restituito dall'importazione di funzioni.
-   I nomi delle colonne restituite dalla stored procedure non corrispondono esattamente ai nomi delle proprietà del tipo complesso.

Per impostazione predefinita, il mapping tra le colonne restituite da una stored procedure e un tipo complesso è basato sui nomi delle colonne e delle proprietà. Se i nomi delle colonne non corrispondono esattamente ai nomi delle proprietà, è necessario utilizzare l'elemento **ComplexTypeMapping** per definire il mapping. Per un esempio del mapping predefinito, vedere elemento FunctionImportMapping (MSL).

L'elemento **ComplexTypeMapping** può avere gli elementi figlio seguenti:

-   ScalarProperty (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **ComplexTypeMapping** .

| Nome attributo | Obbligatorio | valore                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | Sì         | Nome completo dello spazio dei nomi del tipo complesso di cui è in corso il mapping. |

### <a name="example"></a>Esempio

Si consideri la stored procedure seguente:

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

Si consideri anche il tipo complesso del modello concettuale seguente:

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

Per creare un'importazione di funzioni che restituisce istanze del tipo complesso precedente, il mapping tra le colonne restituite dal stored procedure e il tipo di entità deve essere definito in un elemento **ComplexTypeMapping** :

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

## <a name="condition-element-msl"></a>Elemento Condition (MSL)

L'elemento **Condition** in Mapping Specification Language (MSL) posiziona le condizioni sui mapping tra il modello concettuale e il database sottostante. Il mapping definito all'interno di un nodo XML è valido se vengono soddisfatte tutte le condizioni, come specificato negli elementi della **condizione** figlio. In caso contrario, il mapping non è valido. Se, ad esempio, un elemento MappingFragment contiene uno o più elementi figlio **Condition** , il mapping definito all'interno del nodo **MappingFragment** sarà valido solo se vengono soddisfatte tutte le condizioni degli elementi della **condizione** figlio.

Ogni condizione può essere applicata a un **nome** (il nome di una proprietà dell'entità del modello concettuale, specificata dall'attributo **Name** ) o a un **ColumnName** (il nome di una colonna nel database, specificato dall'attributo **ColumnName** ). Quando viene impostato l'attributo **Name** , la condizione viene verificata rispetto al valore della proprietà di un'entità. Quando viene impostato l'attributo **ColumnName** , la condizione viene verificata rispetto a un valore di colonna. È possibile specificare solo uno degli attributi **Name** o **ColumnName** in un elemento **Condition** .

> [!NOTE]
> Quando l'elemento **Condition** viene utilizzato all'interno di un elemento FunctionImportMapping, solo l'attributo **Name** non è applicabile.

L'elemento **Condition** può essere figlio dei seguenti elementi:

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

L'elemento **Condition** non può avere elementi figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **Condition** :

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | No          | Nome della colonna della tabella il cui valore viene utilizzato per valutare la condizione.                                                                                                                                                                                                                   |
| **IsNull**     | No          | **True** o **false**. Se il valore è **true** e il valore della colonna è **null**o se il valore è **false** e il valore della colonna non è **null**, la condizione è true. In caso contrario, la condizione è false. <br/> Impossibile utilizzare contemporaneamente gli attributi **IsNull** e **value** . |
| **Valore**      | No          | Valore con cui viene confrontato il valore della colonna. Se i valori sono uguali, la condizione è true. In caso contrario, la condizione è false. <br/> Impossibile utilizzare contemporaneamente gli attributi **IsNull** e **value** .                                                                       |
| **Nome**       | No          | Nome della proprietà dell'entità del modello concettuale il cui valore viene utilizzato per valutare la condizione. <br/> Questo attributo non è applicabile se l'elemento **Condition** viene utilizzato all'interno di un elemento FunctionImportMapping.                                                                           |

### <a name="example"></a>Esempio

Nell'esempio seguente vengono mostrati gli elementi **Condition** come elementi figlio di elementi **MappingFragment** . Quando **Hiret** non è null e **EnrollmentDate** è null, viene eseguito il mapping dei dati tra il tipo **SchoolModel. Instructor** e le colonne **PersonID** e **Hired** della tabella **Person** . Quando **EnrollmentDate** non è null e il valore di **Hiret** è null, viene eseguito il mapping dei dati tra il tipo **SchoolModel. Student** e le colonne **PersonID** e di **registrazione** della tabella **Person** .

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

## <a name="deletefunction-element-msl"></a>Elemento DeleteFunction (MSL)

L'elemento **DeleteFunction** in Mapping Specification Language (MSL) esegue il mapping della funzione Delete di un tipo di entità o di un'associazione nel modello concettuale a una stored procedure nel database sottostante. Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione. Per ulteriori informazioni, vedere elemento Function (SSDL).

> [!NOTE]
> Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.

### <a name="deletefunction-applied-to-entitytypemapping"></a>DeleteFunction applicato a EntityTypeMapping

Quando applicato all'elemento EntityTypeMapping, l'elemento **DeleteFunction** esegue il mapping della funzione di eliminazione di un tipo di entità nel modello concettuale a un stored procedure.

Quando viene applicato a un elemento **EntityTypeMapping** , l'elemento **DeleteFunction** può includere i seguenti elementi figlio:

-   AssociationEnd (zero o più elementi)
-   ComplexProperty (zero o più elementi)
-   ScarlarProperty (zero o più elementi)

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **DeleteFunction** quando viene applicato a un elemento **EntityTypeMapping** .

| Nome attributo            | Obbligatorio | valore                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Sì         | Nome completo dello spazio dei nomi della stored procedure a cui la funzione di eliminazione viene mappata. La stored procedure deve essere dichiarata nel modello di archiviazione. |
| **RowsAffectedParameter** | No          | Nome del parametro di output che restituisce il numero di righe interessate.                                                                               |

#### <a name="example"></a>Esempio

L'esempio seguente è basato sul modello School e Mostra l'elemento **DeleteFunction** che mappa la funzione Delete del tipo di entità **Person** alla stored procedure **DeletePerson** . Il stored procedure **DeletePerson** viene dichiarato nel modello di archiviazione.

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

### <a name="deletefunction-applied-to-associationsetmapping"></a>DeleteFunction applicato ad AssociationSetMapping

Quando applicato all'elemento AssociationSetMapping, l'elemento **DeleteFunction** esegue il mapping della funzione Delete di un'associazione nel modello concettuale a un stored procedure.

L'elemento **DeleteFunction** può avere gli elementi figlio seguenti quando viene applicato all'elemento **AssociationSetMapping** :

-   EndProperty

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **DeleteFunction** quando viene applicato all'elemento **AssociationSetMapping** .

| Nome attributo            | Obbligatorio | valore                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Sì         | Nome completo dello spazio dei nomi della stored procedure a cui la funzione di eliminazione viene mappata. La stored procedure deve essere dichiarata nel modello di archiviazione. |
| **RowsAffectedParameter** | No          | Nome del parametro di output che restituisce il numero di righe interessate.                                                                               |

#### <a name="example"></a>Esempio

L'esempio seguente è basato sul modello School e Mostra l'elemento **DeleteFunction** usato per eseguire il mapping della funzione Delete dell'associazione **CourseInstructor** a **DeleteCourseInstructor** stored procedure. Il stored procedure **DeleteCourseInstructor** viene dichiarato nel modello di archiviazione.

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

## <a name="endproperty-element-msl"></a>Elemento EndProperty (MSL)

L'elemento **EndProperty** in Mapping Specification Language (MSL) definisce il mapping tra un'entità finale o una funzione di modifica di un'associazione del modello concettuale e il database sottostante. Il mapping delle colonne delle proprietà viene specificato in un elemento ScalarProperty figlio.

Quando si usa un elemento **EndProperty** per definire il mapping per la fine di un'associazione del modello concettuale, è un elemento figlio di un elemento AssociationSetMapping. Quando l'elemento **EndProperty** viene usato per definire il mapping per una funzione di modifica di un'associazione del modello concettuale, è un elemento figlio di un elemento InsertFunction o DeleteFunction.

L'elemento **EndProperty** può avere gli elementi figlio seguenti:

-   ScalarProperty (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **EndProperty** :

| Nome attributo | Obbligatorio | valore                                                 |
|:---------------|:------------|:------------------------------------------------------|
| Nome           | Sì         | Nome dell'entità finale dell'associazione di cui è in corso il mapping. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **AssociationSetMapping** in cui viene eseguito il mapping dell'associazione del ** \_ \_ reparto Course FK** nel modello concettuale alla tabella **Course** nel database. I mapping tra le proprietà del tipo di associazione e le colonne della tabella sono specificati negli elementi **EndProperty** figlio.

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

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **EndProperty** che consente di eseguire il mapping delle funzioni Insert e Delete di un'associazione (**CourseInstructor**) alle stored procedure nel database sottostante. Le funzioni a cui viene eseguito il mapping vengono dichiarate nel modello di archiviazione.

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

## <a name="entitycontainermapping-element-msl"></a>Elemento EntityContainerMapping (MSL)

L'elemento **EntityContainerMapping** in Mapping Specification Language (MSL) esegue il mapping del contenitore di entità nel modello concettuale al contenitore di entità nel modello di archiviazione. L'elemento **EntityContainerMapping** è un elemento figlio dell'elemento mapping.

L'elemento **EntityContainerMapping** può includere i seguenti elementi figlio (nell'ordine elencato):

-   EntitySetMapping (zero o più elementi)
-   AssociationSetMapping (zero o più elementi)
-   FunctionImportMapping (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityContainerMapping** .

| Nome attributo            | Obbligatorio | valore                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | Sì         | Nome del contenitore di entità del modello di archiviazione di cui è in corso il mapping.                                                                                                                                                                                     |
| **CdmEntityContainer**    | Sì         | Nome del contenitore di entità del modello concettuale di cui è in corso il mapping.                                                                                                                                                                                  |
| **GenerateUpdateViews**   | No          | **True** o **false**. Se **false**, non viene generata alcuna vista aggiornamento. Questo attributo deve essere impostato su **false** quando si dispone di un mapping di sola lettura che non sarebbe valido perché i dati non possono essere completati correttamente. <br/> Il valore predefinito è **True**. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainerMapping** che esegue il mapping del contenitore **SchoolModelEntities** (il contenitore di entità del modello concettuale) al contenitore **SchoolModelStoreContainer** (il contenitore di entità del modello di archiviazione):

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

## <a name="entitysetmapping-element-msl"></a>Elemento EntitySetMapping (MSL)

L'elemento **EntitySetMapping** in Mapping Specification Language (MSL) esegue il mapping di tutti i tipi in un set di entità del modello concettuale ai set di entità nel modello di archiviazione. Un set di entità nel modello concettuale è un contenitore logico per istanze di entità dello stesso tipo (e tipi derivati). Un set di entità nel modello di archiviazione rappresenta una tabella o visualizzazione nel database sottostante. Il set di entità del modello concettuale viene specificato dal valore dell'attributo **Name** dell'elemento **EntitySetMapping** . La tabella o la vista di cui è stato eseguito il mapping viene specificata dall'attributo **StoreEntitySet** in ogni elemento MappingFragment figlio o nell'elemento **EntitySetMapping** stesso.

L'elemento **EntitySetMapping** può avere gli elementi figlio seguenti:

-   EntityTypeMapping (zero o più elementi)
-   QueryView (zero o un elemento)
-   MappingFragment (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySetMapping** .

| Nome attributo           | Obbligatorio | valore                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                 | Sì         | Nome del set di entità del modello concettuale di cui è in corso il mapping.                                                                                                                                                             |
| **TypeName** **1**       | No          | Nome del tipo di entità del modello concettuale di cui è in corso il mapping.                                                                                                                                                            |
| **StoreEntitySet** **1** | No          | Nome del set di entità del modello di archiviazione a cui viene eseguito il mapping.                                                                                                                                                             |
| **Makecolumnsdistinct impostato**  | No          | **True** o **false** , a seconda che vengano restituite solo righe distinte. <br/> Se questo attributo è impostato su **true**, l'attributo **GenerateUpdateViews** dell'elemento EntityContainerMapping deve essere impostato su **false**. |

 

**1** gli attributi **typeName** e **StoreEntitySet** possono essere utilizzati al posto degli elementi figlio EntityTypeMapping e MappingFragment per eseguire il mapping di un singolo tipo di entità a una singola tabella.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntitySetMapping** che esegue il mapping di tre tipi (un tipo di base e due tipi derivati) nel set di entità **Courses** del modello concettuale a tre tabelle diverse del database sottostante. Le tabelle vengono specificate dall'attributo **StoreEntitySet** in ogni elemento **MappingFragment** .

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

## <a name="entitytypemapping-element-msl"></a>Elemento EntityTypeMapping (MSL)

L'elemento **EntityTypeMapping** in Mapping Specification Language (MSL) definisce il mapping tra un tipo di entità nel modello concettuale e le tabelle o le viste nel database sottostante. Per informazioni sui tipi di entità del modello concettuale e sulle tabelle o visualizzazioni del database sottostante, vedere Elemento EntityType (CSDL) e Elemento EntitySet (SSDL). Il tipo di entità del modello concettuale di cui è in corso il mapping viene specificato dall'attributo **typeName** dell'elemento **EntityTypeMapping** . La tabella o la vista di cui viene eseguito il mapping viene specificata dall'attributo **StoreEntitySet** dell'elemento MappingFragment figlio.

L'elemento figlio ModificationFunctionMapping può essere utilizzato per eseguire il mapping delle funzioni di inserimento, aggiornamento o eliminazione dei tipi di entità alle stored procedure nel database.

L'elemento **EntityTypeMapping** può avere gli elementi figlio seguenti:

-   MappingFragment (zero o più elementi)
-   ModificationFunctionMapping (zero o un elemento)
-   ScalarProperty
-   Condizione

> [!NOTE]
> Gli elementi **MappingFragment** e **ModificationFunctionMapping** non possono essere elementi figlio dell'elemento **EntityTypeMapping** nello stesso momento.


> [!NOTE]
> Gli elementi **ScalarProperty** e **Condition** possono essere solo elementi figlio dell'elemento **EntityTypeMapping** quando vengono usati all'interno di un elemento FunctionImportMapping.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityTypeMapping** .

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | Sì         | Nome completo dello spazio dei nomi del tipo di entità del modello concettuale di cui è in corso il mapping. <br/> Se il tipo è astratto o un tipo derivato, il valore deve essere `IsOfType(Namespace-qualified_type_name)`. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento EntitySetMapping con due elementi **EntityTypeMapping** figlio. Nel primo elemento **EntityTypeMapping** , il tipo di entità **SchoolModel. Person** viene mappato alla tabella **Person** . Nel secondo elemento **EntityTypeMapping** , viene eseguito il mapping della funzionalità di aggiornamento del tipo **SchoolModel. Person** a un stored procedure, **UpdatePerson**, nel database.

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

### <a name="example"></a>Esempio

Nell'esempio successivo viene mostrato il mapping di una gerarchia di tipo in cui il tipo radice è astratto. Si noti l'uso della `IsOfType` sintassi per gli attributi **typeName** .

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

## <a name="functionimportmapping-element-msl"></a>Elemento FunctionImportMapping (MSL)

L'elemento **FunctionImportMapping** in Mapping Specification Language (MSL) definisce il mapping tra un'importazione di funzioni nel modello concettuale e una stored procedure o una funzione nel database sottostante. Le importazioni di funzioni devono essere dichiarate nel modello concettuale mentre le stored procedure nel modello di archiviazione. Per ulteriori informazioni, vedere elemento FunctionImport (CSDL) e elemento Function (SSDL).

> [!NOTE]
> Per impostazione predefinita, se un'importazione di funzioni restituisce un tipo di entità del modello concettuale o un tipo complesso, i nomi delle colonne restituiti dalla stored procedure sottostante devono corrispondere esattamente ai nomi delle proprietà nel tipo di modello concettuale. Se i nomi delle colonne non corrispondono esattamente ai nomi delle proprietà, il mapping deve essere definito in un elemento ResultMapping.

L'elemento **FunctionImportMapping** può avere gli elementi figlio seguenti:

-   ResultMapping (zero o più)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **FunctionImportMapping** :

| Nome attributo         | Obbligatorio | valore                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | Sì         | Nome dell'importazione di funzioni nel modello concettuale di cui è in corso il mapping.           |
| **FunctionName**       | Sì         | Nome completo dello spazio dei nomi della funzione nel modello di archiviazione di cui è in corso il mapping. |

### <a name="example"></a>Esempio

Di seguito viene riportato un esempio basato sul modello School. Si consideri la funzione seguente nel modello di archiviazione:

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

Si consideri anche questa importazione di funzioni nel modello concettuale:

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

Nell'esempio seguente viene illustrato un elemento **FunctionImportMapping** usato per eseguire il mapping tra la funzione e l'importazione di funzioni sopra elencate:

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>Elemento InsertFunction (MSL)

L'elemento **InsertFunction** in Mapping Specification Language (MSL) esegue il mapping della funzione di inserimento di un tipo di entità o di un'associazione nel modello concettuale a una stored procedure nel database sottostante. Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione. Per ulteriori informazioni, vedere elemento Function (SSDL).

> [!NOTE]
> Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.

L'elemento **InsertFunction** può essere un elemento figlio dell'elemento ModificationFunctionMapping e applicato all'elemento EntityTypeMapping o AssociationSetMapping.

### <a name="insertfunction-applied-to-entitytypemapping"></a>InsertFunction applicato a EntityTypeMapping

Quando applicato all'elemento EntityTypeMapping, l'elemento **InsertFunction** esegue il mapping della funzione di inserimento di un tipo di entità nel modello concettuale a un stored procedure.

Quando viene applicato a un elemento **EntityTypeMapping** , l'elemento **InsertFunction** può includere i seguenti elementi figlio:

-   AssociationEnd (zero o più elementi)
-   ComplexProperty (zero o più elementi)
-   ResultBinding (zero o un elemento)
-   ScarlarProperty (zero o più elementi)

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **InsertFunction** quando vengono applicati a un elemento **EntityTypeMapping** .

| Nome attributo            | Obbligatorio | valore                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Sì         | Nome completo dello spazio dei nomi della stored procedure a cui la funzione di inserimento viene mappata. La stored procedure deve essere dichiarata nel modello di archiviazione. |
| **RowsAffectedParameter** | No          | Nome del parametro di output che restituisce il numero di righe interessate.                                                                               |

#### <a name="example"></a>Esempio

L'esempio seguente è basato sul modello School e Mostra l'elemento **InsertFunction** usato per eseguire il mapping della funzione di inserimento del tipo di entità Person alla stored procedure **InsertPerson** . Il stored procedure **InsertPerson** viene dichiarato nel modello di archiviazione.

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
### <a name="insertfunction-applied-to-associationsetmapping"></a>InsertFunction applicato ad AssociationSetMapping

Quando applicato all'elemento AssociationSetMapping, l'elemento **InsertFunction** esegue il mapping della funzione di inserimento di un'associazione nel modello concettuale a un stored procedure.

L'elemento **InsertFunction** può avere gli elementi figlio seguenti quando viene applicato all'elemento **AssociationSetMapping** :

-   EndProperty

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **InsertFunction** quando viene applicato all'elemento **AssociationSetMapping** .

| Nome attributo            | Obbligatorio | valore                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Sì         | Nome completo dello spazio dei nomi della stored procedure a cui la funzione di inserimento viene mappata. La stored procedure deve essere dichiarata nel modello di archiviazione. |
| **RowsAffectedParameter** | No          | Nome del parametro di output che restituisce il numero di righe interessate.                                                                               |

#### <a name="example"></a>Esempio

L'esempio seguente è basato sul modello School e Mostra l'elemento **InsertFunction** usato per eseguire il mapping della funzione di inserimento dell'associazione **CourseInstructor** a **InsertCourseInstructor** stored procedure. Il stored procedure **InsertCourseInstructor** viene dichiarato nel modello di archiviazione.

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

## <a name="mapping-element-msl"></a>Elemento Mapping (MSL)

L'elemento **mapping** in Mapping Specification Language (MSL) contiene informazioni per il mapping di oggetti definiti in un modello concettuale a un database (come descritto in un modello di archiviazione). Per ulteriori informazioni, vedere Specification CSDL e SSDL Specification.

L'elemento **mapping** è l'elemento radice per una specifica di mapping. Lo spazio dei nomi XML per le specifiche di mapping è https://schemas.microsoft.com/ado/2009/11/mapping/cs .

L'elemento Mapping può includere i seguenti elementi figlio (nell'ordine elencato):

-   Alias (zero o più elementi)
-   EntityContainerMapping (esattamente un elemento)

I nomi di tipi di modelli concettuale e di archiviazione a cui viene fatto riferimento in MSL devono essere qualificati dai rispettivi nomi dello spazio dei nomi. Per informazioni sul nome dello spazio dei nomi del modello concettuale, vedere elemento schema (CSDL). Per informazioni sul nome dello spazio dei nomi del modello di archiviazione, vedere elemento schema (SSDL). Gli alias per gli spazi dei nomi utilizzati in MSL possono essere definiti con l'elemento Alias.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **mapping** .

| Nome attributo | Obbligatorio | valore                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **Spazio**      | Sì         | **C-S**. Si tratta di un valore fisso e non può essere modificato. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento di **mapping** basato su una parte del modello School. Per ulteriori informazioni sul modello School, vedere Guida introduttiva (Entity Framework):

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

## <a name="mappingfragment-element-msl"></a>Elemento MappingFragment (MSL)

L'elemento **MappingFragment** in Mapping Specification Language (MSL) definisce il mapping tra le proprietà di un tipo di entità del modello concettuale e una tabella o una vista nel database. Per informazioni sui tipi di entità del modello concettuale e sulle tabelle o visualizzazioni del database sottostante, vedere Elemento EntityType (CSDL) e Elemento EntitySet (SSDL). **MappingFragment** può essere un elemento figlio dell'elemento EntityTypeMapping o EntitySetMapping.

L'elemento **MappingFragment** può avere gli elementi figlio seguenti:

-   ComplexType (zero o più elementi)
-   ScalarProperty (zero o più elementi)
-   Condition (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **MappingFragment** .

| Nome attributo          | Obbligatorio | valore                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | Sì         | Nome della tabella o visualizzazione di cui è in corso il mapping.                                                                                                                                                                           |
| **Makecolumnsdistinct impostato** | No          | **True** o **false** , a seconda che vengano restituite solo righe distinte. <br/> Se questo attributo è impostato su **true**, l'attributo **GenerateUpdateViews** dell'elemento EntityContainerMapping deve essere impostato su **false**. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **MappingFragment** come figlio di un elemento **EntityTypeMapping** . In questo esempio, le proprietà del tipo **Course** nel modello concettuale vengono mappate alle colonne della tabella **Course** nel database.

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

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **MappingFragment** come figlio di un elemento **EntitySetMapping** . Come nell'esempio precedente, le proprietà del tipo **Course** nel modello concettuale vengono mappate alle colonne della tabella **Course** nel database.

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

## <a name="modificationfunctionmapping-element-msl"></a>Elemento ModificationFunctionMapping (MSL)

L'elemento **ModificationFunctionMapping** in Mapping Specification Language (MSL) esegue il mapping delle funzioni di inserimento, aggiornamento ed eliminazione di un tipo di entità del modello concettuale alle stored procedure nel database sottostante. L'elemento **ModificationFunctionMapping** può inoltre eseguire il mapping delle funzioni Insert e DELETE per le associazioni molti-a-molti nel modello concettuale alle stored procedure nel database sottostante. Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione. Per ulteriori informazioni, vedere elemento Function (SSDL).

> [!NOTE]
> Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.


> [!NOTE]
> Se esiste un mapping tra le funzioni di modifica per un'entità in una gerarchia di ereditarietà e stored procedure, è necessario eseguire il mapping delle funzioni di modifica per tutti i tipi nella gerarchia a stored procedure.

L'elemento **ModificationFunctionMapping** può essere un elemento figlio dell'elemento EntityTypeMapping o AssociationSetMapping.

L'elemento **ModificationFunctionMapping** può avere gli elementi figlio seguenti:

-   DeleteFunction (zero o un elemento)
-   InsertFunction (zero o un elemento)
-   UpdateFunction (zero o un elemento)

Nessun attributo applicabile all'elemento **ModificationFunctionMapping** .

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato il mapping del set di entità per il set di entità **people** nel modello School. Oltre al mapping delle colonne per il tipo di entità **Person** , viene visualizzato il mapping delle funzioni di inserimento, aggiornamento ed eliminazione del tipo **Person** . Le funzioni a cui viene eseguito il mapping vengono dichiarate nel modello di archiviazione.

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

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato il mapping del set di associazioni per il set di associazioni **CourseInstructor** nel modello School. Oltre al mapping delle colonne per l'associazione **CourseInstructor** , viene visualizzato il mapping delle funzioni di inserimento ed eliminazione dell'associazione **CourseInstructor** . Le funzioni a cui viene eseguito il mapping vengono dichiarate nel modello di archiviazione.

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
 

 

## <a name="queryview-element-msl"></a>Elemento QueryView (MSL)

L'elemento **QueryView** in Mapping Specification Language (MSL) definisce un mapping di sola lettura tra un tipo di entità o un'associazione nel modello concettuale e una tabella nel database sottostante. Il mapping viene definito con una query Entity SQL valutata rispetto al modello di archiviazione e il set di risultati viene espresso in termini di entità o associazione nel modello concettuale. Poiché le visualizzazioni di query sono di sola lettura, non è possibile utilizzare comandi di aggiornamento standard per aggiornare i tipi definiti da tali visualizzazioni. A tale scopo è possibile utilizzare le funzioni di modifica. Per altre informazioni, vedere Procedura: eseguire il mapping delle funzioni di modifica alle stored procedure.

> [!NOTE]
> Nell'elemento **QueryView** , le espressioni Entity SQL che contengono **GroupBy**, aggregazioni di gruppo o proprietà di navigazione non sono supportate.

 

L'elemento **QueryView** può essere un elemento figlio dell'elemento EntitySetMapping o AssociationSetMapping. Nel primo caso, la visualizzazione di query definisce un mapping di sola lettura per un'entità nel modello concettuale. Nel secondo caso, la visualizzazione di query definisce un mapping di sola lettura per un'associazione nel modello concettuale.

> [!NOTE]
> Se l'elemento **AssociationSetMapping** è per un'associazione con un vincolo referenziale, l'elemento **AssociationSetMapping** viene ignorato. Per ulteriori informazioni, vedere elemento ReferentialConstraint (CSDL).

L'elemento **QueryView** non può contenere elementi figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **QueryView** .

| Nome attributo | Obbligatorio | valore                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | No          | Nome del tipo di modello concettuale mappato dalla visualizzazione di query. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **QueryView** come figlio dell'elemento **EntitySetMapping** e viene definito un mapping della visualizzazione di query per il tipo di entità **Department** nel modello School.

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

Poiché la query restituisce solo un subset dei membri del tipo **Department** nel modello di archiviazione, il tipo **Department** del modello School è stato modificato in base a questo mapping come segue:

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

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **QueryView** come figlio di un elemento **AssociationSetMapping** e viene definito un mapping di sola lettura per l' `FK_Course_Department` associazione nel modello School.

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
 
### <a name="comments"></a>Commenti

È possibile definire le visualizzazioni di query per consentire gli scenari seguenti:

-   Definire un'entità nel modello concettuale che non includa tutte le proprietà dell'entità nel modello di archiviazione. Sono incluse le proprietà che non dispongono di valori predefiniti e non supportano valori **null** .
-   Eseguire il mapping delle colonne calcolate nel modello di archiviazione alle proprietà dei tipi di entità nel modello concettuale.
-   Definire un mapping in cui le condizioni utilizzate per partizionare le entità nel modello concettuale non siano basate sull'uguaglianza. Quando si specifica un mapping condizionale usando l'elemento **Condition** , la condizione fornita deve corrispondere al valore specificato. Per ulteriori informazioni, vedere elemento Condition (MSL).
-   Eseguire il mapping della stessa colonna nel modello di archiviazione a più tipi nel modello concettuale.
-   Eseguire il mapping di più tipi alla stessa tabella.
-   Definire associazioni nel modello concettuale che non siano basate sulle chiavi esterne dello schema relazionale.
-   Utilizzare la logica di business personalizzata per impostare il valore delle proprietà nel modello concettuale. Ad esempio, è possibile eseguire il mapping del valore stringa "T" nell'origine dati a un valore **true**, un valore booleano, nel modello concettuale.
-   Definire filtri condizionali per i risultati della query.
-   Applicare ai dati del modello concettuale un numero di restrizioni minore di quelle applicate al modello di archiviazione. Ad esempio, è possibile creare una proprietà nel modello concettuale Nullable anche se la colonna a cui è stato eseguito il mapping non supporta valori **null**.

Le considerazioni seguenti riguardano la definizione delle visualizzazioni di query per le entità:

-   Le visualizzazioni di query sono di sola lettura. È possibile applicare aggiornamenti alle entità solo utilizzando le funzioni di modifica.
-   Quando viene definito un tipo di entità da una visualizzazione di query, è necessario definire anche tutte le entità correlate dalle visualizzazioni di query.
-   Quando si esegue il mapping di un'associazione many-to-many a un'entità nel modello di archiviazione che rappresenta una tabella dei collegamenti nello schema relazionale, è necessario definire un elemento **QueryView** nell'elemento **AssociationSetMapping** per la tabella dei collegamenti.
-   È necessario definire le visualizzazioni di query per tutti i tipi di una gerarchia di tipi. È possibile eseguire questa operazione nei modi seguenti:
-   -   Con un singolo elemento **QueryView** che specifica una singola query di Entity SQL che restituisce un'Unione di tutti i tipi di entità nella gerarchia.
    -   Con un singolo elemento **QueryView** che specifica una singola query di Entity SQL che usa l'operatore case per restituire un tipo di entità specifico nella gerarchia in base a una condizione specifica.
    -   Con un elemento **QueryView** aggiuntivo per un tipo specifico nella gerarchia. In questo caso, usare l'attributo **typeName** dell'elemento **QueryView** per specificare il tipo di entità per ogni visualizzazione.
-   Quando viene definita una visualizzazione di query, non è possibile specificare l'attributo **StorageSetName** nell'elemento **EntitySetMapping** .
-   Quando viene definita una visualizzazione di query, l'elemento **EntitySetMapping**non può contenere anche mapping di **Proprietà** .

## <a name="resultbinding-element-msl"></a>Elemento ResultBinding (MSL)

L'elemento **risultante** in Mapping Specification Language (MSL) esegue il mapping dei valori di colonna restituiti dalle stored procedure alle proprietà dell'entità nel modello concettuale quando viene eseguito il mapping delle funzioni di modifica del tipo di entità alle stored procedure nel database sottostante. Quando, ad esempio, il valore di una colonna Identity viene restituito da un stored procedure di inserimento, l'elemento **risultante** esegue il mapping del valore restituito a una proprietà del tipo di entità nel modello concettuale.

L'elemento **risultante** può essere figlio dell'elemento InsertFunction o dell'elemento UpdateFunction.

L'elemento **risultante** non può contenere elementi figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **risultante** :

| Nome attributo | Obbligatorio | valore                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome della proprietà di entità nel modello concettuale di cui è in corso il mapping. |
| **ColumnName** | Sì         | Nome della colonna di cui viene eseguito il mapping.                                          |

### <a name="example"></a>Esempio

L'esempio seguente è basato sul modello School e Mostra un elemento **InsertFunction** usato per eseguire il mapping della funzione di inserimento del tipo di entità **Person** alla stored procedure **InsertPerson** . Il stored procedure **InsertPerson** è illustrato di seguito e viene dichiarato nel modello di archiviazione. Un elemento **risultante** viene utilizzato per eseguire il mapping di un valore di colonna restituito dal stored procedure (**NewPersonID**) a una proprietà del tipo di entità (**PersonID**).

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

L'istruzione Transact-SQL seguente descrive il stored procedure **InsertPerson** :

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

## <a name="resultmapping-element-msl"></a>Elemento ResultMapping (MSL)

L'elemento **ResultMapping** in Mapping Specification Language (MSL) definisce il mapping tra un'importazione di funzioni nel modello concettuale e una stored procedure nel database sottostante quando sono soddisfatte le condizioni seguenti:

-   L'operazione di importazione di funzioni restituisce un tipo di entità del modello concettuale.
-   I nomi delle colonne restituite dalla stored procedure non corrispondono esattamente ai nomi delle proprietà del tipo di entità o del tipo complesso.

Per impostazione predefinita, il mapping tra le colonne restituite da una stored procedure e un tipo di entità o un tipo complesso è basato sui nomi delle colonne e delle proprietà. Se i nomi delle colonne non corrispondono esattamente ai nomi delle proprietà, è necessario utilizzare l'elemento **ResultMapping** per definire il mapping. Per un esempio del mapping predefinito, vedere elemento FunctionImportMapping (MSL).

L'elemento **ResultMapping** è un elemento figlio dell'elemento FunctionImportMapping.

L'elemento **ResultMapping** può avere gli elementi figlio seguenti:

-   EntityTypeMapping (zero o più elementi)
-   ComplexTypeMapping

Nessun attributo applicabile all'elemento **ResultMapping** .

### <a name="example"></a>Esempio

Si consideri la stored procedure seguente:

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

Si consideri inoltre il tipo di entità del modello concettuale seguente:

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

Per creare un'importazione di funzioni che restituisce istanze del tipo di entità precedente, il mapping tra le colonne restituite dal stored procedure e il tipo di entità deve essere definito in un elemento **ResultMapping** :

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

## <a name="scalarproperty-element-msl"></a>Elemento ScalarProperty (MSL)

L'elemento **ScalarProperty** in Mapping Specification Language (MSL) esegue il mapping di una proprietà su un tipo di entità del modello concettuale, un tipo complesso o un'associazione a una colonna della tabella o a un parametro di stored procedure nel database sottostante.

> [!NOTE]
> Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione. Per ulteriori informazioni, vedere elemento Function (SSDL).

L'elemento **ScalarProperty** può essere figlio dei seguenti elementi:

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

Come figlio dell'elemento **MappingFragment**, **ComplexProperty**o **EndProperty** , l'elemento **ScalarProperty** esegue il mapping di una proprietà nel modello concettuale a una colonna nel database. Come figlio dell'elemento **InsertFunction**, **UpdateFunction**o **DeleteFunction** , l'elemento **ScalarProperty** esegue il mapping di una proprietà nel modello concettuale a un parametro stored procedure.

L'elemento **ScalarProperty** non può contenere elementi figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Gli attributi che si applicano all'elemento **ScalarProperty** variano a seconda del ruolo dell'elemento.

Nella tabella seguente vengono descritti gli attributi applicabili quando si utilizza l'elemento **ScalarProperty** per eseguire il mapping di una proprietà del modello concettuale a una colonna nel database:

| Nome attributo | Obbligatorio | valore                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Nome**       | Sì         | Nome della proprietà del modello concettuale di cui è in corso il mapping. |
| **ColumnName** | Sì         | Nome della colonna della tabella di cui è in corso il mapping.              |

Nella tabella seguente vengono descritti gli attributi applicabili all'elemento **ScalarProperty** quando viene utilizzato per eseguire il mapping di una proprietà del modello concettuale a un parametro stored procedure:

| Nome attributo    | Obbligatorio | valore                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**          | Sì         | Nome della proprietà del modello concettuale di cui è in corso il mapping.                                                                                 |
| **ParameterName** | Sì         | Nome del parametro di cui è in corso il mapping.                                                                                                 |
| **Version**       | No          | **Corrente** o **originale** a seconda che il valore corrente o il valore originale della proprietà debba essere usato per i controlli di concorrenza. |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **ScalarProperty** usato in due modi:

-   Per eseguire il mapping delle proprietà del tipo di entità **Person** alle colonne della tabella **Person**.
-   Per eseguire il mapping delle proprietà del tipo di entità **Person** ai parametri del stored procedure **UpdatePerson** . Le stored procedure vengono dichiarate nel modello di archiviazione.

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

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **ScalarProperty** utilizzato per eseguire il mapping delle funzioni Insert e Delete di un'associazione del modello concettuale alle stored procedure nel database. Le stored procedure vengono dichiarate nel modello di archiviazione.

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

## <a name="updatefunction-element-msl"></a>Elemento UpdateFunction (MSL)

L'elemento **UpdateFunction** in Mapping Specification Language (MSL) esegue il mapping della funzione di aggiornamento di un tipo di entità nel modello concettuale a una stored procedure nel database sottostante. Le stored procedure a cui le funzioni di modifica sono mappate devono essere dichiarate nel modello di archiviazione. Per ulteriori informazioni, vedere elemento Function (SSDL).

> [!NOTE]
>  Se non si esegue il mapping di tutte e tre le operazioni di inserimento, aggiornamento o eliminazione di un tipo di entità alle stored procedure, le operazioni non mappate avranno esito negativo se eseguite in fase di esecuzione e viene generata un'eccezione UpdateException.

L'elemento **UpdateFunction** può essere un elemento figlio dell'elemento ModificationFunctionMapping e applicato all'elemento EntityTypeMapping.

L'elemento **UpdateFunction** può avere gli elementi figlio seguenti:

-   AssociationEnd (zero o più elementi)
-   ComplexProperty (zero o più elementi)
-   ResultBinding (zero o un elemento)
-   ScarlarProperty (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **UpdateFunction** .

| Nome attributo            | Obbligatorio | valore                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | Sì         | Nome completo dello spazio dei nomi della stored procedure a cui la funzione di aggiornamento viene mappata. La stored procedure deve essere dichiarata nel modello di archiviazione. |
| **RowsAffectedParameter** | No          | Nome del parametro di output che restituisce il numero di righe interessate.                                                                               |

### <a name="example"></a>Esempio

L'esempio seguente è basato sul modello School e Mostra l'elemento **UpdateFunction** usato per eseguire il mapping della funzione di aggiornamento del tipo di entità **Person** alla stored procedure **UpdatePerson** . Il stored procedure **UpdatePerson** viene dichiarato nel modello di archiviazione.

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
