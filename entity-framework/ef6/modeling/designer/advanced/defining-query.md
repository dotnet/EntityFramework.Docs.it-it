---
title: Definizione di query-EF designer-EF6
description: Definizione di query-EF designer in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: 3640156a2ce97dccab563a18d3236aad3dc72ee0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066382"
---
# <a name="defining-query---ef-designer"></a>Definizione di query-EF designer
Questa procedura dettagliata illustra come aggiungere una query di definizione e un tipo di entità corrispondente a un modello usando la finestra di progettazione EF. Una query di definizione viene in genere utilizzata per fornire funzionalità simili a quelle fornite da una vista di database, ma la vista è definita nel modello e non nel database. Una query di definizione consente di eseguire un'istruzione SQL specificata nell'elemento **DefiningQuery**   di un file con estensione edmx. Per ulteriori informazioni, vedere **DefiningQuery** nella [specifica SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Quando si usa la definizione di query, è anche necessario definire un tipo di entità nel modello. Il tipo di entità viene utilizzato per la superficie dei dati esposti dalla query di definizione. Si noti che i dati esposti tramite questo tipo di entità sono di sola lettura.

Le query con parametri non possono essere eseguite come query di definizione. Tuttavia, è possibile aggiornare i dati eseguendo il mapping delle funzioni di inserimento, aggiornamento ed eliminazione del tipo di entità che fa emergere i dati nelle stored procedure. Per ulteriori informazioni, vedere [inserimento, aggiornamento ed eliminazione con le stored procedure](xref:ef6/modeling/designer/stored-procedures/cud).

In questo argomento viene illustrato come eseguire le attività seguenti.

-   Aggiungere una query di definizione
-   Aggiungere un tipo di entità al modello
-   Mappare la query di definizione al tipo di entità

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario disporre di:

- Una versione recente di Visual Studio.
- [Database di esempio School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurare il progetto

Questa procedura dettagliata usa Visual Studio 2012 o versione successiva.

-   Aprire Visual Studio.
-   Scegliere **Nuovo** dal menu **File**e quindi fare clic su **Progetto**.
-   Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **applicazione console** .
-   Immettere **DefiningQuerySample** come nome del progetto e fare clic su **OK**.

 

## <a name="create-a-model-based-on-the-school-database"></a>Creare un modello basato sul database School

-   Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni, scegliere **Aggiungi**, quindi fare clic su **nuovo elemento**.
-   Selezionare **dati** dal menu a sinistra e quindi selezionare **ADO.NET Entity Data Model** nel riquadro modelli.
-   Immettere **DefiningQueryModel. edmx** per il nome del file e quindi fare clic su **Aggiungi**.
-   Nella finestra di dialogo Scegli contenuto Model selezionare **genera da database**, quindi fare clic su **Avanti**.
-   Fare clic su nuova connessione. Nella finestra di dialogo Proprietà connessione immettere il nome del server (ad esempio, **(local DB) \\ mssqllocaldb**), selezionare il metodo di autenticazione, digitare **School**   come nome del database, quindi fare clic su **OK**.
    La finestra di dialogo scegliere la connessione dati viene aggiornata con l'impostazione di connessione al database.
-   Nella finestra di dialogo Scegli oggetti di database selezionare il nodo **tabelle**   . In questo modo, tutte le tabelle vengono aggiunte al modello **School** .
-   Fare clic su **fine**.
-   In Esplora soluzioni fare clic con il pulsante destro del mouse sul file **DefiningQueryModel. edmx** e scegliere **Apri con...**.
-   Seleziona **editor XML (testo)**.

    ![Editor XML](~/ef6/media/xmleditor.png)

-   Fare clic su **Sì** se richiesto con il messaggio seguente:

    ![Avviso 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>Aggiungere una query di definizione

In questo passaggio verrà usato l'editor XML per aggiungere una query di definizione e un tipo di entità alla sezione SSDL del file con estensione edmx. 

-   Aggiungere un elemento **EntitySet**   alla sezione SSDL del file con estensione edmx (riga 5 fino a 13). Specificare le opzioni seguenti:
    -   Vengono specificati solo gli attributi **Name**   e **EntityType**   dell'elemento **EntitySet**   .
    -   Il nome completo del tipo di entità viene utilizzato nell'attributo **EntityType**   .
    -   L'istruzione SQL da eseguire è specificata nell'elemento **DefiningQuery**   .

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   Aggiungere l'elemento **EntityType** alla sezione SSDL di edmx. file come illustrato di seguito. Tenere presente quanto segue:
    -   Il valore dell'attributo **Name** corrisponde al valore dell'attributo **EntityType** nell'elemento **EntitySet** precedente, anche se il nome completo del tipo di entità viene utilizzato nell'attributo **EntityType** .
    -   I nomi delle proprietà corrispondono ai nomi di colonna restituiti dall'istruzione SQL nell'elemento **DefiningQuery** (sopra).
    -   In questo esempio la chiave di entità è composta da tre proprietà, per assicurare un valore di chiave univoco.

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> Se successivamente si esegue la finestra di dialogo della **procedura guidata Aggiorna modello** , tutte le modifiche apportate al modello di archiviazione, inclusa la definizione di query, verranno sovrascritte.

 

## <a name="add-an-entity-type-to-the-model"></a>Aggiungere un tipo di entità al modello

In questo passaggio si aggiungerà il tipo di entità al modello concettuale usando la finestra di progettazione EF. Tenere presente quanto segue:

-   Il **nome** dell'entità corrisponde al valore dell'attributo **EntityType** nell'elemento **EntitySet** riportato sopra.
-   I nomi delle proprietà corrispondono ai nomi di colonna restituiti dall'istruzione SQL nell'elemento **DefiningQuery** precedente.
-   In questo esempio la chiave di entità è composta da tre proprietà, per assicurare un valore di chiave univoco.

Aprire il modello nella finestra di progettazione EF.

-   Fare doppio clic su DefiningQueryModel. edmx.
-   Pronunciare **Yes** sul messaggio seguente:

    ![Avviso 2](~/ef6/media/warning2.png)

 

Viene visualizzata la Entity Designer, che fornisce un'area di progettazione per la modifica del modello.

-   Fare clic con il pulsante destro del mouse sull'area di progettazione e scegliere **Aggiungi nuova**entità... - &gt; **Entity…**.
-   Specificare **GradeReport** per il nome dell'entità e **CourseID** per la **proprietà chiave**.
-   Fare clic con il pulsante destro del mouse sull'entità **GradeReport** e scegliere **Aggiungi nuova** - &gt; **proprietà scalare**.
-   Modificare il nome predefinito della proprietà in **FirstName**.
-   Aggiungere un'altra proprietà scalare e specificare **LastName** come nome.
-   Aggiungere un'altra proprietà scalare e specificare **Grade** per il nome.
-   Nella finestra **Proprietà** modificare la proprietà **Type** del **livello**in **Decimal**.
-   Selezionare le proprietà **FirstName** e **LastName** .
-   Nella finestra **Proprietà** impostare il valore della proprietà **EntityKey** su **true**.

Di conseguenza, gli elementi seguenti sono stati aggiunti alla sezione **CSDL** del file con estensione edmx.

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>Mappare la query di definizione al tipo di entità

In questo passaggio verrà usata la finestra Dettagli mapping per eseguire il mapping dei tipi di entità concettuali e di archiviazione.

-   Fare clic con il pulsante destro del mouse sull'entità **GradeReport** nell'area di progettazione e scegliere **mapping tabella**.  
    Verrà visualizzata la finestra **Dettagli mapping** .
-   Selezionare **GradeReport** nell'elenco ** &lt; a discesa Aggiungi tabella o &gt; vista** (disponibile in **tabella**s).  
    Vengono visualizzati i mapping predefiniti tra il tipo di entità **GradeReport** concettuale e di archiviazione.  
    ![Mapping di Details3](~/ef6/media/mappingdetails.png)

Di conseguenza, l'elemento **EntitySetMapping**   viene aggiunto alla sezione di mapping del file con estensione edmx. 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   Compilare l'applicazione.

 

## <a name="call-the-defining-query-in-your-code"></a>Chiamare la query di definizione nel codice

È ora possibile eseguire la query di definizione usando il tipo di entità **GradeReport** . 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
