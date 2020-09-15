---
title: Versioni di Visual Studio-EF6
description: Versioni di Visual Studio e Entity Framework 6
author: divega
ms.date: 07/05/2018
uid: ef6/what-is-new/visual-studio
ms.openlocfilehash: 90982c49f47c8967cf3a3ed91fa51a392d94636f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073630"
---
# <a name="visual-studio-releases"></a>Versioni di Visual Studio

È consigliabile usare sempre la versione più recente di Visual Studio perché contiene gli strumenti più recenti per .NET, NuGet e Entity Framework.
Di fatto, i vari esempi e procedure dettagliate nella documentazione di Entity Framework presuppongono che si stia usando una versione recente di Visual Studio.

È tuttavia possibile usare versioni precedenti di Visual Studio con versioni diverse di Entity Framework purché si tengano conto di alcune differenze:

## <a name="visual-studio-2017-157-and-newer"></a>Visual Studio 2017 15,7 e versioni successive

- Questa versione di Visual Studio include la versione più recente di Entity Framework Tools e il runtime di EF 6,2 e non richiede passaggi di configurazione aggiuntivi.
Per ulteriori informazioni su queste versioni, [vedere novità.](xref:ef6/what-is-new/index)
- L'aggiunta di Entity Framework a nuovi progetti tramite gli strumenti EF aggiungerà automaticamente il pacchetto NuGet EF 6,2.
È possibile eseguire manualmente l'installazione o l'aggiornamento a qualsiasi pacchetto NuGet EF disponibile online.
- Per impostazione predefinita, l'istanza di SQL Server disponibile con questa versione di Visual Studio è un'istanza del database locale denominata MSSQLLocalDB.
La sezione Server della stringa di connessione da usare è "(local DB) \\ MSSQLLocalDB".
Ricordarsi di usare una stringa Verbatim con prefisso `@` o due barre rovesciate " \\ \\ " quando si specifica una stringa di connessione nel codice C#.  


## <a name="visual-studio-2015-to-visual-studio-2017-156"></a>Visual Studio 2015 a Visual Studio 2017 15,6

- Queste versioni di Visual Studio includono Entity Framework Tools e 6.1.3 di Runtime.
Per ulteriori informazioni su queste versioni, vedere le [versioni precedenti](xref:ef6/what-is-new/past-releases#ef-613) .
- L'aggiunta di Entity Framework a nuovi progetti tramite gli strumenti EF aggiungerà automaticamente il pacchetto NuGet 6.1.3 di EF.
È possibile eseguire manualmente l'installazione o l'aggiornamento a qualsiasi pacchetto NuGet EF disponibile online.
- Per impostazione predefinita, l'istanza di SQL Server disponibile con questa versione di Visual Studio è un'istanza del database locale denominata MSSQLLocalDB.
La sezione Server della stringa di connessione da usare è "(local DB) \\ MSSQLLocalDB".
Ricordarsi di usare una stringa Verbatim con prefisso `@` o due barre rovesciate " \\ \\ " quando si specifica una stringa di connessione nel codice C#.  


## <a name="visual-studio-2013"></a>Visual Studio 2013
- Questa versione di Visual Studio include e la versione precedente di Entity Framework strumenti e Runtime.
Si consiglia di eseguire l'aggiornamento a Entity Framework Tools 6.1.3, usando [il programma di installazione](https://www.microsoft.com/download/details.aspx?id=40762) disponibile nell'area download Microsoft.
Per ulteriori informazioni su queste versioni, vedere le [versioni precedenti](xref:ef6/what-is-new/past-releases#ef-613) .
- Aggiungendo Entity Framework ai nuovi progetti usando gli strumenti EF aggiornati, verrà aggiunto automaticamente il pacchetto NuGet EF 6.1.3.
È possibile eseguire manualmente l'installazione o l'aggiornamento a qualsiasi pacchetto NuGet EF disponibile online.
- Per impostazione predefinita, l'istanza di SQL Server disponibile con questa versione di Visual Studio è un'istanza del database locale denominata MSSQLLocalDB.
La sezione Server della stringa di connessione da usare è "(local DB) \\ MSSQLLocalDB".
Ricordarsi di usare una stringa Verbatim con prefisso `@` o due barre rovesciate " \\ \\ " quando si specifica una stringa di connessione nel codice C#.  

## <a name="visual-studio-2012"></a>Visual Studio 2012

- Questa versione di Visual Studio include e la versione precedente di Entity Framework strumenti e Runtime.
Si consiglia di eseguire l'aggiornamento a Entity Framework Tools 6.1.3, usando [il programma di installazione](https://www.microsoft.com/download/details.aspx?id=40762) disponibile nell'area download Microsoft.
Per ulteriori informazioni su queste versioni, vedere le [versioni precedenti](xref:ef6/what-is-new/past-releases#ef-613) .
- Aggiungendo Entity Framework ai nuovi progetti usando gli strumenti EF aggiornati, verrà aggiunto automaticamente il pacchetto NuGet EF 6.1.3.
È possibile eseguire manualmente l'installazione o l'aggiornamento a qualsiasi pacchetto NuGet EF disponibile online.
- Per impostazione predefinita, l'istanza di SQL Server disponibile con questa versione di Visual Studio è un'istanza del database locale denominata v 11.0.
La sezione Server della stringa di connessione da usare è "(local DB) \\ v 11.0".
Ricordarsi di usare una stringa Verbatim con prefisso `@` o due barre rovesciate " \\ \\ " quando si specifica una stringa di connessione nel codice C#.  

## <a name="visual-studio-2010"></a>Visual Studio 2010

- La versione di Entity Framework Tools disponibile con questa versione di Visual Studio non è compatibile con il runtime di Entity Framework 6 e non può essere aggiornata.
- Per impostazione predefinita, gli strumenti di Entity Framework aggiungono Entity Framework 4,0 ai progetti.
Per creare applicazioni con le versioni più recenti di Entity Framework, è necessario installare prima di tutto l' [estensione Gestione pacchetti NuGet](https://marketplace.visualstudio.com/items?itemName=NuGetTeam.NuGetPackageManager).
- Per impostazione predefinita, tutta la generazione di codice nella versione degli strumenti EF si basa su EntityObject e Entity Framework 4.
È consigliabile cambiare la generazione del codice in base a DbContext e Entity Framework 5, installando i modelli di generazione del codice DbContext per [C#](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforC) o [Visual Basic](https://marketplace.visualstudio.com/items?itemName=EntityFrameworkTeam.EF5xDbContextGeneratorforVBNET).
- Dopo aver installato le estensioni di gestione pacchetti NuGet, è possibile installare manualmente o eseguire l'aggiornamento a qualsiasi pacchetto NuGet EF disponibile online e usare EF6 con Code First, che non richiede una finestra di progettazione.
- Per impostazione predefinita, l'istanza di SQL Server disponibile con questa versione di Visual Studio è SQL Server Express denominata SQLExpress.
La sezione Server della stringa di connessione da usare è ". \\ SQLEXPRESS ".
Ricordarsi di usare una stringa Verbatim con prefisso `@` o due barre rovesciate " \\ \\ " quando si specifica una stringa di connessione nel codice C#.
