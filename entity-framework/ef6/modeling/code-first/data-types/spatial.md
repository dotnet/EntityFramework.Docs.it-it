---
title: Spatial-Code First-EF6
description: Code First spaziali in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 5d18624cdcef8db3e604277aeef1c3a2d05bfcab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073964"
---
# <a name="spatial---code-first"></a>Code First spaziali
> [!NOTE]
> **EF5 solo** le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 5. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Il video e la procedura dettagliata illustrano come eseguire il mapping di tipi spaziali con Entity Framework Code First. Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.

In questa procedura dettagliata verrà utilizzato Code First per creare un nuovo database, ma è anche possibile utilizzare [Code First a un database esistente](xref:ef6/modeling/code-first/workflows/existing-database).

Il supporto del tipo spaziale è stato introdotto in Entity Framework 5. Si noti che per utilizzare le nuove funzionalità, ad esempio il tipo spaziale, le enumerazioni e le funzioni con valori di tabella, è necessario definire come destinazione .NET Framework 4,5. Per impostazione predefinita, Visual Studio 2012 è destinato a .NET 4,5.

Per utilizzare i tipi di dati spaziali, è necessario utilizzare anche un provider Entity Framework con supporto spaziale. Per ulteriori informazioni, vedere [supporto del provider per i tipi spaziali](xref:ef6/fundamentals/providers/spatial-support) .

Esistono due tipi di dati spaziali principali: geography e Geometry. Il tipo di dati geography archivia dati ellissoidali, ad esempio coordinate di latitudine e longitudine GPS. Il tipo di dati geometry rappresenta un sistema di coordinate euclideo (piano).

## <a name="watch-the-video"></a>Video
In questo video viene illustrato come eseguire il mapping di tipi spaziali con Entity Framework Code First. Viene inoltre illustrato come utilizzare una query LINQ per trovare una distanza tra due posizioni.

**Presentato da**: Julia Kornich

**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)WMV  |  [(zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)

## <a name="pre-requisites"></a>Prerequisiti

Per completare questa procedura dettagliata, è necessario che Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition sia installato.

## <a name="set-up-the-project"></a>Configurare il progetto

1.  Aprire Visual Studio 2012
2.  Scegliere **nuovo**dal menu **file** , quindi fare clic su **progetto** .
3.  Nel riquadro sinistro fare clic su **Visual C \# **e quindi selezionare il modello **console** .
4.  Immettere **SpatialCodeFirst** come nome del progetto e fare clic su **OK** .

## <a name="define-a-new-model-using-code-first"></a>Definire un nuovo modello utilizzando Code First

Quando si usa Code First lo sviluppo si inizia in genere scrivendo .NET Framework classi che definiscono il modello concettuale (dominio). Il codice seguente definisce la classe University.

L'Università ha la proprietà Location del tipo DbGeography. Per usare il tipo DbGeography, è necessario aggiungere un riferimento all'assembly System. Data. Entity e aggiungere anche l'istruzione System. Data. Spatial using.

Aprire il file Program.cs e incollare le istruzioni using seguenti all'inizio del file:

``` csharp
using System.Data.Spatial;
```

Aggiungere la definizione della classe University seguente al file Program.cs.

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
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

Si noti che, oltre all'assembly EntityFramework, viene aggiunto anche un riferimento all'assembly System. ComponentModel. DataAnnotations.

Nella parte superiore del file Program.cs aggiungere l'istruzione using seguente:

``` csharp
using System.Data.Entity;
```

In Program.cs aggiungere la definizione del contesto. 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a>Mantieni e recupera dati

Aprire il file Program.cs in cui è definito il metodo Main. Aggiungere il codice seguente nella funzione Main.

Il codice aggiunge due nuovi oggetti University al contesto. Le proprietà spaziali vengono inizializzate tramite il Metodo DbGeography. FromText. Il punto geografico rappresentato come WellKnownText viene passato al metodo. Il codice salva quindi i dati. Quindi, la query LINQ che restituisce un oggetto University in cui la posizione è più vicina alla posizione specificata viene costruita ed eseguita.

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
        {
            Name = "School of Fine Art",
            Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
        });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                        orderby u.Location.Distance(myLocation)
                        select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

Compilare l'applicazione ed eseguirla. Il programma produce l'output seguente:

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a>Visualizzare il database generato

Quando si esegue l'applicazione per la prima volta, il Entity Framework crea automaticamente un database. Poiché è installato Visual Studio 2012, il database verrà creato nell'istanza del database locale. Per impostazione predefinita, il Entity Framework denomina il database dopo il nome completo del contesto derivato (in questo esempio **SpatialCodeFirst. UniversityContext**). Le volte successive verrà utilizzato il database esistente.  

Si noti che, se si apportano modifiche al modello dopo che è stato creato il database, è necessario utilizzare Migrazioni Code First per aggiornare lo schema del database. Per un esempio di utilizzo delle migrazioni, vedere [Code First a un nuovo database](xref:ef6/modeling/code-first/workflows/new-database) .

Per visualizzare il database e i dati, eseguire le operazioni seguenti:

1.  Nel menu principale di Visual Studio 2012 selezionare **Visualizza**  - &gt; **Esplora oggetti di SQL Server**.
2.  Se il database locale non è presente nell'elenco dei server, fare clic con il pulsante destro del mouse su **SQL Server** e selezionare **Aggiungi SQL Server** utilizzare l' **autenticazione di Windows** predefinita per connettersi all'istanza del database locale.
3.  Espandere il nodo del database locale
4.  Espandere la cartella **database** per visualizzare il nuovo database e passare alla tabella delle **Università**
5.  Per visualizzare i dati, fare clic con il pulsante destro del mouse sulla tabella e selezionare **Visualizza dati** .

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato esaminato come usare i tipi spaziali con Entity Framework Code First. 
