---
title: Uso di DbContext-EF6
description: Utilizzo di DbContext in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 83f3f318c6cd1b0ea440bd472a651ff4454716f1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072148"
---
# <a name="working-with-dbcontext"></a>Uso di DbContext

Per utilizzare Entity Framework per eseguire query, inserire, aggiornare ed eliminare dati utilizzando oggetti .NET, è innanzitutto necessario [creare un modello](xref:ef6/modeling/index) che esegue il mapping delle entità e delle relazioni definite nel modello alle tabelle di un database.

Una volta ottenuto un modello, la classe primaria con cui interagisce l'applicazione è `System.Data.Entity.DbContext` (spesso definita classe del contesto). È possibile usare un DbContext associato a un modello per:
- Scrivere ed eseguire query   
- Materializzare i risultati della query come oggetti entità
- Rilevare le modifiche apportate a tali oggetti
- Mantieni nuovamente le modifiche all'oggetto nel database
- Associare oggetti in memoria ai controlli dell'interfaccia utente

In questa pagina vengono fornite alcune indicazioni su come gestire la classe del contesto.  

## <a name="defining-a-dbcontext-derived-class"></a>Definizione di una classe derivata DbContext  

Il metodo consigliato per lavorare con context consiste nel definire una classe che deriva da DbContext ed espone le proprietà DbSet che rappresentano le raccolte delle entità specificate nel contesto. Se si usa la finestra di progettazione EF, il contesto verrà generato automaticamente. Se si lavora con Code First, in genere si scriverà il contesto autonomamente.  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

Quando si dispone di un contesto, è necessario eseguire una query per, aggiungere (usando `Add` `Attach` i metodi o) o rimuovere (usando `Remove` ) entità nel contesto tramite queste proprietà. L'accesso a una `DbSet` Proprietà in un oggetto Context rappresenta una query iniziale che restituisce tutte le entità del tipo specificato. Si noti che solo l'accesso a una proprietà non eseguirà la query. Una query viene eseguita nei casi seguenti:  

- Enumerata da un'istruzione `foreach` (C#) o `For Each` (Visual Basic).  
- Viene enumerato da un'operazione di raccolta, ad esempio `ToArray` , `ToDictionary` o `ToList` .  
- Gli operatori LINQ, ad esempio `First` o, `Any` vengono specificati nella parte più esterna della query.  
- Viene chiamato uno dei metodi seguenti: il `Load` metodo di estensione, `DbEntityEntry.Reload` ,  `Database.ExecuteSqlCommand` e `DbSet<T>.Find` , se un'entità con la chiave specificata non viene trovata già caricata nel contesto.  

## <a name="lifetime"></a>Durata  

La durata del contesto inizia al momento della creazione dell'istanza e termina quando l'istanza viene eliminata o sottoposta a Garbage Collection. Utilizzare l' **utilizzo** di se si desidera che tutte le risorse che i controlli del contesto vengano eliminate alla fine del blocco. Quando si utilizza **, il**compilatore crea automaticamente un blocco try/finally e chiama Dispose nel blocco **finally** .  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

Di seguito sono riportate alcune linee guida generali per la scelta della durata del contesto:  

- Quando si utilizzano applicazioni Web, utilizzare un'istanza del contesto per ogni richiesta.  
- Quando si utilizza Windows Presentation Foundation (WPF) o Windows Forms, utilizzare un'istanza del contesto per ogni modulo. In questo modo è possibile utilizzare la funzionalità di rilevamento delle modifiche fornita dal contesto.  
- Se l'istanza del contesto viene creata da un contenitore di inserimento delle dipendenze, in genere è responsabilità del contenitore eliminare il contesto.
- Se il contesto viene creato nel codice dell'applicazione, ricordarsi di eliminare il contesto quando non è più necessario.  
- Quando si utilizza il contesto con esecuzione prolungata, considerare quanto segue:  
    - Quando si caricano più oggetti e i relativi riferimenti in memoria, il consumo di memoria del contesto può aumentare rapidamente. È possibile pertanto che si verifichino problemi di prestazioni.  
    - Il contesto non è thread-safe, pertanto non deve essere condiviso tra più thread che vi lavorano contemporaneamente.
    - Se un'eccezione fa sì che il contesto sia in uno stato irreversibile, è possibile che l'intera applicazione venga terminata.  
    - Le possibilità che si verifichino problemi correlati alla concorrenza aumentano quando l'intervallo tra il momento in cui viene eseguita una query sui dati e quello in cui i dati vengono aggiornati aumenta.  

## <a name="connections"></a>Connessioni  

Per impostazione predefinita, il contesto gestisce le connessioni al database. Il contesto si apre e chiude le connessioni in base alle esigenze. Ad esempio, il contesto apre una connessione per eseguire una query e quindi chiude la connessione quando tutti i set di risultati sono stati elaborati.  

In alcuni casi, tuttavia, può essere necessario disporre di maggiore controllo sull'apertura e la chiusura della connessione. Quando si utilizza SQL Server Compact, ad esempio, è spesso consigliabile mantenere una connessione aperta separata al database per la durata dell'applicazione per migliorare le prestazioni. È possibile gestire manualmente questo processo tramite la proprietà `Connection`.  
