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
# <a name="ssdl-specification"></a>Specifica SSDL
Store Schema Definition Language (SSDL) è un linguaggio basato su XML che descrive il modello di archiviazione di un'applicazione Entity Framework.

In un'applicazione Entity Framework i metadati del modello di archiviazione vengono caricati da un file con estensione SSDL (scritto in SSDL) in un'istanza di System. Data. Metadata. Edm. StoreItemCollection ed è possibile accedervi usando i metodi della classe System. Data. Metadata. Edm. MetadataWorkspace. Entity Framework usa i metadati del modello di archiviazione per tradurre le query sul modello concettuale in comandi specifici dell'archivio.

Il Entity Framework Designer (EF designer) archivia le informazioni sul modello di archiviazione in un file con estensione edmx in fase di progettazione. In fase di compilazione il Entity Designer utilizza le informazioni contenute in un file con estensione edmx per creare il file con estensione SSDL necessario per Entity Framework in fase di esecuzione.

Le versioni di SSDL si differenziano tra loro per gli spazi dei nomi XML.

| Versione SSDL | Spazio dei nomi XML                                     |
|:-------------|:--------------------------------------------------|
| SSDL V1      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL V2      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL V3      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Elemento Association (SSDL)

Un elemento **Association** in Store Schema Definition Language (SSDL) specifica le colonne della tabella che fanno parte di un vincolo FOREIGN KEY nel database sottostante. Due elementi End figlio obbligatori specificano le tabelle che costituiscono le entità finali dell'associazione e la molteplicità di ciascuna entità finale. Un elemento ReferentialConstraint facoltativo specifica le entità finali principale e dipendente dell'associazione, nonché le colonne coinvolte. Se non è presente alcun elemento **ReferentialConstraint** , è necessario utilizzare un elemento AssociationSetMapping per specificare i mapping delle colonne per l'associazione.

L'elemento **Association** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   End (esattamente due elementi)
-   ReferentialConstraint (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Association** .

| Nome attributo | Obbligatorio | Valore                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **Nome**       | Sì         | Il nome del vincolo di chiave esterna corrispondente nel database sottostante. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Association** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna ** \_ CustomerOrders FK** :

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

## <a name="associationset-element-ssdl"></a>Elemento AssociationSet (SSDL)

L'elemento **associativo** in Store Schema Definition Language (SSDL) rappresenta un vincolo di chiave esterna tra due tabelle nel database sottostante. Le colonne della tabella che fanno parte del vincolo di chiave esterna vengono specificate in un elemento Association. L'elemento **Association** che corrisponde a **un elemento di associazione specificato è** specificato nell'attributo **Association** dell'elemento associationname. **AssociationSet**

Il mapping dei set di associazioni SSDL viene eseguito in set di associazioni CSDL da un elemento AssociationSetMapping. Tuttavia, se l'associazione CSDL per un determinato set di associazioni CSDL viene definita utilizzando un elemento ReferentialConstraint, non è necessario alcun elemento **AssociationSetMapping** corrispondente. In questo caso, se è presente un elemento **AssociationSetMapping** , i mapping che definisce verranno sottoposti a override dall'elemento **ReferentialConstraint** .

L'elemento **associationname** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   End (zero o due elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento di **associazione** .

| Nome attributo  | Obbligatorio | Valore                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **Nome**        | Sì         | Nome del vincolo di chiave esterna rappresentato dal set di associazioni.                          |
| **Associazione** | Yes         | Nome dell'associazione che definisce le colonne che fanno parte del vincolo di chiave esterna. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento di **associazione** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento di **associazione** che rappresenta il `FK_CustomerOrders` vincolo di chiave esterna nel database sottostante:

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>Elemento CollectionType (SSDL)

L'elemento **CollectionType** in Store Schema Definition Language (SSDL) specifica che il tipo restituito di una funzione è una raccolta. L'elemento **CollectionType** è figlio dell'elemento ReturnType. Il tipo di raccolta viene specificato tramite l'elemento figlio RowType:

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **CollectionType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una funzione che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe.

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

## <a name="commandtext-element-ssdl"></a>Elemento CommandText (SSDL)

L'elemento **CommandText** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento Function che consente di definire un'istruzione SQL che viene eseguita nel database. L'elemento **CommandText** consente di aggiungere una funzionalità simile a una stored procedure nel database, ma l'elemento **CommandText** viene definito nel modello di archiviazione.

L'elemento **CommandText** non può contenere elementi figlio. Il corpo dell'elemento **CommandText** deve essere un'istruzione SQL valida per il database sottostante.

Nessun attributo applicabile all'elemento **CommandText** .

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Function** con un elemento **CommandText** figlio. Esporre la funzione **UpdateProductInOrder** come metodo in ObjectContext mediante l'importazione nel modello concettuale.  

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

## <a name="definingquery-element-ssdl"></a>Elemento DefiningQuery (SSDL)

L'elemento **DefiningQuery** in Store Schema Definition Language (SSDL) consente di eseguire un'istruzione SQL direttamente nel database sottostante. L'elemento **DefiningQuery** viene comunemente utilizzato come una vista di database, ma la vista è definita nel modello di archiviazione anziché nel database. La vista definita in un elemento **DefiningQuery** può essere mappata a un tipo di entità nel modello concettuale tramite un elemento EntitySetMapping. Questi mapping sono di sola lettura.  

Nella sintassi SSDL seguente viene illustrata la dichiarazione di un oggetto **EntitySet** seguito dall'elemento **DefiningQuery** che contiene una query utilizzata per recuperare la visualizzazione.

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

È possibile utilizzare le stored procedure nel Entity Framework per abilitare gli scenari di lettura e scrittura sulle visualizzazioni.È possibile utilizzare una vista origine dati o una vista Entity SQL come tabella di base per il recupero dei dati e per l'elaborazione delle modifiche da parte delle stored procedure.

È possibile usare l'elemento **DefiningQuery** per la destinazione Microsoft SQL Server Compact 3,5. Sebbene SQL Server Compact 3,5 non supporti stored procedure, è possibile implementare una funzionalità simile con l'elemento **DefiningQuery** . Un'altra situazione in cui può essere utile è nella creazione di stored procedure per risolvere una mancata corrispondenza tra i tipi di dati utilizzati nel linguaggio di programmazione e quelli dell'origine dati. È possibile scrivere un **DefiningQuery** che accetta un determinato set di parametri e quindi chiama un stored procedure con un set di parametri diverso, ad esempio un stored procedure che elimina i dati.

## <a name="dependent-element-ssdl"></a>Elemento Dependent (SSDL)

L'elemento **dipendente** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale dipendente di un vincolo di chiave esterna (detto anche vincolo referenziale). L'elemento **dipendente** specifica la colonna (o le colonne) in una tabella che fa riferimento a una colonna di chiave primaria (o a colonne). Gli elementi **PropertyRef** specificano le colonne a cui si fa riferimento. L'elemento Principal specifica le colonne chiave primaria a cui fanno riferimento le colonne specificate nell'elemento **dipendente** .

L'elemento **dipendente** può includere i seguenti elementi figlio (nell'ordine elencato):

-   PropertyRef (uno o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **dipendente** .

| Nome attributo | Obbligatorio | Valore                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Ruolo**       | Sì         | Lo stesso valore dell'attributo **Role** (se utilizzato) dell'elemento End corrispondente; in caso contrario, il nome della tabella che contiene la colonna di riferimento. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **dipendente** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento Association che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna ** \_ CustomerOrders FK** . L'elemento **dipendente** specifica la colonna **CustomerID** della tabella **Order** come entità finale dipendente del vincolo.

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

## <a name="documentation-element-ssdl"></a>Elemento Documentation (SSDL)

L'elemento **Documentation** in Store Schema Definition Language (SSDL) può essere utilizzato per fornire informazioni su un oggetto definito in un elemento padre.

L'elemento **Documentation** può includere i seguenti elementi figlio (nell'ordine elencato):

-   **Riepilogo**: breve descrizione dell'elemento padre. Zero o un elemento.
-   **LongDescription**: Descrizione completa dell'elemento padre. Zero o un elemento.

### <a name="applicable-attributes"></a>Attributi applicabili

All'elemento **Documentation** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato l'elemento **Documentation** come elemento figlio di un elemento EntityType.

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

## <a name="end-element-ssdl"></a>Elemento End (SSDL)

L'elemento **end** in Store Schema Definition Language (SSDL) specifica la tabella e il numero di righe a un'estremità di un vincolo FOREIGN KEY nel database sottostante. L'elemento **end** può essere un elemento figlio dell'elemento Association o dell'elemento associationname. In entrambi i casi, gli elementi figlio possibili e gli attributi applicabili sono diversi.

### <a name="end-element-as-a-child-of-the-association-element"></a>Elemento End come figlio dell'elemento Association

Un elemento **end** (come figlio dell'elemento **Association** ) specifica la tabella e il numero di righe alla fine di un vincolo FOREIGN KEY con rispettivamente il **tipo** e gli attributi di **molteplicità** . Le entità finali del vincolo di chiave esterna sono definite come parte dell'associazione SSDL. L'associazione SSDL deve disporre esattamente di due entità finali.

Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   OnDelete (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **finale** quando è figlio di un elemento **Association** .

| Nome attributo   | Obbligatorio | Valore                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**         | Sì         | Il nome completo del set di entità SSDL che si trova in corrispondenza dell'entità finale del vincolo di chiave esterna.                                                                                                                                                                                                                                                                                          |
| **Ruolo**         | No          | Valore dell'attributo **Role** nell'elemento Principal o dipendente dell'elemento ReferentialConstraint corrispondente (se usato).                                                                                                                                                                                                                                             |
| **Molteplicità** | Yes         | **1**, **0.. 1**o a **\*** seconda del numero di righe che possono trovarsi alla fine del vincolo FOREIGN KEY. <br/> **1** indica che esiste esattamente una riga nell'entità finale del vincolo di chiave esterna. <br/> **0.. 1** indica che l'entità finale del vincolo di chiave esterna è pari a zero o a una riga. <br/> **\*** indica che nell'entità finale del vincolo di chiave esterna sono presenti zero, una o più righe. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che definisce il vincolo di chiave esterna ** \_ CustomerOrders FK** . I valori di **molteplicità** specificati in ogni elemento **end** indicano che molte righe della tabella **Orders** possono essere associate a una riga nella tabella **Customers** , ma solo una riga nella tabella **Customers** può essere associata a una riga nella tabella **Orders** . Inoltre, l'elemento **OnDelete** indica che tutte le righe della tabella **Orders** che fanno riferimento a una determinata riga nella tabella **Customers** verranno eliminate se la riga della tabella **Customers** viene eliminata.

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Elemento End come figlio dell'elemento AssociationSet

L'elemento **end** (come figlio dell'elemento **associationname** ) specifica una tabella in corrispondenza di un'entità finale di un vincolo FOREIGN KEY nel database sottostante.

Un elemento **end** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

#### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che possono essere applicati all'elemento **finale** quando è figlio di un elemento di **associazione** .

| Nome attributo | Obbligatorio | Valore                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | Yes         | Il nome del set di entità SSDL in corrispondenza dell'entità finale del vincolo di chiave esterna.                                      |
| **Ruolo**       | No          | Valore di uno degli attributi **Role** specificati in un elemento **end** dell'elemento Association corrispondente. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **finale** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

#### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** con un elemento di **associazione** con due elementi **end** :

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

## <a name="entitycontainer-element-ssdl"></a>Elemento EntityContainer (SSDL)

Un elemento **EntityContainer** in Store Schema Definition Language (SSDL) descrive la struttura dell'origine dati sottostante in un'applicazione Entity Framework: i set di entità SSDL (definiti negli elementi EntitySet) rappresentano le tabelle in un database, i tipi di entità SSDL (definiti negli elementi EntityType) rappresentano le righe in una tabella e i set di associazioni (definiti in elementi di associazioni) rappresentano vincoli di chiave esterna in un database. Un contenitore di entità del modello di archiviazione esegue il mapping a un contenitore di entità del modello concettuale attraverso l'elemento EntityContainerMapping.

Un elemento **EntityContainer** può avere uno o più elementi di documentazione. Se è presente un elemento di **documentazione** , deve precedere tutti gli altri elementi figlio.

Un elemento **EntityContainer** può avere zero o più degli elementi figlio seguenti (nell'ordine elencato):

-   EntitySet
-   AssociationSet
-   Elementi Annotation

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityContainer** .

| Nome attributo | Obbligatorio | Valore                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del contenitore di entità. Il nome non può contenere caratteri punto (.). |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityContainer** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** che definisce due set di entità e un set di associazioni. I nomi dei tipi di entità e associazione sono qualificati dal nome dello spazio dei nomi del modello concettuale.

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

## <a name="entityset-element-ssdl"></a>Elemento EntitySet (SSDL)

Un elemento **EntitySet** in Store Schema Definition Language (SSDL) rappresenta una tabella o una vista nel database sottostante. Un elemento EntityType in SSDL rappresenta una riga nella tabella o nella visualizzazione. L'attributo **EntityType** di un elemento **EntitySet** specifica il particolare tipo di entità SSDL che rappresenta le righe in un set di entità SSDL. Il mapping tra un set di entità CSDL e un set di entità SSDL viene specificato in un elemento EntitySetMapping.

L'elemento **EntitySet** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   DefiningQuery (zero o un elemento)
-   Elementi Annotation

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntitySet** .

> [!NOTE]
> Alcuni attributi (non elencati qui) possono essere qualificati con l'alias del **negozio** . Questi attributi vengono utilizzati dalla procedura guidata Aggiorna modello in caso di aggiornamento di un modello.

| Nome attributo | Obbligatorio | Valore                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del set di entità.                                                              |
| **EntityType** | Yes         | Nome completo del tipo di entità per il quale il set di entità contiene delle istanze. |
| **Schema**     | No          | Schema del database.                                                                     |
| **Tabella**      | No          | Tabella del database.                                                                      |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntitySet** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityContainer** che dispone di due elementi **EntitySet** e di un elemento di **associazione** :

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

## <a name="entitytype-element-ssdl"></a>Elemento EntityType (SSDL)

Un elemento **EntityType** in Store Schema Definition Language (SSDL) rappresenta una riga in una tabella o vista del database sottostante. Un elemento EntitySet in SSDL rappresenta la tabella o la vista in cui si trova la riga. L'attributo **EntityType** di un elemento **EntitySet** specifica il particolare tipo di entità SSDL che rappresenta le righe in un set di entità SSDL. Il mapping tra un tipo di entità SSDL e un tipo di entità CSDL viene specificato in un elemento EntityTypeMapping.

L'elemento **EntityType** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Key (zero o un elemento)
-   Elementi Annotation

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **EntityType** .

| Nome attributo | Obbligatorio | Valore                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del tipo di entità. Questo valore corrisponde generalmente al nome della tabella in cui il tipo di entità rappresenta una riga. Questo valore non può contenere punti (.). |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **EntityType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con due proprietà:

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

## <a name="function-element-ssdl"></a>Elemento Function (SSDL)

L'elemento **Function** in Store Schema Definition Language (SSDL) specifica una stored procedure esistente nel database sottostante.

L'elemento **Function** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Parameter (zero o più)
-   CommandText (zero o uno)
-   ReturnType (zero o più)
-   Elementi Annotation (zero o più elementi)

Un tipo restituito per una funzione deve essere specificato con l'elemento **returnType** o l'attributo **returnType** (vedere di seguito), ma non entrambi.

È possibile importare stored procedure specificate nel modello di archiviazione nel modello concettuale di un'applicazione. Per ulteriori informazioni, vedere [esecuzione di query con stored procedure](xref:ef6/modeling/designer/stored-procedures/query). L'elemento **Function** può essere utilizzato anche per definire funzioni personalizzate nel modello di archiviazione.  

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Function** .

> [!NOTE]
> Alcuni attributi (non elencati qui) possono essere qualificati con l'alias del **negozio** . Questi attributi vengono utilizzati dalla procedura guidata Aggiorna modello in caso di aggiornamento di un modello.

| Nome attributo             | Obbligatorio | Valore                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                   | Sì         | Nome della stored procedure.                                                                                                                                                                                  |
| **ReturnType**             | No          | Tipo restituito della stored procedure.                                                                                                                                                                           |
| **Aggregata**              | No          | **True** se il stored procedure restituisce un valore di aggregazione. in caso contrario, **false**.                                                                                                                                  |
| **BuiltIn**                | No          | **True** se la funzione è una funzione incorporata<sup>1</sup> . in caso contrario, **false**.                                                                                                                                  |
| **StoreFunctionName**      | No          | Nome della stored procedure.                                                                                                                                                                                  |
| **NiladicFunction**        | No          | **True** se la funzione è una funzione senza parametri<sup>2</sup> . In caso contrario, **false** .                                                                                                                                   |
| **IsComposable**           | No          | **True** se la funzione è una funzione componibile<sup>3</sup> . In caso contrario, **false** .                                                                                                                                |
| **ParameterTypeSemantics** | No          | Enumerazione che definisce la semantica dei tipi utilizzata per risolvere gli overload della funzione. L'enumerazione è definita nel file manifesto del provider per ogni definizione di funzione. Il valore predefinito è **AllowImplicitConversion**. |
| **Schema**                 | No          | Nome dello schema in cui viene definita la stored procedure.                                                                                                                                                   |

<sup>1</sup> una funzione predefinita è una funzione definita nel database. Per informazioni sulle funzioni definite nel modello di archiviazione, vedere elemento CommandText (SSDL).

<sup>2</sup> una funzione senza parametri è una funzione che non accetta parametri e, quando viene chiamato, non richiede le parentesi.

<sup>3</sup> due funzioni sono componibili se l'output di una funzione può essere l'input per l'altra funzione.

> [!NOTE]
> All'elemento **Function** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Function** che corrisponde alla stored procedure **UpdateOrderQuantity** . La stored procedure accetta due parametri e non restituisce alcun valore.

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

## <a name="key-element-ssdl"></a>Elemento Key (SSDL)

L'elemento **Key** in Store Schema Definition Language (SSDL) rappresenta la chiave primaria di una tabella nel database sottostante. **Key** è un elemento figlio di un elemento EntityType, che rappresenta una riga in una tabella. La chiave primaria è definita nell'elemento **chiave** facendo riferimento a uno o più elementi Property definiti nell'elemento **EntityType** .

L'elemento **Key** può includere i seguenti elementi figlio (nell'ordine elencato):

-   PropertyRef (uno o più elementi)
-   Elementi Annotation

Nessun attributo applicabile all'elemento **chiave** .

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con una chiave che fa riferimento a una proprietà:

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

## <a name="ondelete-element-ssdl"></a>Elemento OnDelete (SSDL)

L'elemento **OnDelete** in Store Schema Definition Language (SSDL) riflette il comportamento del database quando viene eliminata una riga che fa parte di un vincolo FOREIGN KEY. Se l'azione è impostata su **Cascade**, verranno eliminate anche le righe che fanno riferimento a una riga in fase di eliminazione. Se l'azione è impostata su **None**, non vengono eliminate anche le righe che fanno riferimento a una riga eliminata. Un elemento **OnDelete** è un elemento figlio di un elemento End.

Un elemento **OnDelete** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **OnDelete** .

| Nome attributo | Obbligatorio | Valore                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **Azione**     | Yes         | **Cascade** o **None**. (Il valore con **restrizioni** è valido ma ha lo stesso comportamento di **None**). |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **OnDelete** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che definisce il vincolo di chiave esterna ** \_ CustomerOrders FK** . L'elemento **OnDelete** indica che tutte le righe della tabella **Orders** che fanno riferimento a una determinata riga della tabella **Customers** verranno eliminate se la riga della tabella **Customers** viene eliminata.

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

## <a name="parameter-element-ssdl"></a>Elemento Parameter (SSDL)

L'elemento **Parameter** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento Function che specifica i parametri per un stored procedure nel database.

L'elemento **Parameter** può includere i seguenti elementi figlio (nell'ordine elencato):

-   Documentation (zero o un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Parameter** .

| Nome attributo | Obbligatorio | Valore                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**       | Sì         | Nome del parametro.                                                                                                                                                                                                      |
| **Tipo**       | Sì         | Tipo di parametro.                                                                                                                                                                                                             |
| **Modalità**       | No          | **In**, **out**o **InOut** a seconda che il parametro sia un parametro di input, di output o di input/output.                                                                                                                |
| **MaxLength**  | No          | Lunghezza massima del parametro.                                                                                                                                                                                            |
| **Precisione**  | No          | Precisione del parametro                                                                                                                                                                                                 |
| **Ridimensionamento**      | No          | Scalabilità del parametro                                                                                                                                                                                                     |
| **SRID**       | No          | Identificatore di riferimento del sistema spaziale. Valido solo per i parametri dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

> [!NOTE]
> All'elemento **Parameter** è possibile applicare qualsiasi numero di attributi di annotazione (attributi XML personalizzati). Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Function** con due elementi **Parameter** che specificano i parametri di input:

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

## <a name="principal-element-ssdl"></a>Elemento Principal (SSDL)

L'elemento **Principal** in Store Schema Definition Language (SSDL) è un elemento figlio dell'elemento ReferentialConstraint che definisce l'entità finale principale di un vincolo FOREIGN KEY (detto anche vincolo referenziale). L'elemento **Principal** specifica la colonna o le colonne di chiave primaria in una tabella a cui fa riferimento un'altra colonna o colonne. Gli elementi **PropertyRef** specificano le colonne a cui si fa riferimento. L'elemento dipendente specifica le colonne che fanno riferimento alle colonne di chiave primaria specificate nell'elemento **Principal** .

L'elemento **Principal** può includere i seguenti elementi figlio (nell'ordine elencato):

-   PropertyRef (uno o più elementi)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Principal** .

| Nome attributo | Obbligatorio | Valore                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Ruolo**       | Sì         | Lo stesso valore dell'attributo **Role** (se utilizzato) dell'elemento End corrispondente; in caso contrario, il nome della tabella che contiene la colonna a cui si fa riferimento. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **principale** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento Association che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna ** \_ CustomerOrders FK** . L'elemento **Principal** specifica la colonna **CustomerID** della tabella **Customer** come entità finale principale del vincolo.

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

## <a name="property-element-ssdl"></a>Elemento Property (SSDL)

L'elemento **Property** in Store Schema Definition Language (SSDL) rappresenta una colonna in una tabella nel database sottostante. Gli elementi **Proprietà** sono elementi figlio degli elementi EntityType, che rappresentano le righe in una tabella. Ogni elemento **Property** definito in un elemento **EntityType** rappresenta una colonna.

Un elemento **Property** non può contenere elementi figlio.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **Property** .

| Nome attributo            | Obbligatorio | Valore                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nome**                  | Sì         | Nome della colonna corrispondente.                                                                                                                                                                                           |
| **Tipo**                  | Sì         | Tipo della colonna corrispondente.                                                                                                                                                                                           |
| **Ammette i valori Null**              | No          | **True** (valore predefinito) o **false** a seconda che la colonna corrispondente possa avere un valore null.                                                                                                                  |
| **DefaultValue**          | No          | Valore predefinito della colonna corrispondente.                                                                                                                                                                                  |
| **MaxLength**             | No          | Lunghezza massima della colonna corrispondente.                                                                                                                                                                                 |
| **FixedLength**           | No          | **True** o **false** a seconda che il valore della colonna corrispondente venga archiviato come stringa a lunghezza fissa.                                                                                                              |
| **Precisione**             | No          | Precisione della colonna corrispondente.                                                                                                                                                                                      |
| **Ridimensionamento**                 | No          | Scala della colonna corrispondente.                                                                                                                                                                                          |
| **Unicode**               | No          | **True** o **false** a seconda che il valore della colonna corrispondente venga archiviato come stringa Unicode.                                                                                                                   |
| **Regole di confronto**             | No          | Stringa che specifica la sequenza di collazione da utilizzare nell'origine dati.                                                                                                                                                   |
| **SRID**                  | No          | Identificatore di riferimento del sistema spaziale. Valido solo per le proprietà dei tipi spaziali. Per ulteriori informazioni, vedere [SRID](https://en.wikipedia.org/wiki/SRID) e [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **StoreGeneratedPattern** | No          | **None**, **Identity** (se il valore della colonna corrispondente è un'identità generata nel database) o **calcolata** (se il valore della colonna corrispondente viene calcolato nel database). Non valido per le proprietà RowType. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **Property** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **EntityType** con due elementi **Proprietà** figlio:

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

## <a name="propertyref-element-ssdl"></a>Elemento PropertyRef (SSDL)

L'elemento **PropertyRef** in Store Schema Definition Language (SSDL) fa riferimento a una proprietà definita in un elemento EntityType per indicare che la proprietà eseguirà uno dei ruoli seguenti:

-   Far parte della chiave primaria della tabella rappresentata da **EntityType** . Per definire una chiave primaria, è possibile usare uno o più elementi **PropertyRef** . Per ulteriori informazioni, vedere Elemento Key.
-   Rappresenterà l'entità finale dipendente o principale di un vincolo referenziale. Per ulteriori informazioni, vedere Elemento ReferentialConstraint.

L'elemento **PropertyRef** può avere solo gli elementi figlio seguenti:

-   Documentation (zero o un elemento)
-   Elementi Annotation

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **PropertyRef** .

| Nome attributo | Obbligatorio | Valore                                |
|:---------------|:------------|:-------------------------------------|
| **Nome**       | Sì         | Nome della proprietà alla quale viene fatto riferimento. |

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **PropertyRef** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a CSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **PropertyRef** utilizzato per definire una chiave primaria facendo riferimento a una proprietà definita in un elemento **EntityType** .

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

## <a name="referentialconstraint-element-ssdl"></a>Elemento ReferentialConstraint (SSDL)

L'elemento **ReferentialConstraint** in Store Schema Definition Language (SSDL) rappresenta un vincolo di chiave esterna (detto anche vincolo di integrità referenziale) nel database sottostante. Le estremità principale e dipendente del vincolo vengono specificate rispettivamente dagli elementi figlio Principal e Dependent. Alle colonne che fanno parte delle estremità principale e dipendente viene fatto riferimento tramite gli elementi PropertyRef.

L'elemento **ReferentialConstraint** è un elemento figlio facoltativo dell'elemento Association. Se non viene utilizzato un elemento **ReferentialConstraint** per eseguire il mapping del vincolo foreign key specificato nell'elemento **Association** , a tale scopo è necessario utilizzare un elemento AssociationSetMapping.

L'elemento **ReferentialConstraint** può avere gli elementi figlio seguenti:

-   Documentation (zero o un elemento)
-   Principal (esattamente un elemento)
-   Dependent (esattamente un elemento)
-   Elementi Annotation (zero o più elementi)

### <a name="applicable-attributes"></a>Attributi applicabili

Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **ReferentialConstraint** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **Association** che usa un elemento **ReferentialConstraint** per specificare le colonne che fanno parte del vincolo di chiave esterna ** \_ CustomerOrders FK** :

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

## <a name="returntype-element-ssdl"></a>Elemento ReturnType (SSDL)

L'elemento **returnType** in Store Schema Definition Language (SSDL) specifica il tipo restituito per una funzione definita in un elemento **Function** . Un tipo restituito di funzione può essere specificato anche con un attributo **returnType** .

Il tipo restituito di una funzione viene specificato con l'attributo **Type** o l'elemento **returnType** .

L'elemento **returnType** può avere gli elementi figlio seguenti:

- CollectionType (uno)  

> [!NOTE]
> Qualsiasi numero di attributi di annotazione (attributi XML personalizzati) può essere applicato all'elemento **returnType** . Tuttavia, gli attributi personalizzati non possono appartenere ad alcuno spazio dei nomi XML riservato a SSDL. I nomi completi per due attributi personalizzati qualsiasi non possono essere uguali.

### <a name="example"></a>Esempio

Nell'esempio seguente viene utilizzata una **funzione** che restituisce una raccolta di righe.

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


## <a name="rowtype-element-ssdl"></a>Elemento RowType (SSDL)

Un elemento **RowType** in Store Schema Definition Language (SSDL) definisce una struttura senza nome come tipo restituito per una funzione definita nell'archivio.

Un elemento **RowType** è l'elemento figlio dell'elemento **CollectionType** :

Un elemento **RowType** può avere gli elementi figlio seguenti:

- Property (uno o più elementi)  

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrata una funzione di archiviazione che utilizza un elemento **CollectionType** per specificare che la funzione restituisce una raccolta di righe, come specificato nell'elemento **RowType** .


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

## <a name="schema-element-ssdl"></a>Elemento Schema (SSDL)

L'elemento **schema** nel Store Schema Definition Language (SSDL) è l'elemento radice di una definizione del modello di archiviazione. Contiene definizioni per gli oggetti, le funzioni e i contenitori che costituiscono un modello di archiviazione.

L'elemento **schema** può contenere zero o più degli elementi figlio seguenti:

-   Association Rules
-   EntityType
-   EntityContainer
-   Funzione

L'elemento **schema** utilizza l'attributo **namespace** per definire lo spazio dei nomi per il tipo di entità e gli oggetti di associazione in un modello di archiviazione. All'interno di uno spazio dei nomi due oggetti non possono avere lo stesso nome.

Uno spazio dei nomi del modello di archiviazione è diverso dallo spazio dei nomi XML dell'elemento **schema** . Uno spazio dei nomi del modello di archiviazione (definito dall'attributo **namespace** ) è un contenitore logico per tipi di entità e tipi di associazione. Lo spazio dei nomi XML, indicato dall'attributo **xmlns** , di un elemento **schema** è lo spazio dei nomi predefinito per gli elementi figlio e gli attributi dell'elemento **schema** . Gli spazi dei nomi XML del form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (dove aaaa e mm rappresentano rispettivamente l'anno e il mese) sono riservati per SSDL. Gli elementi e gli attributi personalizzati non possono essere in spazi dei nomi che hanno questo form.

### <a name="applicable-attributes"></a>Attributi applicabili

Nella tabella seguente vengono descritti gli attributi che è possibile applicare all'elemento **schema** .

| Nome attributo            | Obbligatorio | Valore                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**             | Yes         | Spazio dei nomi del modello di archiviazione. Il valore dell'attributo **namespace** viene utilizzato per formare il nome completo di un tipo. Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi ExampleModel. Store, il nome completo di **EntityType** sarà ExampleModel. Store. Customer. <br/> Non è possibile utilizzare le seguenti stringhe come valore per l'attributo **namespace** : **System**, **Transient**o **EDM**. Il valore dell'attributo **namespace** non può corrispondere al valore dell'attributo **namespace** nell'elemento schema CSDL. |
| **Alias**                 | No          | Identificatore utilizzato al posto del nome dello spazio dei nomi. Se, ad esempio, un elemento **EntityType** denominato *Customer* si trova nello spazio dei nomi ExampleModel. Store e il valore dell'attributo **alias** è *StorageModel*, è possibile utilizzare StorageModel. Customer come nome completo dell'elemento **EntityType.**                                                                                                                                                                                                                                                                                    |
| **Provider**              | Yes         | Provider di dati.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | Yes         | Token che indica al provider quale manifesto del provider restituire. Non è definito alcun formato per il token. I valori per il token sono definiti dal provider. Per informazioni sui token del manifesto del provider SQL Server, vedere SqlClient per Entity Framework.                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento **dello schema** che contiene un elemento **EntityContainer** , due elementi **EntityType** e un elemento **Association** .

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

## <a name="annotation-attributes"></a>Attributi di annotazione

Gli attributi di annotazione in Store Schema Definition Language (SSDL) sono attributi XML personalizzati nel modello di archiviazione che forniscono metadati aggiuntivi sugli elementi del modello di archiviazione. Oltre ad avere una struttura XML valida, agli attributi di annotazione si applicano i vincoli seguenti:

-   Gli attributi di annotazione non devono trovarsi in spazi dei nomi XML riservati a SSDL.
-   I nomi completi di due attributi di annotazione non devono essere uguali.

È possibile applicare più attributi di annotazione a un determinato elemento SSDL. È possibile accedere ai metadati contenuti negli elementi Annotation in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento EntityType con un attributo annotation applicato alla proprietà **OrderID** . Nell'esempio viene inoltre mostrato un elemento Annotation aggiunto all'elemento **EntityType** .

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

## <a name="annotation-elements-ssdl"></a>Elementi Annotation (SSDL)

Gli elementi Annotation in SSDL (Store Schema Definition Language) sono elementi XML personalizzati nel modello di archiviazione che forniscono metadati aggiuntivi sul modello di archiviazione. Oltre ad avere una struttura XML valida, agli elementi Annotation si applicano i vincoli seguenti:

-   Gli elementi Annotation non devono trovarsi in spazi dei nomi XML riservati a SSDL.
-   I nomi completi di due elementi Annotation non devono essere uguali.
-   Gli elementi Annotation devono apparire dopo tutti gli altri elementi figlio di un dato elemento SSDL.

Più elementi Annotation possono essere figli di un dato elemento SSDL. A partire da .NET Framework versione 4, è possibile accedere ai metadati contenuti negli elementi di annotazione in fase di esecuzione usando le classi nello spazio dei nomi System. Data. Metadata. Edm.

### <a name="example"></a>Esempio

Nell'esempio seguente viene illustrato un elemento EntityType con un elemento Annotation (**CustomElement**). Nell'esempio viene inoltre illustrato un attributo di annotazione applicato alla proprietà **OrderID** .

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

## <a name="facets-ssdl"></a>Facet (SSDL)

I facet in Store Schema Definition Language (SSDL) rappresentano dei vincoli sui tipi di colonna specificati in elementi Property. I facet vengono implementati come attributi XML sugli elementi della **Proprietà** .

Nella tabella seguente vengono descritti i facet supportati in SSDL:

| Facet           | Descrizione                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Regole di confronto**   | Specifica la sequenza di ordinamento da usare quando si eseguono operazioni di confronto e di ordinamento su valori della proprietà.                                                                                                             |
| **FixedLength** | Specifica se la lunghezza del valore della colonna può variare.                                                                                                                                                                                                  |
| **MaxLength**   | Specifica la lunghezza massima del valore della colonna.                                                                                                                                                                                                           |
| **Precisione**   | Per le proprietà di tipo **Decimal**, specifica il numero di cifre che un valore della proprietà può avere. Per le proprietà di tipo **Time**, **DateTime**e **DateTimeOffset**, specifica il numero di cifre per la parte frazionaria dei secondi del valore della colonna. |
| **Ridimensionamento**       | Specifica il numero di cifre a destra del separatore decimale per il valore della colonna.                                                                                                                                                                      |
| **Unicode**     | Indica se il valore della colonna viene archiviato come Unicode.                                                                                                                                                                                                    |
