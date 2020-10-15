---
title: Panoramica delle migrazioni - EF Core
description: Panoramica dell'uso delle migrazioni per gestire gli schemi del database con Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: eea2c32cccbb678cacaa63760c4f7d36d2d19bb1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062282"
---
# <a name="migrations-overview"></a>Panoramica delle migrazioni

Nei progetti reali i modelli di dati cambiano quando vengono implementate le funzionalità. Vengono aggiunte o rimosse nuove entità o proprietà e conseguentemente gli schemi del database devono essere modificati per essere sincronizzati con l'applicazione. La funzionalità delle migrazioni in EF Core rappresenta un metodo per l'aggiornamento incrementale dello schema del database per mantenere quest'ultimo sincronizzato con il modello di dati dell'applicazione mantenendo i dati esistenti nel database.

A livello generale, le migrazioni funzionano nel modo seguente:

* Quando viene introdotta una modifica del modello di dati, lo sviluppatore usa gli strumenti di EF Core per aggiungere una migrazione corrispondente in cui sono descritti gli aggiornamenti necessari per la sincronizzazione dello schema del database. EF Core confronta il modello corrente con uno snapshot del modello precedente per determinare le differenze e genera i file di origine della migrazione. È possibile tenere traccia dei file nel controllo del codice sorgente del progetto, come per qualsiasi altro file di origine.
* Dopo aver generato una nuova migrazione, è possibile applicarla a un database in vari modi. EF Core registra tutte le migrazioni applicate in una tabella di cronologia speciale, consentendo di individuare quali migrazioni sono state applicate e quali non sono state applicate.

La parte restante di questa pagina contiene istruzioni dettagliate per principianti sull'uso delle migrazioni. Per informazioni più dettagliate, leggere le altre pagine di questa sezione.

## <a name="getting-started"></a>Introduzione

Si supponga di aver appena completato la prima applicazione EF Core contenente il semplice modello seguente:

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Durante lo sviluppo, è possibile che siano state usate le [API di creazione ed eliminazione](xref:core/managing-schemas/ensure-created) per eseguire un'iterazione rapida, modificando il modello in base alle esigenze. Tuttavia, ora che l'applicazione è in fase di produzione, è necessario un modo per evolvere in modo sicuro lo schema senza eliminare l'intero database.

### <a name="install-the-tools"></a>Installare gli strumenti

Per prima cosa, è necessario installare gli [strumenti da riga di comando di EF Core](xref:core/miscellaneous/cli/index):

* È in genere consigliabile usare gli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/dotnet), che funzionano su tutte le piattaforme.
* Se si preferisce usare Visual Studio o si ha esperienza con le migrazioni di EF6, è anche possibile usare gli [strumenti della console di Gestione pacchetti](xref:core/miscellaneous/cli/powershell).

### <a name="create-your-first-migration"></a>Creare la prima migrazione

A questo punto si è pronti per aggiungere la prima migrazione. Chiedere a EF Core di creare una migrazione denominata **InitialCreate**:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate
```

***

EF Core creerà una directory denominata **Migrazioni** nel progetto e genererà alcuni file. È consigliabile esaminare gli elementi generati da EF Core ed eventualmente modificarli. Per il momento si ignorerà questo passaggio.

### <a name="create-your-database-and-schema"></a>Creare il database e lo schema

A questo punto EF può creare il database e lo schema dalla migrazione. È possibile eseguire questa operazione nel modo seguente:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

***

Tutto qui. L'applicazione è pronta per essere eseguita nel nuovo database e non è stato necessario scrivere una singola riga di SQL. Si noti che questo modo di applicare le migrazioni è ideale per lo sviluppo locale, ma è meno adatto per gli ambienti di produzione. Per altre informazioni, vedere la pagina [Applicazione delle migrazioni](xref:core/managing-schemas/migrations/applying).

### <a name="evolving-your-model"></a>Evoluzione del modello

Sono passati alcuni giorni e viene chiesto di aggiungere un timestamp di creazione ai blog. Sono state apportate le modifiche necessarie all'applicazione e il modello è ora simile al seguente:

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Il modello e il database di produzione non sono ora sincronizzati. È necessario aggiungere una nuova colonna allo schema del database. Verrà ora creata una nuova migrazione a tale scopo:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Si noti che alla migrazione viene assegnato un nome descrittivo in modo da facilitare poi la comprensione della cronologia del progetto.

Poiché non si tratta della prima migrazione del progetto, EF Core ora confronta il modello aggiornato con uno snapshot del modello precedente (prima che fosse aggiunta la colonna). Lo snapshot del modello è uno dei file generati da EF Core in fase di aggiunta di una migrazione e viene archiviato nel controllo del codice sorgente. In base a tale confronto, EF Core rileva che è stata aggiunta una colonna e aggiunge la migrazione appropriata.

È ora possibile applicare la migrazione come eseguito in precedenza:

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

***

Si noti che questa volta EF rileva che il database esiste già. Quando è stata applicata la prima migrazione, questa operazione è stata anche registrata in una tabella di cronologia delle migrazioni speciale all'interno del database. Si consente così a EF di applicare automaticamente solo la nuova migrazione.

### <a name="next-steps"></a>Passaggi successivi

Sopra è stata riportata solo una breve introduzione alle migrazioni. Leggere le altre pagine della documentazione per informazioni dettagliate sulla [gestione delle migrazioni](xref:core/managing-schemas/migrations/managing), sulla relativa [applicazione](xref:core/managing-schemas/migrations/applying) e su altri aspetti. Il [riferimento allo strumento dell'interfaccia della riga di comando di .NET Core](xref:core/miscellaneous/cli/index) contiene anche informazioni utili sui diversi comandi
