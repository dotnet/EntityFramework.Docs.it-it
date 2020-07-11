---
title: Panoramica delle migrazioni-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: 8539a8da6f0051d3737efc583f0adfaf05fb2d3d
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238229"
---
# <a name="migrations-overview"></a>Cenni preliminari sulle migrazioni

Nei progetti reali, i modelli di dati cambiano quando vengono implementate le funzionalità: le nuove entità o proprietà vengono aggiunte e rimosse e gli schemi di database devono essere modificati di conseguenza per essere mantenuti sincronizzati con l'applicazione. La funzionalità delle migrazioni in EF Core rappresenta un metodo per l'aggiornamento incrementale dello schema del database per mantenere quest'ultimo sincronizzato con il modello di dati dell'applicazione mantenendo i dati esistenti nel database.

A un livello elevato, le migrazioni funzionano nel modo seguente:

* Quando viene introdotta una modifica del modello di dati, lo sviluppatore utilizza EF Core strumenti per aggiungere una migrazione corrispondente che descrive gli aggiornamenti necessari per la sincronizzazione dello schema del database. EF Core confronta il modello corrente con uno snapshot del modello precedente per determinare le differenze e genera i file di origine della migrazione. è possibile tenere traccia dei file nel controllo del codice sorgente del progetto, come qualsiasi altro file di origine.
* Una volta generata una nuova migrazione, è possibile applicarla a un database in vari modi. EF Core registra tutte le migrazioni applicate in una tabella di cronologia speciale, consentendo di individuare quali migrazioni sono state applicate e quali non sono state applicate.

Il resto di questa pagina è una guida dettagliata per principianti per l'uso delle migrazioni. Consultare le altre pagine di questa sezione per informazioni più dettagliate.

## <a name="getting-started"></a>Introduzione

Si supponga di aver appena completato la prima applicazione EF Core, che contiene il modello semplice seguente:

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Durante lo sviluppo, è possibile che siano state utilizzate le [API Create e drop](xref:core/managing-schemas/ensure-created) per eseguire un'iterazione rapida, modificando il modello in base alle esigenze. Tuttavia, ora che l'applicazione è in fase di produzione, è necessario un modo per evolvere in modo sicuro lo schema senza eliminare l'intero database.

### <a name="install-the-tools"></a>Installare gli strumenti

Prima di tutto, è necessario installare gli [strumenti da riga di comando EF Core](xref:core/miscellaneous/cli/index):

* È in genere consigliabile usare gli [strumenti di interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet), che funzionano su tutte le piattaforme.
* Se si ha familiarità con l'uso di Visual Studio o si ha esperienza con le migrazioni di EF6, è anche possibile usare gli [strumenti della console di gestione pacchetti](xref:core/miscellaneous/cli/powershell).

### <a name="create-your-first-migration"></a>Creazione della prima migrazione

A questo punto si è pronti per aggiungere la prima migrazione. Indicare EF Core creare una migrazione denominata **InitialCreate**:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

EF Core creerà una directory denominata **Migrations** nel progetto e genererà alcuni file. È consigliabile esaminare le EF Core generate ed eventualmente modificarle, ma per il momento si ignorerà questo aspetto.

### <a name="create-your-database-and-schema"></a>Creare il database e lo schema

A questo punto è possibile fare in modo che EF crei il database e crei lo schema dalla migrazione. Questa operazione può essere eseguita tramite il codice seguente:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Questo è tutto qui: l'applicazione è pronta per essere eseguita nel nuovo database e non è stato necessario scrivere una singola riga di SQL. Si noti che questo modo di applicare le migrazioni è ideale per lo sviluppo locale, ma è meno adatto per gli ambienti di produzione. per altre informazioni, vedere la [pagina applicazione delle migrazioni](xref:core/managing-schemas/migrations/applying) .

### <a name="evolving-your-model"></a>Evoluzione del modello

Sono stati superati alcuni giorni e viene chiesto di aggiungere un timestamp di creazione ai Blog. Sono state apportate le modifiche necessarie all'applicazione e il modello è ora simile al seguente:

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Il modello e il database di produzione sono ora non sincronizzati. è necessario aggiungere una nuova colonna allo schema del database. Verrà ora creata una nuova migrazione:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Si noti che viene fornito un nome descrittivo per la migrazione, per semplificare la comprensione della cronologia del progetto in un secondo momento.

Poiché non si tratta della prima migrazione del progetto, EF Core ora confronta il modello aggiornato con uno snapshot del vecchio modello, prima dell'aggiunta della colonna; lo snapshot del modello è uno dei file generati da EF Core quando si aggiunge una migrazione e viene archiviato nel controllo del codice sorgente. In base a tale confronto, EF Core rileva che è stata aggiunta una colonna e aggiunge la migrazione appropriata.

È ora possibile applicare la migrazione come prima:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Si noti che questa volta, EF rileva che il database esiste già. Inoltre, quando è stata applicata la prima migrazione, questo fact è stato registrato in una tabella di cronologia delle migrazioni speciale nel database. Questo consente a EF di applicare automaticamente solo la nuova migrazione.

### <a name="next-steps"></a>Passaggi successivi

Sopra è stata solo una breve introduzione alle migrazioni. Per ulteriori informazioni sulla [gestione delle migrazioni](xref:core/managing-schemas/migrations/managing), sull' [applicazione](xref:core/managing-schemas/migrations/applying)e su altri aspetti, consultare le altre pagine della documentazione. Il [riferimento allo strumento interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/index) contiene anche informazioni utili sui diversi comandi
