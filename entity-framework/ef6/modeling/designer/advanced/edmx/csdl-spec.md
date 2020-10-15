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
# <a name="csdl-specification"></a>Specifica CSDL
Conceptual Schema Definition Language (CSDL) è un linguaggio basato su XML che descrive le entità, le relazioni e le funzioni che costituiscono un modello concettuale di un'applicazione basata sui dati. Questo modello concettuale può essere utilizzato dal Entity Framework o WCF Data Services. I metadati descritti con CSDL vengono utilizzati dalla Entity Framework per eseguire il mapping di entità e relazioni definite in un modello concettuale a un'origine dati. Per ulteriori informazioni, vedere [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) e [specifica MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).

CSDL è l'implementazione Entity Framework dell'Entity Data Model.

In un'applicazione Entity Framework, i metadati del modello concettuale vengono caricati da un file con estensione CSDL (scritto in CSDL) in un'istanza di System. Data. Metadata. Edm. EdmItemCollection ed è possibile accedervi usando i metodi della classe System. Data. Metadata. Edm. MetadataWorkspace. Entity Framework usa i metadati del modello concettuale per tradurre le query sul modello concettuale in comandi specifici dell'origine dati.

La finestra di progettazione EF archivia le informazioni sul modello concettuale in un file con estensione edmx in fase di progettazione. In fase di compilazione, la finestra di progettazione EF utilizza le informazioni in un file con estensione edmx per creare il file con estensione csdl necessario per Entity Framework in fase di esecuzione.

Le versioni di CSDL si differenziano tra loro per gli spazi dei nomi XML.

| Versione CSDL | Spazio dei nomi XML                                |
|:-------------|:---------------------------------------------|
| CSDL V1      | https://schemas.microsoft.com/ado/2006/04/edm |
| CSDL V2      | https://schemas.microsoft.com/ado/2008/09/edm |
| CSDL V3      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Elemento Association (CSDL)

Un elemento **Association** definisce una relazione tra due tipi di entità. Un'associazione deve specificare i tipi di entità coinvolti nella relazione e il possibile numero di tipi di entità a ogni entità finale della relazione, che è noto come molteplicità. La molteplicità di un'entità finale di un'associazione può avere un valore pari a uno (1), zero o uno (0.. 1) o molti ( \* ). Queste informazioni vengono specificate in due elementi End figlio.

Le istanze del tipo di entità in corrispondenza di un'entità finale di un'associazione sono accessibili attraverso proprietà di navigazione o chiavi esterne se sono esposte in un tipo di entità.

In un'applicazione, un'istanza di un'associazione rappresenta un'associazione specifica tra istanze di tipi di entità. Le istanze dell'associazione sono raggruppate logicamente in un set di associazioni.

Un elemento **Association** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   End (esattamente 2 elementi)
-   ReferentialConstraint (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Association** .

| Nome attributo | Obbligatorio | valore                        |
|:---------------|:------------|:-----------------------------|
| **Nome**       | Sì         | Nome dell'associazione. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** quando le chiavi esterne non sono state esposte sui tipi di entità **Customer** e **Order** . I valori di **molteplicità** per ogni **estremità** dell'associazione indicano che molti **ordini** possono essere associati a un **cliente**, ma solo un **cliente** può essere associato a un **ordine**. Inoltre, l'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati se il **cliente** viene eliminato.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** quando le chiavi esterne sono state esposte sui tipi di entità **Customer** e **Order** . Con le chiavi esterne esposte, la relazione tra le entità viene gestita con un elemento **ReferentialConstraint** . Un elemento AssociationSetMapping corrispondente non è necessario per eseguire il mapping di questa associazione all'origine dati.

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
 

 

## <a name="associationset-element-csdl"></a>Elemento AssociationSet (CSDL)

L'elemento **associativo** in Conceptual Schema Definition Language (CSDL) è un contenitore logico per le istanze di associazione dello stesso tipo. Un set di associazioni fornisce una definizione per il raggruppamento di istanze di associazioni in modo che possano essere mappate a un'origine dati.  

L'elemento **associationname** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero elementi o un elemento consentito)
-   End (esattamente due elementi richiesti)
-   Elementi Annotation (zero o più elementi consentiti)

L'attributo **Association** specifica il tipo di associazione contenuto in un set di associazioni. I set di entità che costituiscono le entità finali di un set di associazioni vengono specificati con esattamente due elementi **end** figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **associazione** .

| Nome attributo  | Obbligatorio | valore                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**        | Sì         | Nome del set di entità. Il valore dell'attributo **Name** non può corrispondere al valore dell'attributo **Association** .                                 |
| **Associazione** | Sì         | Il nome completo dell'associazione le cui istanze sono contenute nel set di associazioni. L'associazione deve essere nello stesso spazio dei nomi del set di associazioni. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento di **associazione** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** con due elementi di **associazione** :

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
 

 

## <a name="collectiontype-element-csdl"></a>Elemento CollectionType (CSDL)

L'elemento **CollectionType** in Conceptual Schema Definition Language (CSDL) specifica che un parametro di funzione o un tipo restituito della funzione è una raccolta. L'elemento **CollectionType** può essere un elemento figlio dell'elemento Parameter o dell'elemento ReturnType (Function). Il tipo di raccolta può essere specificato tramite l'attributo **Type** o uno degli elementi figlio seguenti:

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> Un modello non convaliderà se il tipo di una raccolta viene specificato sia con l'attributo di **tipo** che con un elemento figlio.

 

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **CollectionType** . Si noti che gli attributi **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**e **Collation** sono applicabili solo alle raccolte di **EDMSimpleTypes**.

| Nome attributo                                                          | Obbligatorio | valore                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**                                                                | No          | Tipo della raccolta.                                                                                                                                                                                                      |
| **Ammette i valori Null**                                                            | No          | **True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null. <br/> [!NOTE]                                                                                                                 |
| > in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` . |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | No          | Valore predefinito della proprietà.                                                                                                                                                                                               |
| **MaxLength**                                                           | No          | Lunghezza massima del valore della proprietà.                                                                                                                                                                                        |
| **FixedLength**                                                         | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.                                                                                                                           |
| **Precisione**                                                           | No          | Precisione del valore della proprietà.                                                                                                                                                                                             |
| **Ridimensionamento**                                                               | No          | Scala del valore della proprietà.                                                                                                                                                                                                 |
| **SRID**                                                                | No          | Identificatore di riferimento del sistema spaziale. Valido solo per le proprietà dei tipi spaziali.Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) |
| **Unicode**                                                             | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.                                                                                                                                |
| **Regole di confronto**                                                           | No          | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.                                                                                                                                                    |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di tipi di entità **Person** (come specificato con l'attributo **elementType** ).

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
 

Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .

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
 

Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza l'elemento **CollectionType** per specificare che la funzione accetta come parametro una raccolta di tipi di entità **Department** .

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
 

 

## <a name="complextype-element-csdl"></a>Elemento ComplexType (CSDL)

Un elemento **complexType** definisce una struttura di dati composta da proprietà **EDMSimpleType** o altri tipi complessi.Un tipo complesso può essere una proprietà di un tipo di entità o un altro tipo complesso. Un tipo complesso è simile a un tipo di entità in quanto il tipo complesso definisce i dati. Tuttavia esistono alcune differenze importanti tra i tipi complessi e i tipi di entità:

-   I tipi complessi non dispongono di identità o chiavi e pertanto non possono esistere indipendentemente. I tipi complessi possono esistere solo come proprietà di tipi di entità o gli altri tipi complessi.
-   I tipi complessi non possono far parte di associazioni. Le entità finali di un'associazione non possono essere tipi complessi, di conseguenza non è possibile definire proprietà di navigazione per i tipi complessi.
-   Una proprietà di tipo complesso non può avere un valore null, sebbene ogni proprietà scalare di un tipo complesso possa essere impostata su Null.

Un elemento **complexType** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Property (zero o più elementi)
-   Elementi Annotation (zero o più elementi)

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **complexType** .

| Nome attributo                                                                                                 | Obbligatorio | valore                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome                                                                                                           | Sì         | Nome del tipo complesso. Il nome di un tipo complesso non può essere uguale a quello di un altro tipo complesso, di un tipo di entità o di un'associazione che si trova entro l'ambito del modello. |
| BaseType                                                                                                       | No          | Nome di un altro tipo complesso che è il tipo di base del tipo complesso definito. <br/> [!NOTE]                                                                     |
| > questo attributo non è applicabile in CSDL V1. L'ereditarietà per i tipi complessi non è supportata in quella versione. |             |                                                                                                                                                                                     |
| Contenuto                                                                                                       | No          | **True** o **false** (valore predefinito) a seconda che il tipo complesso sia un tipo astratto. <br/> [!NOTE]                                                                  |
| > questo attributo non è applicabile in CSDL V1. I tipi complessi in quella versione non possono essere tipi astratti.         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **complexType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un tipo complesso, **Address**, con le proprietà **EDMSimpleType** **StreetAddress**, **City**, **StateOrProvince**, **Country**e **PostalCode**.

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

Per definire l' **Indirizzo** del tipo complesso (sopra) come proprietà di un tipo di entità, è necessario dichiarare il tipo di proprietà nella definizione del tipo di entità. Nell'esempio seguente viene illustrata la proprietà **Address** come tipo complesso in un tipo di entità (**Publisher**):

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
 

 

## <a name="definingexpression-element-csdl"></a>Elemento DefiningExpression (CSDL)

L'elemento **DefiningExpression** in Conceptual Schema Definition Language (CSDL) contiene un'espressione Entity SQL che definisce una funzione nel modello concettuale.  

> [!NOTE]
> Ai fini della convalida, un elemento **DefiningExpression** può contenere contenuto arbitrario. Tuttavia, Entity Framework genererà un'eccezione in fase di esecuzione se un elemento **DefiningExpression** non contiene Entity SQL validi.

 

### <a name="applicable-attributes"></a>Attributi applicabili

Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **DefiningExpression** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene utilizzato un elemento **DefiningExpression** per definire una funzione che restituisce il numero di anni da quando un libro è stato pubblicato. Il contenuto dell'elemento **DefiningExpression** è scritto in Entity SQL.

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Elemento Dependent (CSDL)

L'elemento **dipendente** in Conceptual Schema Definition Language (CSDL) è un elemento figlio dell'elemento ReferentialConstraint e definisce l'entità finale dipendente di un vincolo referenziale. Un elemento **ReferentialConstraint** definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale. Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità. Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo. Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo. Gli elementi **PropertyRef** vengono utilizzati per specificare quali chiavi fanno riferimento all'entità finale principale.

L'elemento **dipendente** può includere i seguenti elementi figlio (nell'ordine elencato):

-   PropertyRef (uno o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **dipendente** .

| Nome attributo | Obbligatorio | valore                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Ruolo**       | Sì         | Nome del tipo di entità in un'entità finale dipendente dell'associazione. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **dipendente** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** usato come parte della definizione dell'associazione **PublishedBy** . La proprietà **publisherID** del tipo di entità **book** costituisce l'entità finale dipendente del vincolo referenziale.

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
 

 

## <a name="documentation-element-csdl"></a>Elemento Documentation (CSDL)

L'elemento **Documentation** in Conceptual Schema Definition Language (CSDL) può essere utilizzato per fornire informazioni su un oggetto definito in un elemento padre. In un file con estensione edmx, quando l'elemento **Documentation** è un elemento figlio di un elemento che viene visualizzato come oggetto nell'area di progettazione di Entity Framework Designer, ad esempio un'entità, un'associazione o una proprietà, il contenuto dell'elemento **Documentation** verrà visualizzato nella finestra **proprietà** di Visual Studio per l'oggetto.

L'elemento **Documentation** può includere i seguenti elementi figlio (nell'ordine elencato):

-   **Riepilogo**: breve descrizione dell'elemento padre. Zero o un elemento.
-   **LongDescription**: Descrizione completa dell'elemento padre. Zero o un elemento.
-   Elementi Annotation. Zero o più elementi.

### <a name="applicable-attributes"></a>Attributi applicabili

All'elemento **Documentation** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **Documentation** come elemento figlio di un elemento EntityType. Se il frammento di codice seguente si trova nel contenuto CSDL di un file con estensione edmx, il contenuto degli elementi **Summary** e **LongDescription** verrà visualizzato nella finestra **proprietà** di Visual Studio quando si fa clic sul `Customer` tipo di entità.

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
 

 

## <a name="end-element-csdl"></a>Elemento End (CSDL)

L'elemento **end** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento Association o dell'elemento associationname. In ogni caso, il ruolo dell'elemento **end** è diverso e gli attributi applicabili sono diversi.

### <a name="end-element-as-a-child-of-the-association-element"></a>Elemento End come figlio dell'elemento Association

Un elemento **end** (come figlio dell'elemento **Association** ) identifica il tipo di entità in un'entità finale di un'associazione e il numero di istanze del tipo di entità che possono esistere a tale estremità di un'associazione. Le entità finali dell'associazione sono definite come parte di un'associazione; un'associazione deve disporre esattamente di due entità finali. Le istanze del tipo di entità in corrispondenza di un'entità finale di un'associazione sono accessibili attraverso proprietà di navigazione o chiavi esterne se sono esposte in un tipo di entità.  

Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   OnDelete (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **finale** quando è figlio di un elemento **Association** .

| Nome attributo   | Obbligatorio | valore                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**         | Sì         | Nome del tipo di entità in una entità finale dell'associazione.                                                                                                                                                                                                                                                                                                                                                         |
| **Ruolo**         | No          | Nome per l'entità finale dell'associazione. Se non è fornito alcun nome, verrà utilizzato il nome del tipo di entità nell'entità finale dell'associazione.                                                                                                                                                                                                                                                                                           |
| **Molteplicità** | Sì         | **1**, **0.. 1**o a **\*** seconda del numero di istanze del tipo di entità che possono trovarsi alla fine dell'associazione. <br/> **1** indica che nell'entità finale dell'associazione esiste esattamente un'istanza del tipo di entità. <br/> **0.. 1** indica che nell'entità finale dell'associazione sono presenti zero o un'istanza del tipo di entità. <br/> **\*** indica che nell'entità finale dell'associazione sono presenti zero, una o più istanze del tipo di entità. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** . I valori di **molteplicità** per ogni **estremità** dell'associazione indicano che molti **ordini** possono essere associati a un **cliente**, ma solo un **cliente** può essere associato a un **ordine**. Inoltre, l'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati se il **cliente** viene eliminato.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Elemento End come figlio dell'elemento AssociationSet

L'elemento **end** specifica un'estremità di un set di associazioni. L'elemento **associationname** deve contenere due elementi **end** . Le informazioni contenute in un elemento **end** vengono utilizzate per eseguire il mapping di un set di associazioni a un'origine dati.

Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

> [!NOTE]
> Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio. Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.

 

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **finale** quando è figlio di un elemento di **associazione** .

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | Sì         | Nome dell'elemento **EntitySet** che definisce un'entità finale dell'elemento di **associazione** padre. L'elemento **EntitySet** deve essere definito nello stesso contenitore di entità dell'elemento di **associazione** padre. |
| **Ruolo**       | No          | Nome dell'entità finale del set di associazioni. Se non si utilizza l'attributo **Role** , il nome dell'entità finale del set di associazioni sarà il nome del set di entità.                                                                   |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** con due elementi di **associazione** , ognuno con due elementi **end** :

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
 

 

## <a name="entitycontainer-element-csdl"></a>Elemento EntityContainer (CSDL)

L'elemento **EntityContainer** in Conceptual Schema Definition Language (CSDL) è un contenitore logico per set di entità, set di associazioni e importazioni di funzioni. Un contenitore di entità del modello concettuale esegue il mapping a un contenitore di entità del modello di archiviazione attraverso l'elemento EntityContainerMapping. Un contenitore di entità del modello di archiviazione descrive la struttura del database: i set di entità descrivono le tabelle, i set di associazioni descrivono i vincoli delle chiavi esterne e le importazioni di funzioni descrivono le stored procedure in un database.

Un elemento **EntityContainer** può avere uno o più elementi di documentazione. Se è presente un elemento di **documentazione** , deve precedere tutti gli elementi **EntitySet**, **associationname**e **FunctionImport** .

Un elemento **EntityContainer** può avere zero o più degli elementi figlio seguenti (nell'ordine elencato):

-   EntitySet
-   AssociationSet
-   FunctionImport
-   Elementi Annotation

È possibile estendere un elemento **EntityContainer** per includere il contenuto di un altro oggetto **EntityContainer** che si trova all'interno dello stesso spazio dei nomi. Per includere il contenuto di un altro **EntityContainer**, nell'elemento **EntityContainer** di riferimento, impostare il valore dell'attributo **extends** sul nome dell'elemento **EntityContainer** che si desidera includere. Tutti gli elementi figlio dell'elemento **EntityContainer** incluso verranno considerati come elementi figlio dell'elemento **EntityContainer** di riferimento.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **using** .

| Nome attributo | Obbligatorio | valore                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Nome**       | Sì         | Nome del contenitore di entità.                               |
| **Estende**    | No          | Nome di un altro contenitore di entità all'interno dello stesso spazio dei nomi. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityContainer** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** che definisce tre set di entità e due set di associazioni.

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
 

 

## <a name="entityset-element-csdl"></a>Elemento EntitySet (CSDL)

L'elemento **EntitySet** in Conceptual Schema Definition Language è un contenitore logico per le istanze di un tipo di entità e le istanze di qualsiasi tipo derivato da tale tipo di entità. La relazione tra un tipo di entità e un set di entità è analoga alla relazione tra una riga e una tabella in un database relazionale. Analogamente a una riga, un tipo di entità definisce un set di dati correlati e, analogamente a una tabella, un set di entità contiene istanze di quella definizione. Un set di entità fornisce un construct per il raggruppamento di istanze del tipo di entità in modo che se ne possa eseguire il mapping alle strutture dei dati correlati in un'origine dati.  

È possibile definire più set di entità per un particolare tipo di entità.

> [!NOTE]
> EF designer non supporta i modelli concettuali che contengono Multiple Entity Sets per Type.

 

L'elemento **EntitySet** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Elemento Documentation (zero elementi o un elemento consentito)
-   Elementi Annotation (zero o più elementi consentiti)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySet** .

| Nome attributo | Obbligatorio | valore                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del set di entità.                                                              |
| **EntityType** | Sì         | Nome completo del tipo di entità per il quale il set di entità contiene delle istanze. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntitySet** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** con tre elementi **EntitySet** :

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
 

È possibile definire più set di entità per tipo (MEST). Nell'esempio seguente viene definito un contenitore di entità con due set di entità per il tipo di entità **book** :

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
 

 

## <a name="entitytype-element-csdl"></a>Elemento EntityType (CSDL)

L'elemento **EntityType** rappresenta la struttura di un concetto di primo livello, ad esempio un cliente o un ordine, in un modello concettuale. Un tipo di entità è un modello per istanze di tipi di entità in un'applicazione. Ogni modello contiene le informazioni seguenti:

-   Un nome univoco. Obbligatorio.
-   Una chiave di entità che è definita da una o più proprietà. Obbligatorio.
-   Proprietà per contenere dati. Facoltativo.
-   Proprietà di navigazione che consentono di navigare da un'entità finale di un'associazione all'altra. Facoltativo.

In un'applicazione, un'istanza di un tipo di entità rappresenta un oggetto specifico, quale ad esempio un cliente o un ordine specifico. Ogni istanza di un tipo di entità deve avere una chiave di entità univoca all'interno di un set di entità.

Due istanze di tipi di entità sono considerate uguali solo se sono dello stesso tipo e se i valori delle rispettive chiavi di entità sono uguali.

Un elemento **EntityType** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Key (zero o un elemento)
-   Property (zero o più elementi)
-   NavigationProperty (zero o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityType** .

| Nome attributo                                                                                                                                  | Obbligatorio | valore                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Nome**                                                                                                                                        | Sì         | Nome del tipo di entità.                                                                     |
| **BaseType**                                                                                                                                    | No          | Nome di un altro tipo di entità, che rappresenta il tipo di base del tipo di entità in corso di definizione.  |
| **Astratta**                                                                                                                                    | No          | **True** o **false**, a seconda che il tipo di entità sia un tipo astratto.                 |
| **OpenType**                                                                                                                                    | No          | **True** o **false** a seconda che il tipo di entità sia un tipo di entità aperto. <br/> [!NOTE] |
| > l'attributo **OpenType** è applicabile solo ai tipi di entità definiti nei modelli concettuali utilizzati con ADO.NET Data Services. |             |                                                                                                  |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con tre elementi **Property** e due elementi **NavigationProperty** :

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
 

 

## <a name="enumtype-element-csdl"></a>Elemento EnumType (CSDL)

L'elemento **enumType** rappresenta un tipo enumerato.

Un elemento **enumType** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Member (zero o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **enumType** .

| Nome attributo     | Obbligatorio | valore                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**           | Sì         | Nome del tipo di entità.                                                                                                                                                                  |
| **IsFlags**        | No          | **True** o **false**, a seconda che il tipo enum possa essere utilizzato come set di flag. Il valore predefinito è **false.**                                                                     |
| **UnderlyingType** | No          | **EDM. byte**, **EDM. Int16**, **EDM. Int32**, **EDM. Int64** o **EDM. SByte** che definisce l'intervallo di valori del tipo. Il tipo sottostante predefinito degli elementi di enumerazione è **EDM. Int32.**. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **enumType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **enumType** con tre elementi **member** :

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Elemento Function (CSDL)

L'elemento **Function** in Conceptual Schema Definition Language (CSDL) viene utilizzato per definire o dichiarare funzioni nel modello concettuale. Un funzione viene definita utilizzando un elemento DefiningExpression.  

Un elemento **Function** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Parameter (zero o più elementi)
-   DefiningExpression (zero o un elemento)
-   ReturnType (funzione) (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** (Function) o l'attributo **returnType** (vedere di seguito), ma non entrambi. I tipi restituiti possibili sono il edmSimpleType, il tipo di entità, il tipo complesso, il tipo di riga o il tipo di riferimento o una raccolta di uno di questi tipi.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Function** .

| Nome attributo | Obbligatorio | valore                              |
|:---------------|:------------|:-----------------------------------|
| **Nome**       | Sì         | Nome della funzione.          |
| **ReturnType** | No          | Tipo restituito dalla funzione. |

 

> [!NOTE]
> All'elemento **Function** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene utilizzato un elemento **Function** per definire una funzione che restituisce il numero di anni dall'assunzione di un insegnante.

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>Elemento FunctionImport (CSDL)

L'elemento **FunctionImport** in Conceptual Schema Definition Language (CSDL) rappresenta una funzione definita nell'origine dati, ma disponibile per gli oggetti tramite il modello concettuale. Ad esempio, è possibile utilizzare un elemento Function nel modello di archiviazione per rappresentare una stored procedure in un database. Un elemento **FunctionImport** nel modello concettuale rappresenta la funzione corrispondente in un'applicazione Entity Framework e viene mappato alla funzione del modello di archiviazione utilizzando l'elemento FunctionImportMapping. Quando la funzione viene chiamata nell'applicazione, la stored procedure corrispondente viene eseguita nel database.

L'elemento **FunctionImport** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero elementi o un elemento consentito)
-   Parameter (zero o più elementi consentiti)
-   Elementi Annotation (zero o più elementi consentiti)
-   ReturnType (FunctionImport) (zero o più elementi consentiti)

È necessario definire un elemento **Parameter** per ogni parametro accettato dalla funzione.

Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** (FunctionImport) o l'attributo **returnType** (vedere di seguito), ma non entrambi. Il valore del tipo restituito deve essere una raccolta di EdmSimpleType, EntityType o ComplexType.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **FunctionImport** .

| Nome attributo   | Obbligatorio | valore                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**         | Sì         | Nome della funzione importata.                                                                                                                                                                    |
| **ReturnType**   | No          | Tipo restituito dalla funzione. Non utilizzare questo attributo se la funzione non restituisce un valore. In caso contrario, il valore deve essere una raccolta di ComplexType, EntityType o EDMSimpleType.        |
| **EntitySet**    | No          | Se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve essere il set di entità a cui appartiene la raccolta. In caso contrario, l'attributo **EntitySet** non deve essere utilizzato. |
| **IsComposable** | No          | Se il valore è impostato su true, la funzione è componibile (funzione con valori di tabella) e può essere utilizzata in una query LINQ.Il valore predefinito è **false**.                                                           |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **FunctionImport** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **FunctionImport** che accetta un parametro e restituisce una raccolta di tipi di entità:

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>Elemento Key (CSDL)

L'elemento **Key** è un elemento figlio dell'elemento EntityType e definisce una *chiave di entità* , ovvero una proprietà o un set di proprietà di un tipo di entità che determinano l'identità. Le proprietà che costituiscono una chiave di entità vengono scelte in fase di progettazione. I valori delle proprietà della chiave di entità devono identificare in modo univoco in fase di esecuzione un'istanza del tipo di entità all'interno di un set di entità. Le proprietà che costituiscono una chiave di entità devono essere scelte per garantire univocità delle istanze in un set di entità. L'elemento **Key** definisce una chiave di entità facendo riferimento a una o più proprietà di un tipo di entità.

L'elemento **chiave** può avere gli elementi figlio seguenti:

-   PropertyRef (uno o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **chiave** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene definito un tipo di entità denominato **book**. La chiave di entità viene definita facendo riferimento alla proprietà **ISBN** del tipo di entità.

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
 

La proprietà **ISBN** è una scelta ottimale per la chiave di entità perché un numero di libro standard internazionale (ISBN) identifica in modo univoco un libro.

Nell'esempio seguente viene illustrato un tipo di entità (**autore**) che dispone di una chiave di entità costituita da due proprietà, **Name** e **Address**.

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
 

L'uso del **nome** e dell' **Indirizzo** per la chiave di entità è una scelta ragionevole, perché due autori con lo stesso nome non possono vivere allo stesso indirizzo. Questa scelta per una chiave di entità non garantisce tuttavia in modo assoluto chiavi di entità univoche in un set di entità. In questo caso, è consigliabile aggiungere una proprietà, ad esempio **autorizzazione**, che può essere usata per identificare in modo univoco un autore.

 

## <a name="member-element-csdl"></a>Elemento member (CSDL)

L'elemento **member** è un elemento figlio dell'elemento enumType e definisce un membro del tipo enumerato.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **FunctionImport** .

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del membro.                                                                                                                                                                  |
| **Valore**      | No          | Valore del membro. Per impostazione predefinita, il primo membro ha il valore 0 e il valore di ogni enumeratore successivo viene incrementato di 1. Potrebbero esistere più membri con gli stessi valori. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **FunctionImport** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **enumType** con tre elementi **member** :

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>Elemento NavigationProperty (CSDL)

Un elemento **NavigationProperty** definisce una proprietà di navigazione che fornisce un riferimento all'altra entità finale di un'associazione. A differenza delle proprietà definite con l'elemento Property, le proprietà di navigazione non definiscono la forma e le caratteristiche dei dati. Forniscono un modo per navigare in un'associazione tra due tipi di entità.

Si noti che le proprietà di navigazione sono facoltative in entrambi tipi di entità nelle entità finali di un'associazione. Se si definisce una proprietà di navigazione su un tipo di entità nell'entità finale di un'associazione, non è necessario definire una proprietà di navigazione sul tipo di entità nell'altra entità finale dell'associazione.

Il tipo di dati restituito da una proprietà di navigazione è determinato dalla molteplicità della relativa entità finale di associazione remota. Si supponga, ad esempio, che una proprietà di navigazione, **OrdersNavProp**, esista in un tipo di entità **Customer** e si sposta in un'associazione uno-a-molti tra **Customer** e **Order**. Poiché l'entità finale dell'associazione remota per la proprietà di navigazione dispone di molteplicità many ( \* ), il relativo tipo di dati è una raccolta (di **Order**). Analogamente, se esiste una proprietà di navigazione, **CustomerNavProp nel**, nel tipo di entità **Order** , il relativo tipo di dati sarà **Customer** poiché la molteplicità di end remoto è uno (1).

Un elemento **NavigationProperty** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **NavigationProperty** .

| Nome attributo   | Obbligatorio | valore                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**         | Sì         | Nome della proprietà di navigazione.                                                                                                                                                                                                             |
| **Relationship** | Sì         | Nome di un'associazione che è all'interno dell'ambito del modello.                                                                                                                                                                                |
| **ToRole**       | Sì         | Entità finale dell'associazione in corrispondenza della quale termina la navigazione. Il valore dell'attributo **ToRole** deve corrispondere al valore di uno degli attributi **Role** definiti in una delle entità finali dell'associazione (definite nell'elemento AssociationEnd).       |
| **FromRole**     | Sì         | Entità finale dell'associazione dalla quale ha inizio la navigazione. Il valore dell'attributo **FromRole** deve corrispondere al valore di uno degli attributi **Role** definiti in una delle entità finali dell'associazione (definite nell'elemento AssociationEnd). |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **NavigationProperty** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene definito un tipo di entità (**book**) con due proprietà di navigazione (**PublishedBy** e **WrittenBy**):

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
 

 

## <a name="ondelete-element-csdl"></a>Elemento OnDelete (CSDL)

L'elemento **OnDelete** in Conceptual Schema Definition Language (CSDL) definisce il comportamento connesso a un'associazione. Se l'attributo **Action** è impostato su **Cascade** in un'entità finale di un'associazione, i tipi di entità correlati sull'altra entità finale dell'associazione vengono eliminati quando viene eliminato il tipo di entità alla prima estremità. Se l'associazione tra due tipi di entità è una relazione tra chiave primaria e chiave primaria, un oggetto dipendente caricato viene eliminato quando l'oggetto Principal sull'altra entità finale dell'associazione viene eliminato indipendentemente dalla specifica **OnDelete** .  

> [!NOTE]
> L'elemento **OnDelete** influisca solo sul comportamento di runtime di un'applicazione. non influisce sul comportamento nell'origine dati. Il comportamento definito nell'origine dati deve essere uguale al comportamento definito nell'applicazione.

 

Un elemento **OnDelete** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **OnDelete** .

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Azione**     | Sì         | **Cascade** o **None**. Se **Cascade**, i tipi di entità dipendenti verranno eliminati quando il tipo di entità principale viene eliminato. Se **None**, i tipi di entità dipendenti non verranno eliminati quando il tipo di entità principale viene eliminato. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che definisce l'associazione **CustomerOrders** . L'elemento **OnDelete** indica che tutti **gli ordini** relativi a un determinato **cliente** e che sono stati caricati in ObjectContext verranno eliminati quando il **cliente** viene eliminato.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Elemento Parameter (CSDL)

L'elemento **Parameter** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento FunctionImport o dell'elemento Function.

### <a name="functionimport-element-application"></a>Applicazione dell'elemento FunctionImport

Un elemento **Parameter** (come figlio dell'elemento **FunctionImport** ) viene usato per definire i parametri di input e output per le importazioni di funzioni dichiarate in CSDL.

L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero elementi o un elemento consentito)
-   Elementi Annotation (zero o più elementi consentiti)

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del parametro.                                                                                                                                                                                                      |
| **Tipo**       | Sì         | Tipo di parametro. Il valore deve essere di tipo **EDMSimpleType** o di un tipo complesso compreso nell'ambito del modello.                                                                                                             |
| **Modalità**       | No          | **In**, **out**o **InOut** a seconda che il parametro sia un parametro di input, di output o di input/output.                                                                                                                |
| **MaxLength**  | No          | Lunghezza massima consentita per il parametro.                                                                                                                                                                                    |
| **Precisione**  | No          | Precisione del parametro                                                                                                                                                                                                 |
| **Ridimensionamento**      | No          | Scalabilità del parametro                                                                                                                                                                                                     |
| **SRID**       | No          | Identificatore di riferimento del sistema spaziale. Valido solo per i parametri dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

 

> [!NOTE]
> All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **FunctionImport** con un elemento figlio **Parameter** . La funzione accetta un parametro di input e restituisce una raccolta di tipi di entità.

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Applicazione dell'elemento Function

Un elemento **Parameter** (come figlio dell'elemento **Function** ) definisce i parametri per le funzioni definite o dichiarate in un modello concettuale.

L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   CollectionType (zero o un elemento)
-   ReferenceType (zero o un elemento)
-   RowType (zero o un elemento)

> [!NOTE]
> Solo uno degli elementi **CollectionType**, **ReferenceType**o **RowType** può essere un elemento figlio di un elemento **Property** .

 

-   Elementi Annotation (zero o più elementi consentiti)

> [!NOTE]
> Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio. Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.

 

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .

| Nome attributo   | Obbligatorio | valore                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**         | Sì         | Nome del parametro.                                                                                                                                                                                                      |
| **Tipo**         | No          | Tipo di parametro. Un parametro può essere uno qualsiasi dei seguenti tipi (o raccolte di questi tipi): <br/> **EdmSimpleType** <br/> tipo di entità <br/> tipo complesso <br/> tipo di riga <br/> tipo di riferimento                             |
| **Ammette i valori Null**     | No          | **True** (valore predefinito) o **false** a seconda che la proprietà possa avere un valore **null** .                                                                                                                          |
| **DefaultValue** | No          | Valore predefinito della proprietà.                                                                                                                                                                                              |
| **MaxLength**    | No          | Lunghezza massima del valore della proprietà.                                                                                                                                                                                       |
| **FixedLength**  | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.                                                                                                                          |
| **Precisione**    | No          | Precisione del valore della proprietà.                                                                                                                                                                                            |
| **Ridimensionamento**        | No          | Scala del valore della proprietà.                                                                                                                                                                                                |
| **SRID**         | No          | Identificatore di riferimento del sistema spaziale. Valido solo per le proprietà dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**      | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.                                                                                                                               |
| **Regole di confronto**    | No          | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.                                                                                                                                                   |

 

> [!NOTE]
> All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Function** che utilizza un elemento figlio **Parameter** per definire un parametro di funzione.

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a>Elemento Principal (CSDL)

L'elemento **Principal** in Conceptual Schema Definition Language (CSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale principale di un vincolo referenziale. Un elemento **ReferentialConstraint** definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale. Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità. Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo. Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo. Gli elementi **PropertyRef** vengono utilizzati per specificare a quali chiavi viene fatto riferimento dall'entità finale dipendente.

L'elemento **Principal** può includere i seguenti elementi figlio (nell'ordine elencato):

-   PropertyRef (uno o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Principal** .

| Nome attributo | Obbligatorio | valore                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Ruolo**       | Sì         | Nome del tipo di entità in un'entità finale principale dell'associazione. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **principale** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** che fa parte della definizione dell'associazione **PublishedBy** . La proprietà **ID** del tipo di entità **Publisher** costituisce l'entità finale principale del vincolo referenziale.

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
 

 

## <a name="property-element-csdl"></a>Elemento Property (CSDL)

L'elemento **Property** in Conceptual Schema Definition Language (CSDL) può essere un elemento figlio dell'elemento EntityType, dell'elemento complexType o dell'elemento RowType.

### <a name="entitytype-and-complextype-element-applications"></a>Applicazione degli elementi EntityType e ComplexType

Gli elementi **Property** (come elementi figlio degli elementi **EntityType** o **complexType** ) definiscono la forma e le caratteristiche dei dati che un'istanza del tipo di entità o di tipo complesso conterrà. Le proprietà in un modello concettuale sono analoghe alle proprietà definite su una classe. Nello stesso modo in cui le proprietà su una classe definiscono la forma della classe e forniscono informazioni su oggetti, le proprietà in un modello concettuale definiscono la forma di un tipo di entità e forniscono informazioni su istanze del tipo di entità.

L'elemento **Property** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Elemento Documentation (zero elementi o un elemento consentito)
-   Elementi Annotation (zero o più elementi consentiti)

A un elemento **Property** è possibile applicare i facet seguenti: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**, **Collation**, **ConcurrencyMode**. I facet sono attributi XML che forniscono informazioni sul modo in cui i valori di proprietà vengono archiviati nell'archivio dati.

> [!NOTE]
> I facet possono essere applicati solo a proprietà di tipo **EDMSimpleType**.

 

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .

| Nome attributo                                                         | Obbligatorio | valore                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                                                               | Sì         | Nome della proprietà.                                                                                                                                                                                                       |
| **Tipo**                                                               | Sì         | Tipo del valore della proprietà. Il tipo del valore della proprietà deve corrispondere a **EDMSimpleType** o a un tipo complesso (indicato da un nome completo) nell'ambito del modello.                                                 |
| **Ammette i valori Null**                                                           | No          | **True** (valore predefinito) o <strong>False</strong>, a seconda che la proprietà possa avere valore null. <br/> [!NOTE]                                                                                                   |
| > in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | No          | Valore predefinito della proprietà.                                                                                                                                                                                              |
| **MaxLength**                                                          | No          | Lunghezza massima del valore della proprietà.                                                                                                                                                                                       |
| **FixedLength**                                                        | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.                                                                                                                          |
| **Precisione**                                                          | No          | Precisione del valore della proprietà.                                                                                                                                                                                            |
| **Ridimensionamento**                                                              | No          | Scala del valore della proprietà.                                                                                                                                                                                                |
| **SRID**                                                               | No          | Identificatore di riferimento del sistema spaziale. Valido solo per le proprietà dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                            | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.                                                                                                                               |
| **Regole di confronto**                                                          | No          | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.                                                                                                                                                   |
| **ConcurrencyMode**                                                    | No          | **None** (valore predefinito) o **Fixed**. Se il valore è impostato su **Fixed**, il valore della proprietà verrà utilizzato nei controlli della concorrenza ottimistica.                                                                                  |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con tre elementi **Property** :

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
 

Nell'esempio seguente viene illustrato un elemento **complexType** con cinque elementi **Property** :

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>Applicazione dell'elemento RowType

Gli elementi **Property** (come gli elementi figlio di un elemento **RowType** ) definiscono la forma e le caratteristiche dei dati che possono essere passati o restituiti da una funzione definita dal modello.  

L'elemento **Property** può avere esattamente uno degli elementi figlio seguenti:

-   CollectionType
-   ReferenceType
-   RowType

L'elemento **Property** può includere qualsiasi numero di elementi Annotation figlio.

> [!NOTE]
> Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.

 

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .

| Nome attributo                                                     | Obbligatorio | valore                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                                                           | Sì         | Nome della proprietà.                                                                                                                                                                                                       |
| **Tipo**                                                           | Sì         | Tipo del valore della proprietà.                                                                                                                                                                                                 |
| **Ammette i valori Null**                                                       | No          | **True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null. <br/> [!NOTE]                                                                                                                |
| > in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | No          | Valore predefinito della proprietà.                                                                                                                                                                                              |
| **MaxLength**                                                      | No          | Lunghezza massima del valore della proprietà.                                                                                                                                                                                       |
| **FixedLength**                                                    | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.                                                                                                                          |
| **Precisione**                                                      | No          | Precisione del valore della proprietà.                                                                                                                                                                                            |
| **Ridimensionamento**                                                          | No          | Scala del valore della proprietà.                                                                                                                                                                                                |
| **SRID**                                                           | No          | Identificatore di riferimento del sistema spaziale. Valido solo per le proprietà dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.                                                                                                                               |
| **Regole di confronto**                                                      | No          | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.                                                                                                                                                   |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

#### <a name="example"></a>Esempio

Nell'esempio seguente vengono illustrati gli elementi di **Proprietà** utilizzati per definire la forma del tipo restituito di una funzione definita dal modello.

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
 

 

## <a name="propertyref-element-csdl"></a>Elemento PropertyRef (CSDL)

L'elemento **PropertyRef** in Conceptual Schema Definition Language (CSDL) fa riferimento a una proprietà di un tipo di entità per indicare che la proprietà eseguirà uno dei ruoli seguenti:

-   Parte della chiave di entità (una proprietà o un set di proprietà di un tipo di entità che determinano l'identità). Per definire una chiave di entità, è possibile usare uno o più elementi **PropertyRef** .
-   L'entità finale dipendente o principale di un vincolo referenziale.

L'elemento **PropertyRef** può contenere solo elementi Annotation (zero o più) come elementi figlio.

> [!NOTE]
> Gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.

 

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **PropertyRef** .

| Nome attributo | Obbligatorio | valore                                |
|:---------------|:------------|:-------------------------------------|
| **Nome**       | Sì         | Nome della proprietà alla quale viene fatto riferimento. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **PropertyRef** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene definito un tipo di entità (**book**). La chiave di entità viene definita facendo riferimento alla proprietà **ISBN** del tipo di entità.

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
 

Nell'esempio seguente vengono usati due elementi **PropertyRef** per indicare che due proprietà (**ID** e **publisherID**) sono le estremità principale e dipendente di un vincolo referenziale.

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
 

 

## <a name="referencetype-element-csdl"></a>Elemento ReferenceType (CSDL)

L'elemento **ReferenceType** in Conceptual Schema Definition Language (CSDL) specifica un riferimento a un tipo di entità. L'elemento **ReferenceType** può essere figlio dei seguenti elementi:

-   ReturnType (funzione)
-   Parametro
-   CollectionType

L'elemento **ReferenceType** viene usato quando si definisce un parametro o un tipo restituito per una funzione.

Un elemento **ReferenceType** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **ReferenceType** .

| Nome attributo | Obbligatorio | valore                                         |
|:---------------|:------------|:----------------------------------------------|
| **Tipo**       | Sì         | Nome del tipo di entità a cui viene fatto riferimento. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **ReferenceType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **ReferenceType** usato come figlio di un elemento **Parameter** in una funzione definita dal modello che accetta un riferimento a un tipo di entità **Person** :

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
 

Nell'esempio seguente viene illustrato l'elemento **ReferenceType** utilizzato come elemento figlio di un elemento **returnType** (Function) in una funzione definita dal modello che restituisce un riferimento a un tipo di entità **Person** :

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
 

 

## <a name="referentialconstraint-element-csdl"></a>Elemento ReferentialConstraint (CSDL)

Un elemento **ReferentialConstraint** in Conceptual Schema Definition Language (CSDL) definisce la funzionalità che è simile a un vincolo di integrità referenziale in un database relazionale. Nello stesso modo in cui una colonna (o più colonne) da una tabella di database può fare riferimento alla chiave primaria di un'altra tabella, una proprietà (o più proprietà) di un tipo di entità può fare riferimento alla chiave di entità di un altro tipo di entità. Il tipo di entità a cui si fa riferimento viene chiamato *entità finale principale* del vincolo. Il tipo di entità che fa riferimento all'entità finale principale viene chiamato entità *finale dipendente* del vincolo.

Se una chiave esterna esposta in un tipo di entità fa riferimento a una proprietà in un altro tipo di entità, l'elemento **ReferentialConstraint** definisce un'associazione tra i due tipi di entità. Poiché l'elemento **ReferentialConstraint** fornisce informazioni sul modo in cui due tipi di entità sono correlati, non è necessario alcun elemento AssociationSetMapping corrispondente nella Mapping Specification Language (MSL). Un'associazione tra due tipi di entità che non dispongono di chiavi esterne esposte deve avere un elemento **AssociationSetMapping** corrispondente per eseguire il mapping delle informazioni di associazione all'origine dati.

Se una chiave esterna non è esposta in un tipo di entità, l'elemento **ReferentialConstraint** può definire solo un vincolo PRIMARY KEY-Primary Key tra il tipo di entità e un altro tipo di entità.

Un elemento **ReferentialConstraint** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Principal (esattamente un elemento)
-   Dependent (esattamente un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

L'elemento **ReferentialConstraint** può avere un numero qualsiasi di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **ReferentialConstraint** usato come parte della definizione dell'associazione **PublishedBy** .

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
 

 

## <a name="returntype-function-element-csdl"></a>Elemento ReturnType (Function) (CSDL)

L'elemento **returnType** (Function) in Conceptual Schema Definition Language (CSDL) specifica il tipo restituito per una funzione definita in un elemento Function. Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .

I tipi restituiti possono essere qualsiasi **EDMSimpleType**, tipo di entità, tipo complesso, tipo di riga, tipo di riferimento o una raccolta di uno di questi tipi.

Il tipo restituito di una funzione può essere specificato con l'attributo **Type** dell'elemento **returnType** (Function) o con uno degli elementi figlio seguenti:

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> Un modello non viene convalidato se si specifica un tipo restituito dalla funzione con l'attributo **Type** dell'elemento **returnType** (Function) e uno degli elementi figlio.

 

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **returnType** (Function).

| Nome attributo | Obbligatorio | valore                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | No          | Tipo restituito dalla funzione. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** (Function). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene usato un elemento **Function** per definire una funzione che restituisce il numero di anni in cui un libro è stato stampato. Si noti che il tipo restituito viene specificato dall'attributo **Type** di un elemento **returnType** (Function).

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>Elemento ReturnType (FunctionImport) (CSDL)

L'elemento **returnType** (FunctionImport) in Conceptual Schema Definition Language (CSDL) specifica il tipo restituito per una funzione definita in un elemento FunctionImport. Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .

I tipi restituiti possono essere qualsiasi raccolta di tipi di entità, tipi complessi o **EDMSimpleType**,

Il tipo restituito di una funzione viene specificato con l'attributo **Type** dell'elemento **returnType** (FunctionImport).

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **returnType** (FunctionImport).

| Nome attributo | Obbligatorio | valore                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**       | No          | Tipo restituito dalla funzione. Il valore deve essere una raccolta di ComplexType, EntityType o EDMSimpleType.                                                                                      |
| **EntitySet**  | No          | Se la funzione restituisce una raccolta di tipi di entità, il valore di **EntitySet** deve essere il set di entità a cui appartiene la raccolta. In caso contrario, l'attributo **EntitySet** non deve essere utilizzato. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** (FunctionImport). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene usato un **FunctionImport** che restituisce libri e autori. Si noti che la funzione restituisce due set di risultati e pertanto sono specificati due elementi **returnType** (FunctionImport).

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>Elemento RowType (CSDL)

Un elemento **RowType** in Conceptual Schema Definition Language (CSDL) definisce una struttura senza nome come parametro o tipo restituito per una funzione definita nel modello concettuale.

Un elemento **RowType** può essere figlio dei seguenti elementi:

-   CollectionType
-   Parametro
-   ReturnType (funzione)

Un elemento **RowType** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Property (uno o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **RowType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .

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

## <a name="schema-element-csdl"></a>Elemento Schema (CSDL)

L'elemento **schema** è l'elemento radice di una definizione del modello concettuale. Contiene definizioni per gli oggetti, le funzioni e i contenitori che costituiscono un modello concettuale.

L'elemento **schema** può contenere zero o più degli elementi figlio seguenti:

-   Con
-   EntityContainer
-   EntityType
-   EnumType
-   Association Rules
-   ComplexType
-   Funzione

Un elemento **dello schema** può contenere zero o un elemento Annotation.

> [!NOTE]
> L'elemento **Function** e gli elementi Annotation sono consentiti solo in CSDL V2 e versioni successive.

 

L'elemento **schema** utilizza l'attributo **namespace** per definire lo spazio dei nomi per il tipo di entità, il tipo complesso e gli oggetti di associazione in un modello concettuale. All'interno di uno spazio dei nomi due oggetti non possono avere lo stesso nome. Gli spazi dei nomi possono estendersi a più elementi **dello schema** e a più file con estensione csdl.

Uno spazio dei nomi del modello concettuale è diverso dallo spazio dei nomi XML dell'elemento **schema** . Uno spazio dei nomi del modello concettuale (definito dall'attributo **namespace** ) è un contenitore logico per tipi di entità, tipi complessi e tipi di associazione. Lo spazio dei nomi XML, indicato dall'attributo **xmlns** , di un elemento **schema** è lo spazio dei nomi predefinito per gli elementi figlio e gli attributi dell'elemento **schema** . Gli spazi dei nomi XML del form https://schemas.microsoft.com/ado/YYYY/MM/edm (dove aaaa e mm rappresentano rispettivamente l'anno e il mese) sono riservati per CSDL. Gli elementi e gli attributi personalizzati non possono essere in spazi dei nomi che hanno questo form.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **schema** .

| Nome attributo | Obbligatorio | Valore                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | Sì         | Spazio dei nomi del modello concettuale. Il valore dell'attributo **namespace** viene utilizzato per formare il nome completo di un tipo. Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi Simple. example. Model, il nome completo di **EntityType** sarà sarà SimpleExampleModel. Customer. <br/> Non è possibile utilizzare le seguenti stringhe come valore per l'attributo **namespace** : **System**, **Transient**o **EDM**. Il valore dell'attributo **namespace** non può corrispondere al valore dell'attributo **namespace** nell'elemento schema SSDL. |
| **Alias**      | No          | Identificatore utilizzato al posto del nome dello spazio dei nomi. Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi Simple. example. Model e il valore dell'attributo **alias** è *Model*, sarà possibile utilizzare Model. Customer come nome completo dell'elemento **EntityType.**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **schema** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **dello schema** che contiene un elemento **EntityContainer** , due elementi **EntityType** e un elemento **Association** .

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
 

 

## <a name="typeref-element-csdl"></a>Elemento TypeRef (CSDL)

L'elemento **typeref** in Conceptual Schema Definition Language (CSDL) fornisce un riferimento a un tipo denominato esistente. L'elemento **typeref** può essere un elemento figlio dell'elemento CollectionType, che viene usato per specificare che una funzione ha una raccolta come parametro o tipo restituito.

Un elemento **typeref** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **typeref** . Si noti che gli attributi **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **scale**, **Unicode**e **Collation** sono applicabili solo a **EDMSimpleTypes**.

| Nome attributo                                                     | Obbligatorio | valore                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**                                                           | No          | Nome del tipo a cui si fa riferimento.                                                                                                                                                                                          |
| **Ammette i valori Null**                                                       | No          | **True** (valore predefinito) o **False**, a seconda che la proprietà possa avere valore null. <br/> [!NOTE]                                                                                                                |
| > in CSDL V1, una proprietà di tipo complesso deve avere `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | No          | Valore predefinito della proprietà.                                                                                                                                                                                              |
| **MaxLength**                                                      | No          | Lunghezza massima del valore della proprietà.                                                                                                                                                                                       |
| **FixedLength**                                                    | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa a lunghezza fissa.                                                                                                                          |
| **Precisione**                                                      | No          | Precisione del valore della proprietà.                                                                                                                                                                                            |
| **Ridimensionamento**                                                          | No          | Scala del valore della proprietà.                                                                                                                                                                                                |
| **SRID**                                                           | No          | Identificatore di riferimento del sistema spaziale. Valido solo per le proprietà dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | No          | **True** o **false** a seconda che il valore della proprietà venga archiviato come stringa Unicode.                                                                                                                               |
| **Regole di confronto**                                                      | No          | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.                                                                                                                                                   |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una funzione definita dal modello che utilizza l'elemento **typeref** (come figlio di un elemento **CollectionType** ) per specificare che la funzione accetta una raccolta di tipi di entità **Department** .

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
 

 

## <a name="using-element-csdl"></a>Elemento Using (CSDL)

L'elemento **using** in Conceptual Schema Definition Language (CSDL) importa il contenuto di un modello concettuale presente in uno spazio dei nomi diverso. Impostando il valore dell'attributo **namespace** , è possibile fare riferimento a tipi di entità, tipi complessi e tipi di associazione definiti in un altro modello concettuale. Più di un elemento **using** può essere figlio di un elemento **dello schema** .

> [!NOTE]
> L'elemento **using** in CSDL non funziona esattamente come un'istruzione **using** in un linguaggio di programmazione. Importando uno spazio dei nomi con un'istruzione **using** in un linguaggio di programmazione, non si influiscono sugli oggetti nello spazio dei nomi originale. In CSDL, uno spazio dei nomi importato può contenere un tipo di entità derivato da un tipo di entità nello spazio dei nomi originale. Ciò può influire sui set di entità dichiarati nello spazio dei nomi originale.

 

L'elemento **using** può includere i seguenti elementi figlio:

-   Documentation (zero elementi o un elemento consentito)
-   Elementi Annotation (zero o più elementi consentiti)

### <a name="applicable-attributes"></a>Attributi applicabili

La tabella seguente descrive gli attributi che possono essere applicati all'elemento **using** .

| Nome attributo | Obbligatorio | Valore                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | Sì         | Nome dello spazio dei nomi importato.                                                                                                                                                |
| **Alias**      | Sì         | Identificatore utilizzato al posto del nome dello spazio dei nomi. Anche se questo attributo è obbligatorio, non è necessario utilizzarlo al posto del nome dello spazio dei nomi per qualificare nomi di oggetti. |

 

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **using** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

 

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **using** usato per importare uno spazio dei nomi definito altrove. Si noti che lo spazio dei nomi per l'elemento **dello schema** visualizzato è `BooksModel` . La `Address` Proprietà in `Publisher` **EntityType** è un tipo complesso definito nello `ExtendedBooksModel` spazio dei nomi (importato con l'elemento **using** ).

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
 

 

## <a name="annotation-attributes-csdl"></a>Attributi di annotazione (CSDL)

Gli attributi di annotazione in Conceptual Schema Definition Language (CSDL) sono attributi XML personalizzati nel modello concettuale. Oltre ad avere una struttura XML valida, per gli attributi di annotazione hanno le caratteristiche seguenti:

-   Gli attributi di annotazione non devono trovarsi in spazi dei nomi XML riservati a CSDL.
-   È possibile applicare più attributi di annotazione a un determinato elemento CSDL.
-   I nomi completi di due attributi di annotazione non devono essere uguali.

Gli attributi di annotazione possono essere utilizzati per fornire metadati aggiuntivi sugli elementi in un modello concettuale. È possibile accedere ai metadati contenuti negli elementi Annotation in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con un attributo Annotation (**CustomAttribute**). Nell'esempio viene inoltre mostrato un elemento di annotazione applicato all'elemento del tipo di entità.

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
 

Il codice seguente recupera i metadati dell'attributo di annotazione e li scrive nella console:

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
 

Nel codice riportato in precedenza si presuppone che il file `School.csdl` si trovi nella directory di output del progetto e che al progetto siano state aggiunte le istruzioni `Imports` e `Using` seguenti:

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>Elementi di annotazione (CSDL)

Gli elementi Annotation in Conceptual Schema Definition Language (CSDL) sono elementi XML personalizzati nel modello concettuale. Oltre ad avere una struttura XML valida, gli elementi Annotation hanno le caratteristiche seguenti:

-   Gli elementi Annotation non devono trovarsi in spazi dei nomi XML riservati a CSDL.
-   Più elementi Annotation possono essere figli di un dato elemento CSDL.
-   I nomi completi di due elementi Annotation non devono essere uguali.
-   Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio di un dato elemento CSDL.

Gli elementi Annotation possono essere utilizzati per fornire metadati aggiuntivi sugli elementi in un modello concettuale. A partire da .NET Framework versione 4, è possibile accedere ai metadati contenuti negli elementi di annotazione in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con un elemento Annotation (**CustomElement**). Nell'esempio viene inoltre mostrato un attributo di annotazione applicato all'elemento del tipo di entità.

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
 

Il codice seguente recupera i metadati dell'elemento Annotation e li scrive nella console:

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
 

Nel codice riportato in precedenza si presuppone che il file School.csdl si trovi nella directory di output del progetto e che al progetto siano state aggiunte le istruzioni `Imports` e `Using` seguenti:

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>Tipi del modello concettuale (CSDL)

CSDL (Conceptual Schema Definition Language) supporta un set di tipi di dati primitivi astratti, denominati **EDMSimpleTypes**, che definiscono le proprietà in un modello concettuale. **EDMSimpleTypes** sono proxy per i tipi di dati primitivi supportati nell'ambiente di archiviazione o host.

Nella tabella seguente vengono elencati i tipi di dati primitivi supportati da CSDL. Nella tabella sono inoltre elencati i facet che è possibile applicare a ogni **EDMSimpleType**.

| EDMSimpleType                    | Description                                                | Facet applicabili                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.Binary**                   | Contiene dati binari.                                      | MaxLength, FixedLength, Nullable, Default                                |
| **Edm.Boolean**                  | Contiene il valore **true** o **false**.                  | Nullable, Default                                                        |
| **Edm.Byte**                     | Contiene un Unsigned Integer a 8 bit.                  | Precision, Nullable, Default                                             |
| **Edm.DateTime**                 | Rappresenta una data e un'ora.                                | Precision, Nullable, Default                                             |
| **Edm.DateTimeOffset**           | Contiene una data e un'ora come offset in minuti rispetto all'ora GMT. | Precision, Nullable, Default                                             |
| **Edm.Decimal**                  | Contiene un valore numerico con scala e precisione fisse.   | Precision, Nullable, Default                                             |
| **Edm.Double**                   | Contiene un numero a virgola mobile con precisione di 15 cifre   | Precision, Nullable, Default                                             |
| **EDM. float**                    | Contiene un numero a virgola mobile con precisione a 7 cifre.   | Precision, Nullable, Default                                             |
| **Edm.Guid**                     | Contiene un identificatore univoco a 16 byte.                      | Precision, Nullable, Default                                             |
| **Edm.Int16**                    | Contiene un Signed Integer a 16 bit.                    | Precision, Nullable, Default                                             |
| **Edm.Int32**                    | Contiene un Signed Integer a 32 bit.                    | Precision, Nullable, Default                                             |
| **Edm.Int64**                    | Contiene un Signed Integer a 64 bit.                    | Precision, Nullable, Default                                             |
| **Edm.SByte**                    | Contiene un Signed Integer a 8 bit.                     | Precision, Nullable, Default                                             |
| **Edm.String**                   | Contiene dati di tipo carattere.                                   | Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default |
| **Edm.Time**                     | Contiene un'ora del giorno.                                    | Precision, Nullable, Default                                             |
| **EDM. Geography**                |                                                            | Nullable, default, SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyLineString**      |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyPolygon**         |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyMultiPoint**      |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyMultiLineString** |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyMultiPolygon**    |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyCollection**      |                                                            | Nullable, default, SRID                                                  |
| **EDM. Geometry**                 |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryPoint**            |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryLineString**       |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryPolygon**          |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryMultiPoint**       |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryMultiLineString**  |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryMultiPolygon**     |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryCollection**       |                                                            | Nullable, default, SRID                                                  |

## <a name="facets-csdl"></a>Facet (CSDL)

I facet in Conceptual Schema Definition Language (CSDL) rappresentano vincoli sulle proprietà di tipi di entità e di tipi complessi. I facet appaiono come attributi XML negli elementi CSDL seguenti:

-   Proprietà
-   TypeRef
-   Parametro

Nella tabella seguente vengono descritti i facet supportati in CSDL. Tutti i facet sono facoltativi. Alcuni facet elencati di seguito vengono usati dal Entity Framework durante la generazione di un database da un modello concettuale.

> [!NOTE]
> Per informazioni sui tipi di dati in un modello concettuale, vedere tipi di modelli concettuali (CSDL).

| Facet               | Description                                                                                                                                                                                                                                                   | Si applica a                                                                                                                                                                                                                                                                                                                                                                           | Utilizzato per la generazione di database. | Utilizzato dal runtime |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **Regole di confronto**       | Specifica la sequenza di ordinamento da usare quando si eseguono operazioni di confronto e di ordinamento su valori della proprietà.                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Sì                              | No                  |
| **ConcurrencyMode** | Indica che il valore della proprietà deve essere usato per le verifiche della concorrenza ottimistica.                                                                                                                                                                    | Tutte le proprietà di **EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | No                               | Sì                 |
| **Default**         | Specifica il valore predefinito della proprietà se durante la creazione di istanze non viene fornito alcun valore.                                                                                                                                                                       | Tutte le proprietà di **EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Sì                              | Sì                 |
| **FixedLength**     | Specifica se la lunghezza del valore della proprietà può variare.                                                                                                                                                                                                  | **EDM. Binary**, **EDM. String**                                                                                                                                                                                                                                                                                                                                                       | Sì                              | No                  |
| **MaxLength**       | Specifica la lunghezza massima del valore della proprietà.                                                                                                                                                                                                           | **EDM. Binary**, **EDM. String**                                                                                                                                                                                                                                                                                                                                                       | Sì                              | No                  |
| **Ammette i valori Null**        | Specifica se la proprietà può avere un valore **null** .                                                                                                                                                                                                     | Tutte le proprietà di **EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Sì                              | Sì                 |
| **Precisione**       | Per le proprietà di tipo **Decimal**, specifica il numero di cifre che un valore della proprietà può avere. Per le proprietà di tipo **Time**, **DateTime**e **DateTimeOffset**, specifica il numero di cifre per la parte frazionaria dei secondi del valore della proprietà. | **EDM. DateTime**, **EDM. DateTimeOffset**, **EDM. Decimal**, **EDM. Time**                                                                                                                                                                                                                                                                                                              | Sì                              | No                  |
| **Ridimensionamento**           | Specifica il numero di cifre a destra del separatore decimale per il valore della proprietà.                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | Sì                              | No                  |
| **SRID**            | Specifica l'ID del sistema di riferimento del sistema spaziale. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).                                                              | **EDM. Geography, Edm. GeographyPoint, Edm. GeographyLineString, Edm. GeographyPolygon, Edm. GeographyMultiPoint, Edm. GeographyMultiLineString, Edm. GeographyMultiPolygon, Edm. GeographyCollection, Edm. Geometry, Edm. GeometryPoint, Edm. GeometryLineString, Edm. GeometryPolygon, Edm. GeometryMultiPoint, Edm. GeometryMultiLineString, Edm. GeometryMultiPolygon, Edm. GeometryCollection** | No                               | Sì                 |
| **Unicode**         | Viene indicato se il valore della proprietà viene archiviato come Unicode.                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Sì                              | Sì                 |

>[!NOTE]
> Quando si genera un database da un modello concettuale, la procedura guidata genera database riconoscerà il valore dell'attributo **StoreGeneratedPattern** su un elemento **Proprietà** se si trova nello spazio dei nomi seguente: https://schemas.microsoft.com/ado/2009/02/edm/annotation . I valori supportati per l'attributo sono **Identity** e **calcolato**. Il valore **Identity** produrrà una colonna di database con un valore Identity generato nel database. Il valore **calcolato** produrrà una colonna con un valore calcolato nel database.

### <a name="example"></a>Esempio

Nell'esempio seguente vengono mostrati i facet applicati alle proprietà di un tipo di entità:

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
