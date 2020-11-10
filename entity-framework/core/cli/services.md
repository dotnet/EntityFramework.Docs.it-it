---
title: Servizi in fase di progettazione-EF Core
description: Informazioni su Entity Framework Core servizi in fase di progettazione
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431571"
---
# <a name="design-time-services"></a>Servizi in fase di progettazione

Alcuni servizi utilizzati dagli strumenti vengono utilizzati solo in fase di progettazione. Questi servizi vengono gestiti separatamente dai servizi di runtime di EF Core per impedire che vengano distribuiti con l'app. Per eseguire l'override di uno di questi servizi, ad esempio il servizio per generare file di migrazione, aggiungere un'implementazione di `IDesignTimeServices` al progetto di avvio.

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>Riferimento a Microsoft. EntityFrameworkCore. Design

Microsoft. EntityFrameworkCore. Design è un pacchetto DevelopmentDependency. Ciò significa che la dipendenza non verrà propagata in modo transitivo in altri progetti e che, per impostazione predefinita, non è possibile fare riferimento ai relativi tipi.

Per fare riferimento ai relativi tipi ed eseguire l'override dei servizi in fase di progettazione, aggiornare i metadati dell'elemento PackageReference nel file di progetto.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Se al pacchetto viene fatto riferimento in modo transitivo tramite Microsoft. EntityFrameworkCore. Tools, sarà necessario aggiungere un PackageReference esplicito al pacchetto e modificarne i metadati.

## <a name="list-of-services"></a>Elenco dei servizi

Di seguito è riportato un elenco dei servizi in fase di progettazione.

Servizio                                                                              | Descrizione
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | Genera il codice per le annotazioni del modello corrispondenti.
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | Consente di generare il codice C#.
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | Parole plurali e singolari.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | Genera il codice per una migrazione.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | Classe principale per la gestione dei file di migrazione.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | Consente di creare un modello di database da un database.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | Genera il codice per un modello.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | Genera il codice di onconfigurazione.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | Classe principale per l'impalcatura di modelli decodificati.

## <a name="using-services"></a>Uso dei servizi

Questi servizi possono essere utili anche per creare strumenti personalizzati. Ad esempio, quando si vuole automatizzare parte del flusso di lavoro della fase di progettazione.

È possibile compilare un provider di servizi che contiene questi servizi usando i metodi di estensione AddEntityFrameworkDesignTimeServices e AddDbContextDesignTimeServices.

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
