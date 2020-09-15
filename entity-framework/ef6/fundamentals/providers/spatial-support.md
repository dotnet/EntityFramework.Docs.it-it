---
title: Supporto del provider per i tipi spaziali-EF6
description: Supporto del provider per i tipi spaziali in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: d6214ec503af8ea02b586cfddfd958ec7b47e42f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070445"
---
# <a name="provider-support-for-spatial-types"></a>Supporto del provider per i tipi spaziali
Entity Framework supporta l'utilizzo di dati spaziali tramite le classi DbGeography o DbGeometry. Queste classi si basano sulle funzionalità specifiche del database offerte dal provider Entity Framework. Non tutti i provider supportano i dati spaziali e quelli che possono avere prerequisiti aggiuntivi, ad esempio l'installazione di assembly di tipo spaziale. Altre informazioni sul supporto dei provider per i tipi spaziali sono disponibili di seguito.  

Altre informazioni su come usare i tipi spaziali in un'applicazione sono disponibili in due procedure dettagliate, una per Code First, l'altra per Database First o Model First:  

- [Tipi di dati spaziali in Code First](xref:ef6/modeling/code-first/data-types/spatial)  
- [Tipi di dati spaziali in EF designer](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a>Versioni EF che supportano i tipi spaziali  

Il supporto per i tipi spaziali è stato introdotto in EF5. Tuttavia, nei tipi spaziali EF5 sono supportati solo quando l'applicazione è destinata ed è in esecuzione in .NET 4,5.  

A partire da i tipi spaziali EF6 sono supportati per le applicazioni destinate sia a .NET 4 che a .NET 4,5.  

## <a name="ef-providers-that-support-spatial-types"></a>Provider EF che supportano i tipi spaziali  

### <a name="ef5"></a>EF5  

I provider Entity Framework per EF5 che sono in grado di supportare i tipi spaziali sono:  

- Provider Microsoft SQL Server  
    - Questo provider viene fornito come parte di EF5.  
    - Questo provider dipende da alcune librerie di basso livello aggiuntive che potrebbero dover essere installate. per informazioni dettagliate, vedere di seguito.  
- [Devart dotConnect per Oracle](https://www.devart.com/dotconnect/oracle/)  
    - Si tratta di un provider di terze parti di Devart.  

Se si è a conoscenza di un provider EF5 che supporta i tipi spaziali, contattare il contatto e sarà possibile aggiungerlo a questo elenco.  

### <a name="ef6"></a>EF6  

I provider Entity Framework per EF6 che sono in grado di supportare i tipi spaziali sono:  

- Provider Microsoft SQL Server  
    - Questo provider viene fornito come parte di EF6.  
    - Questo provider dipende da alcune librerie di basso livello aggiuntive che potrebbero dover essere installate. per informazioni dettagliate, vedere di seguito.  
- [Devart dotConnect per Oracle](https://www.devart.com/dotconnect/oracle/)  
    - Si tratta di un provider di terze parti di Devart.  

Se si è a conoscenza di un provider EF6 che supporta i tipi spaziali, contattare il contatto e sarà possibile aggiungerlo a questo elenco.  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>Prerequisiti per i tipi spaziali con Microsoft SQL Server  

SQL Server supporto spaziale dipende dai tipi SqlGeography e SqlGeometry di basso livello SQL Server specifici. Questi tipi risiedono in Microsoft.SqlServer.Types.dll assembly e questo assembly non viene fornito come parte di EF o come parte del .NET Framework.  

Quando si installa Visual Studio, viene spesso installata anche una versione di SQL Server, che includerà l'installazione del Microsoft.SqlServer.Types.dll.  

Se SQL Server non è installato nel computer in cui si desidera utilizzare tipi spaziali o se i tipi spaziali sono stati esclusi dall'installazione SQL Server, sarà necessario installarli manualmente. I tipi possono essere installati utilizzando `SQLSysClrTypes.msi` , che fa parte di Microsoft SQL Server Feature Pack. I tipi spaziali sono SQL Server specifici della versione, quindi è consigliabile [cercare "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) nell'area download Microsoft, quindi selezionare e scaricare l'opzione che corrisponde alla versione di SQL Server che verrà usata.
