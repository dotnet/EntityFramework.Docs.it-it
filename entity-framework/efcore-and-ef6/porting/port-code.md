---
title: Porting da EF6 a EF Core-porting di un modello basato sul codice-EF
description: Informazioni specifiche sul porting di un'applicazione modello basata su codice Entity Framework 6 per Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: d3920e6132576e3a93dd0ffb9dac1412b6e511aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619619"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>Porting di un modello basato su codice EF6 per EF Core

Se sono state lette tutte le avvertenze e si è pronti per la porta, di seguito sono riportate alcune linee guida utili per iniziare.

## <a name="install-ef-core-nuget-packages"></a>Installare EF Core pacchetti NuGet

Per usare EF Core, installare il pacchetto NuGet per il provider di database che si vuole usare. Ad esempio, quando la destinazione è SQL Server, è necessario installare `Microsoft.EntityFrameworkCore.SqlServer` . Per informazioni dettagliate, vedere [provider di database](xref:core/providers/index) .

Se si prevede di usare le migrazioni, è necessario installare anche il `Microsoft.EntityFrameworkCore.Tools` pacchetto.

È consigliabile lasciare installato il pacchetto NuGet EF6 (EntityFramework), perché EF Core e EF6 possono essere utilizzati side-by-side nella stessa applicazione. Tuttavia, se non si intende usare EF6 in tutte le aree dell'applicazione, la disinstallazione del pacchetto consente di ottenere errori di compilazione su frammenti di codice che richiedono attenzione.

## <a name="swap-namespaces"></a>Scambia spazi dei nomi

La maggior parte delle API usate in EF6 si trova nello `System.Data.Entity` spazio dei nomi e negli spazi dei nomi secondari correlati. La prima modifica del codice prevede lo scambio nello `Microsoft.EntityFrameworkCore` spazio dei nomi. Si inizia in genere con il file di codice del contesto derivato, quindi si esegue questa operazione, risolvendo gli errori di compilazione man mano che si verificano.

## <a name="context-configuration-connection-etc"></a>Configurazione del contesto (connessione e così via)

Come descritto in [assicurarsi che EF Core funzionerà per l'applicazione](xref:efcore-and-ef6/porting/index), EF core ha meno magie per il rilevamento del database a cui connettersi. Sarà necessario eseguire l'override del `OnConfiguring` metodo nel contesto derivato e utilizzare l'API specifica del provider di database per configurare la connessione al database.

La maggior parte delle applicazioni EF6 archivia la stringa di connessione nel file delle applicazioni `App/Web.config` . In EF Core la stringa di connessione viene letta usando l' `ConfigurationManager` API. Per poter usare questa API, potrebbe essere necessario aggiungere un riferimento all' `System.Configuration` assembly del Framework.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a>Aggiornare il codice

A questo punto, è necessario risolvere gli errori di compilazione ed esaminare il codice per verificare se le modifiche apportate al comportamento incidono sull'utente.

## <a name="existing-migrations"></a>Migrazioni esistenti

Non esiste un modo davvero possibile per trasferire le migrazioni di EF6 esistenti in EF Core.

Se possibile, è preferibile presupporre che tutte le migrazioni precedenti da EF6 siano state applicate al database e quindi avviare la migrazione dello schema da tale punto utilizzando EF Core. A tale scopo, è necessario utilizzare il `Add-Migration` comando per aggiungere una migrazione dopo che il modello viene trasferito a EF core. Si rimuoverà quindi tutto il codice dai `Up` `Down` metodi e della migrazione con impalcature. Le migrazioni successive vengono confrontate con il modello quando la migrazione iniziale è stata sottoposta a impalcatura.

## <a name="test-the-port"></a>Testare la porta

Solo perché l'applicazione viene compilata, non significa che la porta è stata eseguita correttamente per EF Core. È necessario testare tutte le aree dell'applicazione per assicurarsi che nessuna delle modifiche del comportamento abbia avuto un impatto negativo sull'applicazione.
