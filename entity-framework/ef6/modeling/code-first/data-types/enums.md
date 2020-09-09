---
title: Supporto enum-Code First-EF6
description: Supporto enum-Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 77a42501-27c9-4f4b-96df-26c128021467
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: 2ed5cd73f8477469d3651ef4bf9f79ce80598ee7
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617173"
---
# <a name="enum-support---code-first"></a>Supporto enum-Code First
> [!NOTE]
> **EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Questo video e la procedura dettagliata illustrano come usare i tipi enum con Entity Framework Code First. Viene inoltre illustrato come utilizzare le enumerazioni in una query LINQ.

In questa procedura dettagliata verrà utilizzato Code First per creare un nuovo database, ma è possibile utilizzare anche [Code First per eseguire il mapping a un database esistente](xref:ef6/modeling/code-first/workflows/existing-database).

Il supporto enum è stato introdotto in Entity Framework 5. Per utilizzare le nuove funzionalità come enum, i tipi di dati spaziali e le funzioni con valori di tabella, è necessario specificare come destinazione .NET Framework 4,5. Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.

In Entity Framework, un'enumerazione può includere i seguenti tipi sottostanti: **byte**, **Int16**, **Int32**, **Int64** o **SByte**.

## <a name="watch-the-video"></a>Video
In questo video viene illustrato come utilizzare i tipi enum con Entity Framework Code First. Viene inoltre illustrato come utilizzare le enumerazioni in una query LINQ.

**Presentato da**: Julia Kornich

**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.

 

## <a name="set-up-the-project"></a>Configurare il progetto

1.  Aprire Visual Studio 2012
2.  Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .
3.  Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
4.  Immettere **EnumCodeFirst** come nome del progetto e fare clic su **OK** .

## <a name="define-a-new-model-using-code-first"></a>Definire un nuovo modello utilizzando Code First

Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio). Il codice seguente definisce la classe Department.

Il codice definisce anche l'enumerazione DepartmentNames. Per impostazione predefinita, l'enumerazione è di tipo **int** . La proprietà Name della classe Department è del tipo DepartmentNames.

Aprire il file Program.cs e incollare le definizioni delle classi seguenti.

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a>Definire il tipo derivato DbContext

Oltre alla definizione delle entità, è necessario definire una classe che deriva da DbContext ed espone le proprietà di DbSet &lt; TEntity &gt; . Le proprietà di DbSet &lt; TEntity &gt; consentono al contesto di individuare i tipi che si desidera includere nel modello.

Un'istanza del tipo derivato DbContext gestisce gli oggetti entità in fase di esecuzione, che include il popolamento di oggetti con dati da un database, il rilevamento delle modifiche e il salvataggio permanente dei dati nel database.

I tipi DbContext e DbSet sono definiti nell'assembly EntityFramework. Si aggiungerà un riferimento a questa DLL usando il pacchetto NuGet EntityFramework.

1.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul nome del progetto.
2.  Selezionare **Gestisci pacchetti NuGet...**
3.  Nella finestra di dialogo Gestisci pacchetti NuGet selezionare la scheda **online** e scegliere il pacchetto **EntityFramework** .
4.  Fai clic su **Install** (Installa).

Si noti che, oltre all'assembly EntityFramework, vengono aggiunti anche i riferimenti agli assembly System. ComponentModel. DataAnnotations e System. Data. Entity.

Nella parte superiore del file Program.cs aggiungere l'istruzione using seguente:

``` csharp
using System.Data.Entity;
```

In Program.cs aggiungere la definizione del contesto. 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>Mantieni e recupera dati

Aprire il file Program.cs in cui è definito il metodo Main. Aggiungere il codice seguente nella funzione Main. Il codice aggiunge un nuovo oggetto Department al contesto. Quindi Salva i dati. Il codice esegue anche una query LINQ che restituisce un reparto in cui il nome è DepartmentNames. English.

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Compilare l'applicazione ed eseguirla. Il programma produce l'output seguente:

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>Visualizzare il database generato

Quando si esegue l'applicazione per la prima volta, il Entity Framework crea automaticamente un database. Poiché è installato Visual Studio 2012, il database verrà creato nell'istanza del database locale. Per impostazione predefinita, il Entity Framework denomina il database dopo il nome completo del contesto derivato (per questo esempio **EnumCodeFirst. EnumTestContext**). Le volte successive verrà utilizzato il database esistente.  

Si noti che, se si apportano modifiche al modello dopo che è stato creato il database, è necessario utilizzare Migrazioni Code First per aggiornare lo schema del database. Per un esempio di utilizzo delle migrazioni, vedere [Code First a un nuovo database](xref:ef6/modeling/code-first/workflows/new-database) .

Per visualizzare il database e i dati, eseguire le operazioni seguenti:

1.  Nel menu principale di Visual Studio 2012 selezionare **Visualizza**  - &gt; **Esplora oggetti di SQL Server**.
2.  Se il database locale non è presente nell'elenco dei server, fare clic con il pulsante destro del mouse su **SQL Server** e selezionare **Aggiungi SQL Server** utilizzare l' **autenticazione di Windows** predefinita per la connessione all'istanza del database locale.
3.  Espandere il nodo del database locale
4.  Espandere la cartella **database** per visualizzare il nuovo database e passare alla tabella **Department** nota, che Code First non crea una tabella che esegue il mapping al tipo di enumerazione
5.  Per visualizzare i dati, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati** .

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato esaminato come usare i tipi enum con Entity Framework Code First. 
