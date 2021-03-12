---
title: Rilevamento delle modifiche e notifiche-EF Core
description: Rilevamento delle modifiche alle proprietà e alle relazioni mediante DetectChanges o notifiche
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: fae8bdb1a89478531535b377f4ba8b02d1c848f4
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023185"
---
# <a name="change-detection-and-notifications"></a>Rilevamento delle modifiche e notifiche

Ogni <xref:Microsoft.EntityFrameworkCore.DbContext> istanza tiene traccia delle modifiche apportate alle entità. Queste entità rilevate, a loro volta, comportano la modifica del database quando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> viene chiamato il metodo. Questa operazione viene illustrata in [Rilevamento modifiche EF core e in](xref:core/change-tracking/index)questo documento si presuppone che gli Stati dell'entità e le nozioni di base del rilevamento delle modifiche Entity Framework Core (EF Core) siano compresi.

Per il rilevamento delle modifiche alle proprietà e alle relazioni è necessario che DbContext sia in grado di rilevare tali modifiche. In questo documento viene illustrato il modo in cui si verifica questo rilevamento, nonché come utilizzare le notifiche delle proprietà o i proxy di rilevamento delle modifiche per forzare il rilevamento immediato delle modifiche.

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeDetectionAndNotifications).

## <a name="snapshot-change-tracking"></a>Rilevamento modifiche basato su snapshot

Per impostazione predefinita, EF Core crea uno snapshot dei valori delle proprietà di ogni entità quando viene rilevata per la prima volta da un'istanza di DbContext. I valori archiviati in questo snapshot vengono quindi confrontati con i valori correnti dell'entità per determinare quali valori della proprietà sono stati modificati.

Questo rilevamento delle modifiche si verifica quando viene chiamato SaveChanges per assicurarsi che tutti i valori modificati vengano rilevati prima di inviare aggiornamenti al database. Tuttavia, il rilevamento delle modifiche avviene anche in altri momenti per garantire che l'applicazione funzioni con informazioni di rilevamento aggiornate. Il rilevamento delle modifiche può essere forzato in qualsiasi momento chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="when-change-detection-is-needed"></a>Quando è necessario il rilevamento delle modifiche

Il rilevamento delle modifiche è necessario quando una proprietà o una navigazione è stata modificata _senza usare EF core per apportare questa modifica_. Si consideri ad esempio il caricamento di Blog e post e quindi la modifica di queste entità:

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

Esaminando la [visualizzazione di debug di rilevamento modifiche](xref:core/change-tracking/debug-views) prima di chiamare <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> viene indicato che le modifiche apportate non sono state rilevate e pertanto non vengono riflesse negli Stati dell'entità e i dati delle proprietà modificati:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

In particolare, lo stato della voce di Blog è ancora `Unchanged` e il nuovo post non viene visualizzato come entità rilevata. Si noterà che le proprietà segnalano i nuovi valori, anche se queste modifiche non sono state ancora rilevate dal EF Core. Ciò è dovuto al fatto che la visualizzazione debug sta leggendo i valori correnti direttamente dall'istanza dell'entità.

A differenza di quanto segue con la visualizzazione debug dopo la chiamata a DetectChanges:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Il Blog è ora contrassegnato correttamente come `Modified` e il nuovo post è stato rilevato e viene registrato come `Added` .

All'inizio di questa sezione è stato indicato che è necessario rilevare le modifiche quando non si usa _EF core per apportare la modifica_. Questo è ciò che accade nel codice riportato sopra. In altre circostanze, le modifiche apportate alla proprietà e alla navigazione vengono apportate _direttamente nelle istanze di entità_ e non usando metodi EF core.

A differenza di quanto avviene con il codice seguente, che modifica le entità nello stesso modo, ma questa volta usando EF Core metodi:

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

In questo caso, la visualizzazione di debug del rilevamento modifiche mostra che tutti gli Stati delle entità e le modifiche delle proprietà sono noti, anche se non si è verificato alcun rilevamento delle modifiche. Questo è dovuto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> al fatto che è un metodo di EF core, il che significa che EF Core sa immediatamente le modifiche apportate da questo metodo. Analogamente, <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> la chiamata a consente EF Core di conoscere immediatamente la nuova entità e di tenerne traccia in modo appropriato.

> [!TIP]
> Non tentare di evitare di rilevare le modifiche usando sempre EF Core metodi per apportare modifiche alle entità. Questa operazione è spesso più complessa ed esegue una minore quantità di modifiche alle entità in modo normale. Lo scopo di questo documento è fornire informazioni sul momento in cui è necessario rilevare le modifiche e in caso contrario. L'intenzione non è incoraggiare la prevenzione del rilevamento delle modifiche.

### <a name="methods-that-automatically-detect-changes"></a>Metodi che rilevano automaticamente le modifiche

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> viene chiamato automaticamente dai metodi in cui è probabile che questa operazione abbia un effetto sui risultati. Questi metodi sono:

- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>per assicurarsi che tutte le modifiche vengano rilevate prima di aggiornare il database.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>per assicurarsi che gli Stati delle entità e le proprietà modificate siano aggiornate.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, per assicurarsi che il risultato sia accurato.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, per garantire la correttezza degli Stati delle entità per le entità principale/padre prima della propagazione.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, per assicurarsi che il grafico rilevato sia aggiornato.

Esistono anche alcune posizioni in cui il rilevamento delle modifiche avviene solo in una singola istanza di entità, anziché nell'intero grafico delle entità rilevate. Questi punti sono:

- Quando <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> si usa, per assicurarsi che lo stato dell'entità e le proprietà modificate siano aggiornate.
- Quando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> si usano metodi come `Property` , `Collection` `Reference` o `Member` per assicurarsi che le modifiche delle proprietà, i valori correnti e così via siano aggiornati.
- Quando un'entità dipendente/figlio sta per essere eliminata perché una relazione obbligatoria è stata interrotta. Questa operazione rileva quando un'entità non deve essere eliminata perché è stata riassegnata all'elemento padre.

Il rilevamento locale delle modifiche per una singola entità può essere attivato in modo esplicito chiamando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .

> [!NOTE]
> Le modifiche rilevate localmente possono mancare alcune modifiche individuate da un rilevamento completo. Ciò si verifica quando le azioni di propagazione derivanti da modifiche non rilevate ad altre entità hanno un effetto sull'entità in questione. In tali situazioni è possibile che l'applicazione debba forzare un'analisi completa di tutte le entità chiamando in modo esplicito <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="disabling-automatic-change-detection"></a>Disabilitazione del rilevamento delle modifiche automatico

Le prestazioni del rilevamento delle modifiche non costituiscono un collo di bottiglia per la maggior parte delle applicazioni. Tuttavia, il rilevamento delle modifiche può costituire un problema di prestazioni per alcune applicazioni che tengono traccia di migliaia di entità. Il numero esatto dipenderà da molti elementi, ad esempio il numero di proprietà nell'entità. Per questo motivo è possibile disabilitare il rilevamento automatico delle modifiche usando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> . Si consideri, ad esempio, l'elaborazione di entità di join in una relazione molti-a-molti con payload:

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

Come è noto nella sezione precedente, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> e <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> rilevano automaticamente le modifiche. Tuttavia, dopo aver chiamato le voci, il codice non esegue alcuna modifica dello stato dell'entità o della proprietà. L'impostazione dei normali valori delle proprietà nelle entità aggiunte non comporta alcuna modifica dello stato. Il codice Disabilita pertanto il rilevamento automatico delle modifiche superflue quando si chiama il metodo SaveChanges di base. Il codice utilizza inoltre un blocco try/finally per assicurarsi che l'impostazione predefinita venga ripristinata anche se SaveChanges ha esito negativo.

> [!TIP]
> Non presupporre che il codice debba disabilitare il rilevamento automatico delle modifiche in per ottenere prestazioni ottimali. Questa operazione è necessaria solo quando si esegue la profilatura di un'applicazione che monitora molte entità indica che le prestazioni del rilevamento delle modifiche sono un problema.

### <a name="detecting-changes-and-value-conversions"></a>Rilevamento delle modifiche e delle conversioni dei valori

Per usare il rilevamento delle modifiche dello snapshot con un tipo di entità, EF Core deve essere in grado di:

- Crea uno snapshot di ogni valore di proprietà quando viene rilevata l'entità
- Confrontare questo valore con il valore corrente della proprietà
- Genera un codice hash per il valore

Questa operazione viene gestita automaticamente da EF Core per i tipi che possono essere mappati direttamente al database. Tuttavia, quando un [convertitore di valori viene usato per eseguire il mapping di una proprietà](xref:core/modeling/value-conversions), il convertitore deve specificare come eseguire queste azioni. Questa operazione viene eseguita con un operatore di confronto del valore ed è descritta in dettaglio nella documentazione relativa agli [operatori di confronto dei valori](xref:core/modeling/value-comparers) .

## <a name="notification-entities"></a>Entità di notifica

Il rilevamento delle modifiche dello snapshot è consigliato per la maggior parte delle applicazioni. Tuttavia, le applicazioni che tengono traccia di molte entità e/o apportano molte modifiche a tali entità possono trarre vantaggio dall'implementazione di entità che notificano automaticamente EF Core quando cambiano i valori di proprietà e di navigazione. Queste sono note come "entità di notifica".

### <a name="implementing-notification-entities"></a>Implementazione di entità di notifica

Le entità di notifica utilizzano le <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> interfacce e, che fanno parte della libreria di classi base .NET (BCL). Queste interfacce definiscono gli eventi che devono essere generati prima e dopo la modifica del valore di una proprietà. Ad esempio:

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

Inoltre, tutte le esplorazioni della raccolta devono implementare `INotifyCollectionChanged` ; nell'esempio precedente questa operazione è stata soddisfatta usando un <xref:System.Collections.ObjectModel.ObservableCollection%601> di post. EF Core fornisce anche un' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementazione con ricerche più efficienti a scapito dell'ordinamento stabile.

La maggior parte di questo codice di notifica viene in genere spostata in una classe di base non mappata. Ad esempio:

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a>Configurazione di entità di notifica

Non esiste alcun modo per convalidare la EF Core che `INotifyPropertyChanging` o `INotifyPropertyChanged` siano completamente implementati per l'uso con EF core. In particolare, alcuni utilizzi di queste interfacce vengono eseguite con le notifiche solo su determinate proprietà, anziché su tutte le proprietà (incluse le navigazioni) come richiesto dal EF Core. Per questo motivo, EF Core non viene collegato automaticamente a questi eventi.

Al contrario, EF Core necessario configurare per l'utilizzo di queste entità di notifica. Questa operazione viene in genere eseguita per tutti i tipi di entità chiamando <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> . Ad esempio:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

La strategia può essere impostata anche in modo diverso per diversi tipi di entità utilizzando <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> , ma ciò è in genere controproducente perché DetectChanges è ancora necessario per i tipi che non sono entità di notifica.

Per il rilevamento delle modifiche delle notifiche complete è necessario implementare sia che `INotifyPropertyChanging` `INotifyPropertyChanged` . In questo modo è possibile salvare i valori originali immediatamente prima della modifica del valore della proprietà, evitando la necessità di EF Core creare uno snapshot durante il rilevamento dell'entità. I tipi di entità che implementano solo `INotifyPropertyChanged` possono essere usati anche con EF core. In questo caso, EF crea ancora uno snapshot durante il rilevamento di un'entità per tenere traccia dei valori originali, ma usa le notifiche per rilevare immediatamente le modifiche, anziché dover chiamare DetectChanges.

I diversi <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> valori sono riepilogati nella tabella seguente.

| ChangeTrackingStrategy                              | Interfacce necessarie                                      | Richiede DetectChanges | Snapshot dei valori originali
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| Snapshot                                            | nessuno                                                   | Sì                 | Sì
| ChangedNotifications                                | INotifyPropertyChanged                                 | No                  | Sì
| ChangingAndChangedNotifications                     | INotifyPropertyChanged e INotifyPropertyChanging     | No                  | No
| ChangingAndChangedNotificationsWithOriginalValues   | INotifyPropertyChanged e INotifyPropertyChanging     | No                  | Sì

### <a name="using-notification-entities"></a>Utilizzo di entità di notifica

Le entità di notifica si comportano come qualsiasi altra entità, ad eccezione del fatto che le modifiche apportate alle istanze di entità non richiedono una chiamata a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> per rilevare tali modifiche. Ad esempio:

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

Con le entità normali, la [visualizzazione di debug di change tracker](xref:core/change-tracking/debug-views) ha mostrato che queste modifiche non sono state rilevate fino a quando non è stato chiamato DetectChanges. Esaminando la visualizzazione debug quando vengono usate le entità di notifica, le modifiche sono state rilevate immediatamente:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a>Proxy di rilevamento delle modifiche

> [!NOTE]
> I proxy di rilevamento delle modifiche sono stati introdotti nella EF Core 5,0.

EF Core possibile generare dinamicamente tipi proxy che implementano <xref:System.ComponentModel.INotifyPropertyChanging> e <xref:System.ComponentModel.INotifyPropertyChanged> . È necessario installare il pacchetto NuGet [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) e abilitare i proxy di rilevamento delle modifiche con, <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> ad esempio:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

La creazione di un proxy dinamico comporta la creazione di un nuovo tipo .NET dinamico (usando l'implementazione dei proxy di [Castle. Core](https://www.nuget.org/packages/Castle.Core/) ), che eredita dal tipo di entità e quindi esegue l'override di tutti i setter di proprietà. I tipi di entità per i proxy devono pertanto essere tipi che possono essere ereditati da e devono avere proprietà di cui è possibile eseguire l'override. Inoltre, le esplorazioni delle raccolte create in modo esplicito devono implementare, <xref:System.Collections.Specialized.INotifyCollectionChanged> ad esempio:

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

Uno svantaggio significativo dei proxy di rilevamento delle modifiche è che EF Core necessario rilevare sempre le istanze del proxy, mai le istanze del tipo di entità sottostante. Questo è dovuto al fatto che le istanze del tipo di entità sottostante non genereranno le notifiche, il che significa che le modifiche apportate a tali entità verranno perse.

EF Core crea automaticamente istanze proxy quando si esegue una query sul database, quindi questo svantaggio è in genere limitato al rilevamento di nuove istanze di entità. Queste istanze devono essere create usando i <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> metodi di estensione e **non** in modo normale usando `new` . Questo significa che il codice degli esempi precedenti deve ora usare `CreateProxy` :

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a>Eventi rilevamento modifiche

EF Core genera l' <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> evento quando viene rilevata un'entità per la prima volta. Le modifiche dello stato dell'entità future generano <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> eventi. Per ulteriori informazioni, vedere [eventi .NET in EF Core](xref:core/logging-events-diagnostics/events) .

> [!NOTE]
> L' `StateChanged` evento non viene generato quando viene rilevata per la prima volta un'entità, anche se lo stato è stato modificato da `Detached` a uno degli altri Stati. Assicurarsi di restare in ascolto degli `StateChanged` eventi e per `Tracked` ottenere tutte le notifiche pertinenti.
