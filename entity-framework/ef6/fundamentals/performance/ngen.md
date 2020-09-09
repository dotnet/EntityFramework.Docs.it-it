---
title: Miglioramento delle prestazioni di avvio con NGen-EF6
description: Miglioramento delle prestazioni di avvio con NGen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: 53b9c2147c95fe096d459ad195a0549b32b67155
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616088"
---
# <a name="improving-startup-performance-with-ngen"></a>Miglioramento delle prestazioni di avvio con NGen
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.  

Il .NET Framework supporta la generazione di immagini native per le applicazioni e le librerie gestite per aiutare le applicazioni ad avviarsi più velocemente e, in alcuni casi, l'utilizzo di una quantità inferiore di memoria. Le immagini native vengono create convertendo gli assembly di codice gestito in file contenenti istruzioni native della macchina prima dell'esecuzione dell'applicazione, evitando al compilatore .NET JIT (just-in-Time) di generare le istruzioni native in fase di esecuzione dell'applicazione.  

Prima della versione 6, le librerie principali del runtime EF facevano parte del .NET Framework e le immagini native venivano generate automaticamente. A partire dalla versione 6, l'intero runtime di EF è stato combinato nel pacchetto NuGet EntityFramework. Le immagini native devono ora essere generate usando lo strumento da riga di comando NGen.exe per ottenere risultati simili.  

Le osservazioni empiriche mostrano che le immagini native degli assembly di runtime EF possono tagliare tra 1 e 3 secondi di tempo di avvio dell'applicazione.  

## <a name="how-to-use-ngenexe"></a>Come usare NGen.exe  

La funzione più semplice dello strumento NGen.exe consiste nel "installare", ovvero creare e salvare in modo permanente su disco, le immagini native per un assembly e tutte le relative dipendenze dirette. Ecco come è possibile ottenere questo risultato:  

1. Aprire una finestra del Prompt dei comandi come amministratore.
2. Impostare la directory di lavoro corrente sul percorso degli assembly per i quali si desidera generare immagini native:

   ``` console
   cd <*Assemblies location*>  
   ```

3. A seconda del sistema operativo e della configurazione dell'applicazione, potrebbe essere necessario generare immagini native per l'architettura a 32 bit, l'architettura a 64 bit o per entrambe.

   Per l'esecuzione a 32 bit:

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   Per l'esecuzione a 64 bit:
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> La generazione di immagini native per l'architettura errata è un errore molto comune. In caso di dubbi è possibile generare semplicemente immagini native per tutte le architetture applicabili al sistema operativo installato nel computer.  

NGen.exe supporta anche altre funzioni, ad esempio la disinstallazione e la visualizzazione delle immagini native installate, l'accodamento della generazione di più immagini e così via. Per ulteriori informazioni sull'utilizzo, vedere la [ documentazioneNGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx).  

## <a name="when-to-use-ngenexe"></a>Quando usare NGen.exe  

Quando si decide quali assembly generare immagini native per in un'applicazione basata su EF versione 6 o successiva, è consigliabile prendere in considerazione le opzioni seguenti:  

- **L'assembly di runtime EF principale, EntityFramework.dll**: un'applicazione tipica basata su EF esegue una quantità significativa di codice da questo assembly all'avvio o al primo accesso al database. Di conseguenza, la creazione di immagini native di questo assembly produrrà i maggiori vantaggi nelle prestazioni di avvio.  
- **Qualsiasi assembly del provider EF usato dall'applicazione: il**tempo di avvio può anche trarre vantaggio leggermente dalla generazione di immagini native. Se, ad esempio, l'applicazione usa il provider EF per SQL Server sarà necessario generare un'immagine nativa per EntityFramework.SqlServer.dll.  
- **Assembly e altre dipendenze dell'applicazione**: la [ documentazioneNGen.exe](https://msdn.microsoft.com/library/6t9t5wcf.aspx) illustra i criteri generali per la scelta degli assembly per la generazione di immagini native per e l'effetto di immagini native sulla sicurezza, opzioni avanzate, ad esempio "binding hardware", scenari come l'uso di immagini native negli scenari di debug e di profilatura e così via.  

> [!TIP]
> Assicurarsi di misurare attentamente l'effetto dell'utilizzo di immagini native sia sulle prestazioni di avvio sia sulle prestazioni complessive dell'applicazione e di confrontarle con i requisiti effettivi. Sebbene le immagini native consentano in genere di migliorare le prestazioni di avvio e, in alcuni casi, di ridurre l'utilizzo della memoria, non tutti gli scenari beneficeranno ugualmente. Ad esempio, in caso di esecuzione di uno stato stabile (ovvero, una volta che tutti i metodi usati dall'applicazione sono stati richiamati almeno una volta), il codice generato dal compilatore JIT può produrre effettivamente prestazioni leggermente migliori rispetto alle immagini native.  

## <a name="using-ngenexe-in-a-development-machine"></a>Uso di NGen.exe in un computer di sviluppo  

Durante lo sviluppo, il compilatore JIT .NET offrirà il migliore compromesso generale per il codice che viene modificato di frequente. La generazione di immagini native per le dipendenze compilate, ad esempio gli assembly di runtime di EF, consente di accelerare lo sviluppo e il test riducendo alcuni secondi all'inizio di ogni esecuzione.  

Un posto ideale per trovare gli assembly di runtime EF è il percorso del pacchetto NuGet per la soluzione. Ad esempio, per un'applicazione che usa EF 6.0.2 con SQL Server e destinata a .NET 4,5 o versione successiva, è possibile digitare quanto segue in una finestra del prompt dei comandi (ricordarsi di aprirla come amministratore):  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> Questo sfrutta il fatto che l'installazione delle immagini native per il provider EF per SQL Server installerà anche le immagini native per l'assembly di runtime EF principale. Questa operazione funziona perché NGen.exe possibile rilevare che EntityFramework.dll è una dipendenza diretta dell'assembly EntityFramework.SqlServer.dll che si trova nella stessa directory.  

## <a name="creating-native-images-during-setup"></a>Creazione di immagini native durante l'installazione  

Il Toolkit WiX supporta l'accodamento della generazione di immagini native per gli assembly gestiti durante l'installazione, come illustrato in questa [Guida](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html). Un'altra alternativa consiste nel creare un'attività di installazione personalizzata che esegua il comando NGen.exe.  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>Verifica per verificare se le immagini native vengono usate per EF  

È possibile verificare che un'applicazione specifica usi un assembly nativo cercando gli assembly caricati con estensione ".ni.dll" o ".ni.exe". Ad esempio, un'immagine nativa per l'assembly di runtime principale di EF verrà chiamata EntityFramework.ni.dll. Un modo semplice per esaminare gli assembly .NET caricati di un processo consiste nell'usare [Esplora processi](https://technet.microsoft.com/sysinternals/bb896653).  

## <a name="other-things-to-be-aware-of"></a>Altri aspetti da tenere presente  

La **creazione di un'immagine nativa di un assembly non deve essere confusa con la registrazione dell'assembly nella [GAC (Global assembly cache)](https://msdn.microsoft.com/library/yf1d93sz.aspx)**. NGen.exe consente la creazione di immagini di assembly che non si trovano nella GAC e, in realtà, più applicazioni che usano una versione specifica di EF possono condividere la stessa immagine nativa. Sebbene Windows 8 sia in grado di creare automaticamente immagini native per gli assembly inseriti nella GAC, il runtime di EF è ottimizzato per essere distribuito insieme all'applicazione e non è consigliabile registrarlo nella GAC, perché questo ha un impatto negativo sulla risoluzione degli assembly e sulla manutenzione delle applicazioni tra gli altri aspetti.  
