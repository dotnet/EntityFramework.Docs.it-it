---
title: Provider di Entity Framework - EF6
description: Provider di Entity Framework in Entity Framework 6
author: divega
ms.date: 06/27/2018
ms.assetid: 7BFB7763-CD6C-4520-93A2-7B265F5FA586
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: 5140fefeb118b6e70ac27ab010d813d03ec7000c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615876"
---
# <a name="entity-framework-6-providers"></a>Provider di Entity Framework 6
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Entity Framework viene ora sviluppato in base a una licenza open source e EF6 e versioni successive non faranno parte di .NET Framework. Questa novità offre molti vantaggi ma comporta anche la necessità di ricompilare i provider di Entity Framework in base agli assembly di EF6. I provider di Entity Framework per EF5 e versioni precedenti funzioneranno infatti con EF6 solo dopo che saranno stati ricompilati.

## <a name="which-providers-are-available-for-ef6"></a>Quali provider sono disponibili per EF6?

In base alle informazioni attuali, i provider ricompilati per EF6 includono:

*   **Provider Microsoft SQL Server**
    *   Compilato dalla [codebase open source di Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Incluso nel [pacchetto NuGet EntityFramework](https://nuget.org/packages/EntityFramework)
*   **Provider Microsoft SQL Server Compact Edition**
    *   Compilato dalla [codebase open source di Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Incluso nel [pacchetto NuGet EntityFramework.SqlServerCompact](https://nuget.org/packages/EntityFramework.SqlServerCompact)
*   [**Provider di dati Devart dotConnect**](https://www.devart.com/dotconnect/)
    *   Sono disponibili provider di terze parti di [Devart](https://www.devart.com/) per un'ampia gamma di database tra cui Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2 e SQL Server
*   [**Provider CData Software**](https://www.cdata.com/ado/)
    *   Sono disponibili provider di terze parti di [CData Software](https://www.cdata.com/ado/) per un'ampia gamma di archivi dati tra cui Salesforce, Archiviazione tabelle di Azure, MySql e molti altri
*   **Provider Firebird**
    *   Disponibile come [pacchetto NuGet](https://www.nuget.org/packages/EntityFramework.Firebird/)
*   **Provider Visual Fox Pro**
    *   Disponibile come [pacchetto NuGet](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)
*   **MySQL**
    *   [MySQL Connector/NET for Entity Framework](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   **PostgreSQL**
    *   Npgsql è disponibile come [pacchetto NuGet](https://www.nuget.org/packages/EntityFramework6.Npgsql/)
*   **Oracle**
    *   ODP.NET è disponibile come [pacchetto NuGet](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)

Tenere presente che l'inclusione in questo elenco non indica il livello di funzionalità o supporto per un determinato provider, ma solo che è stata resa disponibile una build per EF6.

## <a name="registering-ef-providers"></a>Registrazione dei provider di Entity Framework

A partire da Entity Framework 6, è possibile registrare i provider di Entity Framework usando la configurazione basata su codice o nel file di configurazione dell'applicazione.

### <a name="config-file-registration"></a>Registrazione nel file di configurazione

La registrazione del provider di Entity Framework in app.config o web.config ha il formato seguente:


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

Tenere presente che se il provider di Entity Framework viene installato da NuGet, il pacchetto NuGet spesso aggiungerà automaticamente la registrazione al file di configurazione. Se il pacchetto NuGet viene installato in un progetto che non è il progetto di avvio per l'app, potrebbe essere necessario copiare la registrazione nel file di configurazione per il progetto di avvio.

"invariantName" in questa registrazione è lo stesso nome invariante usato per identificare un provider ADO.NET. È indicato come attributo "invariant" in una registrazione DbProviderFactories e come attributo "providerName" nella registrazione di una stringa di connessione. Il nome invariante da usare deve essere incluso anche nella documentazione del provider. Esempi di nomi invarianti sono "System.Data.SqlClient" per SQL Server e "System.Data.SqlServerCe.4.0" per SQL Server Compact.

Il "tipo" in questa registrazione è il nome qualificato dall'assembly del tipo di provider che deriva da "System.Data.Entity.Core.Common.DbProviderServices". La stringa da usare per SQL Compact, ad esempio, è "System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact". Il tipo da usare qui deve essere incluso nella documentazione del provider.

### <a name="code-based-registration"></a>Registrazione basata su codice

A partire da Entity Framework 6, la configurazione a livello di applicazione per Entity Framework può essere specificata nel codice. Per i dettagli completi, vedere _[Entity Framework - configurazione basata su codice](https://msdn.microsoft.com/data/jj680699)_. La normale modalità di registrazione di un provider di Entity Framework tramite la configurazione basata su codice consiste nel creare una nuova classe che deriva da System.Data.Entity.DbConfiguration e inserirla nello stesso assembly della classe DbContext. La classe DbConfiguration registrerà quindi il provider nel proprio costruttore. Ad esempio, per la registrazione del provider SQL Compact, la classe DbConfiguration ha questo aspetto:

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

In questo codice "SqlCeProviderServices.ProviderInvariantName" è un elemento utile per la stringa del nome invariante del provider SQL Server Compact ("System.Data.SqlServerCe.4.0") e SqlCeProviderServices.Instance restituisce l'istanza singleton del provider di Entity Framework per SQL Compact.

## <a name="what-if-the-provider-i-need-isnt-available"></a>Cosa si può fare se si ha necessità di un provider che non è disponibile?

Se il provider è disponibile per le versioni precedenti di Entity Framework, contattare il proprietario del provider e chiedere di creare una versione per Entity Framework 6. Includere un riferimento alla [documentazione del modello di provider di Entity Framework 6](xref:ef6/fundamentals/providers/provider-model).

## <a name="can-i-write-a-provider-myself"></a>Si può scrivere autonomamente un provider?

È certamente possibile creare autonomamente un provider di Entity Framework, anche se si tratta di un'operazione tutt'altro che banale. Il collegamento sopra riportato relativo al modello di provider di Entity Framework 6 è un buon punto di partenza. Può anche essere utile usare il codice per il provider SQL Server e SQL CE incluso nella [codebase open source di Entity Framework](https://github.com/aspnet/EntityFramework6) come punto di partenza o come riferimento.

Si noti che a partire da EF6, il collegamento tra il provider di Entity Framework e il provider ADO.NET sottostante è meno rigido. Ciò semplifica la scrittura di un provider di Entity Framework poiché evita la necessità di scrivere le classi ADO.NET o di eseguirne il wrapping.
