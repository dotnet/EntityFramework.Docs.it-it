---
title: Definizione di DbSet-EF6
description: Definizione di DbSet in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: dc5ad7f8b4ba32454c702f354b37223007e856e3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065225"
---
# <a name="defining-dbsets"></a>Definizione di DbSet
Quando si sviluppa con il flusso di lavoro Code First si definisce un DbContext derivato che rappresenta la sessione con il database ed espone un DbSet per ogni tipo nel modello. In questo argomento vengono illustrati i vari modi in cui è possibile definire le proprietà DbSet.  

## <a name="dbcontext-with-dbset-properties"></a>DbContext con proprietà DbSet  

Il caso comune illustrato in Code First esempi è avere un DbContext con proprietà DbSet automatiche pubbliche per i tipi di entità del modello. Ad esempio:  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Se usato in modalità Code First, in questo modo vengono configurati i Blog e i post come tipi di entità, nonché la configurazione di altri tipi raggiungibili da questi. Inoltre, DbContext chiamerà automaticamente il setter per ognuna di queste proprietà per impostare un'istanza del DbSet appropriato.  

## <a name="dbcontext-with-idbset-properties"></a>DbContext con proprietà IDbSet  

In alcune situazioni, ad esempio durante la creazione di simulazioni o fake, è più utile dichiarare le proprietà del set usando un'interfaccia. In questi casi è possibile usare l'interfaccia IDbSet al posto di DbSet. Ad esempio:  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

Questo contesto funziona esattamente allo stesso modo del contesto che usa la classe DbSet per le proprietà impostate.  

## <a name="dbcontext-with-read-only-set-properties"></a>DbContext con proprietà set di sola lettura  

Se non si vuole esporre Setter pubblici per le proprietà DbSet o IDbSet, è possibile creare le proprietà di sola lettura e creare le istanze del set manualmente. Ad esempio:  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

Si noti che DbContext memorizza nella cache l'istanza di DbSet restituita dal metodo set, in modo che ognuna di queste proprietà restituisca la stessa istanza ogni volta che viene chiamato.  

L'individuazione dei tipi di entità per Code First funziona nello stesso modo in cui avviene per le proprietà con Getter e setter pubblici.  
