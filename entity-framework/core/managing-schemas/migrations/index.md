---
title: Panoramica delle migrazioni - EF Core
description: Panoramica dell'uso delle migrazioni per gestire gli schemi del database con Entity Framework Core
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "95003395"
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

Per prima cosa, è necessario installare gli [strumenti da riga di comando di EF Core](xref:core/cli/index):

* È in genere consigliabile usare gli [strumenti dell'interfaccia della riga di comando di .NET Core](xref:core/cli/dotnet), che funzionano su tutte le piattaforme.
* Se si preferisce usare Visual Studio o si ha esperienza con le migrazioni di EF6, è anche possibile usare gli [strumenti della console di Gestione pacchetti](xref:core/cli/powershell).

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

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[Interfaccia della riga di comando di .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

***

Si noti che questa volta EF rileva che il database esiste già. Quando è stata applicata la prima migrazione, questa operazione è stata anche registrata in una tabella di cronologia delle migrazioni speciale all'interno del database. Si consente così a EF di applicare automaticamente solo la nuova migrazione.

### <a name="excluding-parts-of-your-model"></a>Esclusione di parti del modello

> [!NOTE]
> Questa funzionalità è stata introdotta in EF in Core 5,0.

In alcuni casi può essere utile fare riferimento ai tipi da un altro DbContext. Questo può causare conflitti di migrazione. Per evitare questo problema, escludere il tipo dalle migrazioni di uno dei oggetti DbContext.

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a>Passaggi successivi

Sopra è stata riportata solo una breve introduzione alle migrazioni. Leggere le altre pagine della documentazione per informazioni dettagliate sulla [gestione delle migrazioni](xref:core/managing-schemas/migrations/managing), sulla relativa [applicazione](xref:core/managing-schemas/migrations/applying) e su altri aspetti. Il [riferimento allo strumento dell'interfaccia della riga di comando di .NET Core](xref:core/cli/index) contiene anche informazioni utili sui diversi comandi

## <a name="additional-resources"></a>Risorse aggiuntive

* [EF Core sessione standup della community](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) passa alle nuove funzionalità di migrazione in EF Core 5,0.
