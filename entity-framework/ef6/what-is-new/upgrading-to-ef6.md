---
title: Aggiornamento a Entity Framework 6-EF6
description: Aggiornamento a Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: b27504e3b18c668a1c41142b9cd5e697944cb5fa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619703"
---
# <a name="upgrading-to-entity-framework-6"></a>Aggiornamento a Entity Framework 6

Nelle versioni precedenti di EF il codice è stato suddiviso tra le librerie principali (principalmente System.Data.Entity.dll) fornite come parte delle librerie .NET Framework e fuori banda (OOB) (principalmente EntityFramework.dll) fornite in un pacchetto NuGet. EF6 acquisisce il codice dalle librerie principali e le incorpora nelle librerie di OOB. Questa operazione era necessaria per consentire a EF di essere creata in modo open source e per potersi sviluppare a ritmi diversi da .NET Framework. La conseguenza è che le applicazioni dovranno essere ricompilate in base ai tipi spostati.

Questo dovrebbe essere semplice per le applicazioni che usano DbContext come fornito in EF 4,1 e versioni successive. Per le applicazioni che usano ObjectContext è necessario un po' di lavoro, ma non è ancora difficile.

Di seguito è riportato un elenco di controllo delle operazioni che è necessario eseguire per aggiornare un'applicazione esistente a EF6.

## <a name="1-install-the-ef6-nuget-package"></a>1. installare il pacchetto NuGet EF6

È necessario eseguire l'aggiornamento al nuovo runtime di Entity Framework 6.

1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet...**  
2. Nella scheda **online** selezionare **EntityFramework** e fare clic su **Installa** .  
   > [!NOTE]
   > Se è stata installata una versione precedente del pacchetto NuGet EntityFramework, questo verrà aggiornato a EF6.

In alternativa, è possibile eseguire il comando seguente dalla console di gestione pacchetti:

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. Assicurarsi che i riferimenti ad assembly System.Data.Entity.dll vengano rimossi

L'installazione del pacchetto NuGet EF6 dovrebbe rimuovere automaticamente dal progetto tutti i riferimenti a System. Data. Entity.

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. scambiare i modelli di progettazione EF (EDMX) per usare la generazione di codice EF 6. x

Se sono presenti modelli creati con la finestra di progettazione di EF, sarà necessario aggiornare i modelli di generazione del codice per generare codice compatibile con EF6.

> [!NOTE]
> Attualmente sono disponibili solo i modelli di Generatore DbContext di EF 6. x per Visual Studio 2012 e 2013.

1. Elimina i modelli di generazione del codice esistenti. Questi file saranno in genere denominati ** \<edmx_file_name\> . TT** e ** \<edmx_file_name\> . Context.tt** ed essere annidati nel file edmx in Esplora soluzioni. È possibile selezionare i modelli in Esplora soluzioni e premere il tasto **Canc** per eliminarli.  
   > [!NOTE]
   > Nei progetti di siti Web i modelli non verranno annidati nel file edmx, ma elencati insieme in Esplora soluzioni.  

   > [!NOTE]
   > Nei progetti VB.NET è necessario abilitare ' Mostra tutti i file ' per poter visualizzare i file del modello annidato.
2. Aggiungere il modello di generazione del codice EF 6. x appropriato. Aprire il modello nella finestra di progettazione EF, fare clic con il pulsante destro del mouse nell'area di progettazione e scegliere **Aggiungi elemento di generazione codice...**
    - Se si usa l'API DbContext (scelta consigliata), il **Generatore EF 6. x DbContext** sarà disponibile nella scheda **dati** .  
      > [!NOTE]
      > Se si usa Visual Studio 2012, sarà necessario installare gli strumenti di EF 6 per avere questo modello. Per informazioni dettagliate, vedere [Get Entity Framework](xref:ef6/fundamentals/install) .  

    - Se si usa l'API ObjectContext, sarà necessario selezionare la scheda **online** e cercare **Ef 6. x EntityObject Generator**.  
3. Se è stata applicata una personalizzazione ai modelli di generazione del codice, sarà necessario applicarli nuovamente ai modelli aggiornati.

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. aggiornare gli spazi dei nomi per tutti i tipi EF principali usati

Gli spazi dei nomi per i tipi DbContext e Code First non sono stati modificati. Ciò significa che per molte applicazioni che utilizzano EF 4,1 o versioni successive non è necessario modificare alcun elemento.

I tipi come ObjectContext precedentemente in System.Data.Entity.dll sono stati spostati in nuovi spazi dei nomi. Ciò significa che potrebbe essere necessario aggiornare le direttive *using* o *Import* per la compilazione con EF6.

La regola generale per le modifiche dello spazio dei nomi è che qualsiasi tipo in System. Data. * viene spostato in System. Data. Entity. Core. *. In altre parole, è sufficiente inserire **Entity. Core.** Dopo System. Data. Ad esempio:

- System. Data. EntityException => System. Data. **Entity. Core**. EntityException  
- System. Data. Objects. ObjectContext => System. Data. **Entity. Core**. Objects. ObjectContext  
- System. Data. Objects. dataclasss. RelationshipManager => System. Data. **Entity. Core**. Objects. dataclasss. RelationshipManager  

Questi tipi sono inclusi negli spazi dei nomi *principali* perché non vengono utilizzati direttamente per la maggior parte delle applicazioni basate su DbContext. Alcuni tipi che facevano parte di System.Data.Entity.dll vengono comunque usati in modo comune e diretto per le applicazioni basate su DbContext, quindi non sono stati spostati negli spazi dei nomi di *base* . Si tratta di:

- System. Data. EntityState => System. Data. **Entità**. EntityState  
- System. Data. Objects. dataclasss. EdmFunctionAttribute => System. Data. **Entity. DbFunctionAttribute**  
  > [!NOTE]
  > Questa classe è stata rinominata. una classe con il nome precedente esiste ancora e funziona, ma ora è contrassegnata come obsoleta.  
- System. Data. Objects. EntityFunctions => System. Data. **Entity. DbFunctions**  
  > [!NOTE]
  > Questa classe è stata rinominata. una classe con il nome precedente esiste ancora e funziona, ma è ora contrassegnata come obsoleta.  
- Le classi spaziali (ad esempio, DbGeography, DbGeometry) sono state spostate da System. Data. Spatial => System. Data. **Entità**. Spaziali

> [!NOTE]
> Alcuni tipi nello spazio dei nomi System. Data sono System.Data.dll che non è un assembly EF. Questi tipi non sono stati spostati, quindi gli spazi dei nomi rimangono invariati.
