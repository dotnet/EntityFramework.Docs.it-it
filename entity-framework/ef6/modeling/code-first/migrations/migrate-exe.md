---
title: Uso di migrate.exe-EF6
description: Utilizzo di migrate.exe in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/migrate-exe
ms.openlocfilehash: 421b0a186afabdaa225816e0e5967633a95de959
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072174"
---
# <a name="using-migrateexe"></a>Utilizzo di migrate.exe
Migrazioni Code First può essere utilizzato per aggiornare un database dall'interno di Visual Studio, ma può essere eseguito anche tramite lo strumento da riga di comando migrate.exe. Questa pagina fornirà una rapida panoramica su come usare migrate.exe per eseguire le migrazioni in un database.

> [!NOTE]
> Questo articolo presuppone che l'utente sappia come usare Migrazioni Code First negli scenari di base. In caso contrario, sarà necessario leggere [migrazioni Code First](xref:ef6/modeling/code-first/migrations/index) prima di continuare.

## <a name="copy-migrateexe"></a>Copia migrate.exe

Quando si installa Entity Framework usando NuGet migrate.exe si troverà all'interno della cartella Tools del pacchetto scaricato. Nella &lt; cartella del progetto &gt; \\ pacchetti \\ EntityFramework. &lt; strumenti della versione &gt; \\

Una volta migrate.exe, è necessario copiarlo nel percorso dell'assembly che contiene le migrazioni.

Se l'applicazione è destinata a .NET 4 e non a 4,5, sarà necessario copiare anche il **Redirect.config** nel percorso e rinominarlo **migrate.exe.config**. In questo modo migrate.exe ottiene i reindirizzamenti di binding corretti per poter individuare l'assembly del Entity Framework.

| .NET 4.5                                      | .NET 4,0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![File .NET 4,5](~/ef6/media/net45files.png) | ![File .NET 4,0](~/ef6/media/net40files.png) |

> [!NOTE]
> migrate.exe non supporta gli assembly x64.

Dopo aver spostato migrate.exe nella cartella corretta, sarà possibile usarlo per eseguire le migrazioni sul database. Tutte le utilità sono progettate per eseguire le migrazioni. Non è possibile generare migrazioni o creare uno script SQL.

## <a name="see-options"></a>Vedere le opzioni

``` console
Migrate.exe /?
```

Sopra verrà visualizzata la pagina della Guida associata a questa utilità, si noti che è necessario che il EntityFramework.dll sia nello stesso percorso in cui si esegue migrate.exe per poter funzionare.

## <a name="migrate-to-the-latest-migration"></a>Eseguire la migrazione alla migrazione più recente

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

Quando si esegue migrate.exe l'unico parametro obbligatorio è l'assembly, ovvero l'assembly contenente le migrazioni che si sta tentando di eseguire, ma verranno utilizzate tutte le impostazioni basate sulla convenzione se non si specifica il file di configurazione.

## <a name="migrate-to-a-specific-migration"></a>Eseguire la migrazione a una migrazione specifica

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

Se si desidera eseguire le migrazioni fino a una migrazione specifica, è possibile specificare il nome della migrazione. Questa operazione consente di eseguire tutte le migrazioni precedenti, se necessario, fino alla migrazione specificata.

## <a name="specify-working-directory"></a>Specifica directory di lavoro

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

Se l'assembly ha dipendenze o legge i file relativi alla directory di lavoro, sarà necessario impostare startupDirectory.

## <a name="specify-migration-configuration-to-use"></a>Specificare la configurazione della migrazione da usare

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

Se sono presenti più classi di configurazione della migrazione, le classi che ereditano da DbMigrationConfiguration, è necessario specificare che deve essere usato per questa esecuzione. Questa impostazione viene specificata fornendo il secondo parametro facoltativo senza un'opzione come sopra.

## <a name="provide-connection-string"></a>Specificare la stringa di connessione

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

Se si desidera specificare una stringa di connessione dalla riga di comando, è necessario fornire anche il nome del provider. Se non si specifica il nome del provider, verrà generata un'eccezione.

## <a name="common-problems"></a>Problemi comuni

| Messaggio di errore                                                                                                                                                                                                                                                                                                                      | Soluzione                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Eccezione non gestita: System. IO. FileLoadException: Impossibile caricare il file o l'assembly ' EntityFramework, Version = 5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089' o una delle relative dipendenze. La definizione del manifesto dell'assembly individuato non corrisponde al riferimento all'assembly. (Eccezione da HRESULT: 0x80131040)         | Ciò significa in genere che si esegue un'applicazione .NET 4 senza il file di Redirect.config. È necessario copiare il Redirect.config nello stesso percorso migrate.exe e rinominarlo in migrate.exe.config.                                                                                       |
| Eccezione non gestita: System. IO. FileLoadException: Impossibile caricare il file o l'assembly ' EntityFramework, Version = 4.4.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089' o una delle relative dipendenze. La definizione del manifesto dell'assembly individuato non corrisponde al riferimento all'assembly. (Eccezione da HRESULT: 0x80131040)          | Questa eccezione indica che è in esecuzione un'applicazione .NET 4,5 con la Redirect.config copiata nel percorso migrate.exe. Se l'app è .NET 4,5, non è necessario avere il file di configurazione con i reindirizzamenti all'interno di. Eliminare il file di migrate.exe.config.                                    |
| ERRORE: non è possibile aggiornare il database in modo che corrisponda al modello corrente perché sono presenti modifiche in sospeso e la migrazione automatica è disabilitata. Scrivere le modifiche al modello in sospeso in una migrazione basata su codice o abilitare la migrazione automatica. Impostare DbMigrationsConfiguration. AutomaticMigrationsEnabled su true per abilitare la migrazione automatica. | Questo errore si verifica se si esegue la migrazione quando non è stata creata una migrazione per gestire le modifiche apportate al modello e il database non corrisponde al modello. L'aggiunta di una proprietà a una classe di modello e l'esecuzione migrate.exe senza creare una migrazione per aggiornare il database è un esempio. |
| ERRORE: il tipo non è stato risolto per il membro ' System. Data. Entity. Migrations. Design. ToolingFacade + UpdateRunner, EntityFramework, Version = 5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089'.                                                                                                                                       | Questo errore può essere causato dalla specifica di una directory di avvio non corretta. Deve essere il percorso di migrate.exe                                                                                                                                                                                      |
| Eccezione non gestita: System. NullReferenceException: riferimento all'oggetto non impostato su un'istanza di un oggetto. <br/>   in System. Data. Entity. Migrations. Console. Program. Main (String [] args)                                                                                                                                             | Questo problema può essere causato dalla mancata specificazione di un parametro obbligatorio per uno scenario in uso. Ad esempio specificando una stringa di connessione senza specificare il nome del provider.                                                                                                                        |
| ERRORE: nell'assembly ' ClassLibrary1' è stato trovato più di un tipo di configurazione delle migrazioni. Specificare il nome di quello da usare.                                                                                                                                                                                                  | Come indica l'errore, nell'assembly specificato sono presenti più classi di configurazione. È necessario usare l'opzione/configurationType per specificare quale usare.                                                                                                                                           |
| ERRORE: Impossibile caricare il file o l'assembly ' &lt; AssemblyName &gt; ' o una delle relative dipendenze. Il nome o la codebase dell'assembly specificato non è valido. (Eccezione da HRESULT: 0x80131047)                                                                                                                                                    | Questa situazione può essere causata dalla specifica di un nome di assembly in modo errato o senza                                                                                                                                                                                                                          |
| ERRORE: Impossibile caricare il file o l'assembly ' &lt; AssemblyName &gt; ' o una delle relative dipendenze. Tentativo di caricare un programma con un formato non corretto.                                                                                                                                                                          | Questo errore si verifica se si tenta di eseguire migrate.exe su un'applicazione x64. EF 5,0 e versioni precedenti funzioneranno solo su x86.                                                                                                                                                                                |
