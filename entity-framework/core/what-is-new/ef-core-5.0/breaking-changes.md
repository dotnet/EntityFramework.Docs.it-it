---
title: Modifiche di rilievo in EF Core 5,0-EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666195"
---
# <a name="breaking-changes-in-ef-core-50"></a>Modifiche di rilievo nella EF Core 5,0

Le modifiche alle API e al comportamento seguenti hanno il rischio di interrompere l'aggiornamento delle applicazioni esistenti per EF Core 5.0.0.

## <a name="summary"></a>Summary

| **Modifica di rilievo**                                                                                                               | **Impatto** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | Basso        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Metodo HasGeometricDimension rimosso dall'estensione SQLite NTS

[Rilevamento del problema #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Comportamento precedente**

HasGeometricDimension è stato usato per abilitare dimensioni aggiuntive (Z e M) sulle colonne Geometry. Tuttavia, la creazione del database ha avuto solo effetto. Non era necessario specificarlo per eseguire query sui valori con dimensioni aggiuntive. Non funziona anche correttamente quando si inseriscono o si aggiornano valori con dimensioni aggiuntive ([vedere #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Nuovo comportamento**

Per consentire l'inserimento e l'aggiornamento dei valori di geometria con dimensioni aggiuntive (Z e M), è necessario specificare la dimensione come parte del nome del tipo di colonna. Questo corrisponde maggiormente al comportamento sottostante della funzione AddGeometryColumn di SpatiaLite.

**Perché**

L'utilizzo di HasGeometricDimension dopo aver specificato la dimensione nel tipo di colonna è superfluo e ridondante, quindi è stato rimosso completamente HasGeometricDimension.

**Soluzioni di prevenzione**

Utilizzare `HasColumnType` per specificare la dimensione:

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
