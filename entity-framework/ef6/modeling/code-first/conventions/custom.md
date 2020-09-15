---
title: Convenzioni Code First personalizzate-EF6
description: Convenzioni di Code First personalizzate in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 19f22d311438b8c7d3ac9d270017f3ea4907efda
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074029"
---
# <a name="custom-code-first-conventions"></a>Convenzioni di Code First personalizzate
> [!NOTE]
> **Solo EF6 e versioni successive**: funzionalità, API e altri argomenti discussi in questa pagina sono stati introdotti in Entity Framework 6. Se si usa una versione precedente, le informazioni qui riportate, o parte di esse, non sono applicabili.

Quando si usa Code First il modello viene calcolato dalle classi usando un set di convenzioni. Le [convenzioni Code First](xref:ef6/modeling/code-first/conventions/built-in) predefinite determinano elementi come la proprietà che diventa la chiave primaria di un'entità, il nome della tabella a cui viene eseguito il mapping di un'entità e la precisione e la scala di una colonna decimale per impostazione predefinita.

A volte queste convenzioni predefinite non sono ideali per il modello ed è necessario aggirarle configurando molte singole entità usando le annotazioni dei dati o l'API Fluent. Le convenzioni Code First personalizzate consentono di definire convenzioni personalizzate che forniscono impostazioni predefinite di configurazione per il modello. In questa procedura dettagliata vengono esaminati i diversi tipi di convenzioni personalizzate e viene illustrato come crearli.


## <a name="model-based-conventions"></a>Convenzioni basate su modelli

Questa pagina descrive l'API DbModelBuilder per le convenzioni personalizzate. Questa API dovrebbe essere sufficiente per la creazione della maggior parte delle convenzioni personalizzate. Tuttavia, esiste anche la possibilità di creare convenzioni basate su modelli, che modificano il modello finale dopo la creazione, per gestire scenari avanzati. Per altre informazioni, vedere [convenzioni basate su modelli](xref:ef6/modeling/code-first/conventions/model).

 

## <a name="our-model"></a>Modello

Si inizierà con la definizione di un modello semplice che è possibile usare con le convenzioni. Aggiungere le classi seguenti al progetto.

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a>Introduzione alle convenzioni personalizzate

Verrà ora scritta una convenzione che configura qualsiasi proprietà denominata Key come chiave primaria per il tipo di entità.

Le convenzioni sono abilitate nel generatore di modelli, a cui è possibile accedere eseguendo l'override di OnModelCreating nel contesto. Aggiornare la classe ProductContext come segue:

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

A questo punto, qualsiasi proprietà nel modello denominato Key verrà configurata come chiave primaria di qualsiasi entità di cui fa parte.

È anche possibile rendere più specifiche le convenzioni filtrando il tipo di proprietà che verrà configurata:

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

Verranno configurate tutte le proprietà chiamate chiave come chiave primaria dell'entità, ma solo se sono di tipo Integer.

Una funzionalità interessante del metodo IsKey è che si tratta di un additivo. Ciò significa che se si chiama IsKey su più proprietà e tutte diventano parte di una chiave composta. Un avvertimento è che, quando si specificano più proprietà per una chiave, è necessario specificare anche un ordine per tali proprietà. Questa operazione può essere eseguita chiamando il metodo HasColumnOrder come indicato di seguito:

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

Questo codice consente di configurare i tipi del modello in modo che abbiano una chiave composta costituita dalla colonna chiave int e dalla colonna nome stringa. Se si Visualizza il modello nella finestra di progettazione, l'aspetto sarà simile al seguente:

![Chiave composta](~/ef6/media/compositekey.png)

Un altro esempio di convenzioni di proprietà è la configurazione di tutte le proprietà DateTime nel modello per eseguire il mapping al tipo datetime2 in SQL Server anziché in DateTime. È possibile ottenere questo risultato con quanto segue:

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>Classi convenzione

Un altro modo per definire le convenzioni consiste nell'usare una classe Convention per incapsulare la convenzione. Quando si usa una classe Convention, viene creato un tipo che eredita dalla classe Convention nello spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions.

È possibile creare una classe di convenzione con la convenzione datetime2 mostrata in precedenza effettuando le operazioni seguenti:

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

Per indicare a EF di usare questa convenzione, è necessario aggiungerla alla raccolta Conventions in OnModelCreating, che se è stata completata con la procedura dettagliata sarà simile alla seguente:

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

Come si può notare, si aggiunge un'istanza della convenzione alla raccolta Conventions. L'ereditarietà dalla convenzione fornisce un modo pratico per raggruppare e condividere le convenzioni tra team o progetti. È possibile, ad esempio, avere una libreria di classi con un set di convenzioni comune usato da tutti i progetti delle organizzazioni.

 

## <a name="custom-attributes"></a>Attributi personalizzati

Un altro ottimo utilizzo delle convenzioni è quello di consentire l'utilizzo di nuovi attributi durante la configurazione di un modello. Per illustrare questo problema, creare un attributo che è possibile usare per contrassegnare le proprietà di stringa come non Unicode.

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

A questo punto, creare una convenzione per applicare questo attributo al modello:

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

Con questa convenzione è possibile aggiungere l'attributo non Unicode a una qualsiasi delle proprietà della stringa, il che significa che la colonna nel database verrà archiviata come varchar anziché nvarchar.

Una cosa da notare in merito a questa convenzione è che se si inserisce l'attributo non Unicode su un valore diverso da una proprietà stringa, verrà generata un'eccezione. Questa operazione viene eseguita perché non è possibile configurare l'estensione Unicode su un tipo diverso da una stringa. In tal caso, è possibile rendere più specifica la convenzione, in modo da escludere qualsiasi elemento che non sia una stringa.

Sebbene la convenzione precedente funzioni per la definizione di attributi personalizzati, è disponibile un'altra API che può essere molto più semplice da utilizzare, specialmente quando si desidera utilizzare le proprietà della classe Attribute.

Per questo esempio verrà aggiornato l'attributo e il valore verrà modificato in un attributo deunicode, quindi sarà simile al seguente:

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

Al termine di questa operazione, è possibile impostare un valore bool sull'attributo per indicare alla convenzione se una proprietà deve essere Unicode o meno. È possibile eseguire questa operazione nella convenzione già eseguita accedendo al ClrProperty della classe di configurazione come segue:

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

Questa operazione è abbastanza semplice, ma esiste un modo più conciso per ottenere questo problema usando il metodo having dell'API Conventions. Il metodo having ha un parametro di tipo Func &lt; PropertyInfo, T &gt; che accetta l'oggetto PropertyInfo uguale al metodo Where, ma si prevede che restituisca un oggetto. Se l'oggetto restituito è null, la proprietà non verrà configurata, il che significa che è possibile filtrare le proprietà con il valore come WHERE, ma è diverso in quanto acquisisce anche l'oggetto restituito e lo passa al metodo Configure. Il funzionamento è simile al seguente:

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

Gli attributi personalizzati non sono l'unico motivo per usare il metodo having, ma è utile ovunque sia necessario ragionare su un elemento su cui si sta filtrando quando si configurano tipi o proprietà.

 

## <a name="configuring-types"></a>Configurazione di tipi

Fino a questo punto, tutte le convenzioni sono state usate per le proprietà, ma esiste un'altra area dell'API convenzioni per la configurazione dei tipi nel modello. L'esperienza è simile alle convenzioni che abbiamo visto finora, ma le opzioni all'interno di configure si troveranno all'entità anziché al livello della proprietà.

Uno degli elementi che possono essere particolarmente utili per le convenzioni del livello di tipo è la modifica della convenzione di denominazione della tabella, per eseguire il mapping a uno schema esistente diverso da quello predefinito di EF oppure per creare un nuovo database con una convenzione di denominazione diversa. A tale scopo, è necessario innanzitutto un metodo in grado di accettare TypeInfo per un tipo nel modello e restituire il nome della tabella per quel tipo:

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

Questo metodo accetta un tipo e restituisce una stringa che usa lettere minuscole con caratteri di sottolineatura anziché CamelCase. Nel modello questo significa che la classe ProductCategory verrà mappata a una tabella denominata Product \_ Category anziché ProductCategories.

Una volta ottenuto il metodo, è possibile chiamarlo in una convenzione come la seguente:

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

Questa convenzione Configura ogni tipo nel modello per eseguire il mapping al nome della tabella restituito dal metodo gettablenamename. Questa convenzione equivale a chiamare il metodo ToTable per ogni entità nel modello usando l'API Fluent.

Una cosa da tenere presente è che quando si chiama ToTable EF prenderà la stringa fornita come nome di tabella esatto, senza alcuna parte della pluralità che verrebbe eseguita normalmente durante la determinazione dei nomi delle tabelle. Questo è il motivo per cui il nome della tabella della convenzione è Product \_ Category anziché categorie di prodotti \_ . Possiamo risolverlo nella convenzione effettuando una chiamata al servizio di pluralismo.

Nel codice seguente verrà usata la funzionalità di [risoluzione delle dipendenze](xref:ef6/fundamentals/configuring/dependency-resolution) aggiunta in EF6 per recuperare il servizio di pluralismo che EF avrebbe usato e plurali il nome della tabella.

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> La versione generica di GetService è un metodo di estensione nello spazio dei nomi System. Data. Entity. Infrastructure. DependencyResolution. è necessario aggiungere un'istruzione using al contesto per poterla usare.

### <a name="totable-and-inheritance"></a>ToTable ed ereditarietà

Un altro aspetto importante di ToTable è che se si esegue in modo esplicito il mapping di un tipo a una determinata tabella, è possibile modificare la strategia di mapping che verrà usata da EF. Se si chiama ToTable per ogni tipo in una gerarchia di ereditarietà, passando il nome del tipo come nome della tabella, come descritto in precedenza, si modificherà la strategia di mapping tabella per gerarchia (TPH) predefinita a tabella per tipo (TPT). Il modo migliore per descrivere questa operazione è un esempio concreto:

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

Per impostazione predefinita, sia Employee che Manager vengono mappati alla stessa tabella (Employees) del database. La tabella conterrà sia i dipendenti che i responsabili con una colonna discriminatore che indica il tipo di istanza archiviato in ogni riga. Si tratta di un mapping TPH poiché esiste una singola tabella per la gerarchia. Tuttavia, se si chiama ToTable su entrambi i tipi, verrà eseguito il mapping di ogni tipo alla relativa tabella, nota anche come TPT, perché ogni tipo ha una propria tabella.

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

Il codice precedente eseguirà il mapping a una struttura di tabella simile alla seguente:

![Esempio di TPT](~/ef6/media/tptexample.jpg)

È possibile evitare questo problema e mantenere il mapping predefinito di TPH, in due modi:

1.  Chiamare ToTable con lo stesso nome di tabella per ogni tipo nella gerarchia.
2.  Chiamare ToTable solo sulla classe di base della gerarchia, in questo esempio si tratta di Employee.

 

## <a name="execution-order"></a>Ordine di esecuzione

Le convenzioni operano in un ultimo modo WINS, come l'API Fluent. Ciò significa che se si scrivono due convenzioni che configurano la stessa opzione della stessa proprietà, l'ultima esecuzione di WINS. Ad esempio, nel codice sotto la lunghezza massima di tutte le stringhe è impostato su 500, ma vengono quindi configurate tutte le proprietà denominate nel modello per avere una lunghezza massima di 250.

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

Poiché la convenzione per impostare la lunghezza massima su 250 è successiva a quella che imposta tutte le stringhe su 500, tutte le proprietà denominate nel modello avranno un valore MaxLength di 250 mentre qualsiasi altra stringa, ad esempio Description, sarà 500. L'uso di convenzioni in questo modo significa che è possibile fornire una convenzione generale per i tipi o le proprietà nel modello e quindi Sostituisci per subset diversi.

Le annotazioni di dati e API Fluent possono essere utilizzate anche per eseguire l'override di una convenzione in casi specifici. Nell'esempio precedente, se è stata usata l'API Fluent per impostare la lunghezza massima di una proprietà, è possibile inserirla prima o dopo la convenzione, perché l'API Fluent più specifica vincerà la convenzione di configurazione più generale.

 

## <a name="built-in-conventions"></a>Convenzioni predefinite

Poiché le convenzioni personalizzate possono essere influenzate dalle convenzioni Code First predefinite, può essere utile aggiungere convenzioni da eseguire prima o dopo un'altra convenzione. A tale scopo, è possibile usare i metodi AddBefore e AddAfter della raccolta Conventions nel DbContext derivato. Il codice seguente aggiunge la classe della convenzione creata in precedenza, in modo che venga eseguita prima della convenzione di individuazione chiave incorporata.

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

Questo sarà il più usato quando si aggiungono le convenzioni che devono essere eseguite prima o dopo le convenzioni predefinite. un elenco delle convenzioni predefinite è disponibile qui: [spazio dei nomi System. Data. Entity. ModelConfiguration. Conventions](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).

È inoltre possibile rimuovere le convenzioni che non si desidera applicare al modello. Per rimuovere una convenzione, usare il metodo Remove. Di seguito è riportato un esempio di rimozione del PluralizingTableNameConvention.

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
