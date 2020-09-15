---
title: Annotazioni dei dati Code First-EF6
description: Annotazioni dei dati Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: dd91ddf674f2235190e50beb847e569a898ad59b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074064"
---
# <a name="code-first-data-annotations"></a>Annotazioni dei dati per Code First
> [!NOTE]
> **EF 4.1 solo e versioni successive** : le funzionalità, le API e così via descritte in questa pagina sono state introdotte in Entity Framework 4,1. Se si usa una versione precedente, alcune o tutte queste informazioni non sono valide.

Il contenuto di questa pagina è stato adattato da un articolo scritto in origine da Julie Lerman ( \<http://thedatafarm.com> ).

Entity Framework Code First consente di usare le proprie classi di dominio per rappresentare il modello su cui è basato EF per eseguire query, rilevamento delle modifiche e funzioni di aggiornamento. Code First utilizza un modello di programmazione denominato "Convenzione sulla configurazione". Code First presuppone che le classi seguano le convenzioni di Entity Framework e, in tal caso, verrà automaticamente illustrato come eseguire il processo. Tuttavia, se le classi non seguono le convenzioni, è possibile aggiungere configurazioni alle classi per fornire a EF le informazioni necessarie.

Code First offre due modi per aggiungere queste configurazioni alle classi. Uno usa semplici attributi denominati DataAnnotations e il secondo usa l'API Fluent di Code First, che consente di descrivere le configurazioni in modo imperativo nel codice.

Questo articolo è incentrato sull'uso di DataAnnotations (nello spazio dei nomi System. ComponentModel. DataAnnotations) per configurare le classi, evidenziando le configurazioni più comunemente necessarie. Le annotazioni vengono inoltre riconosciute da diverse applicazioni .NET, ad esempio ASP.NET MVC, che consente a queste applicazioni di sfruttare le stesse annotazioni per le convalide lato client.


## <a name="the-model"></a>Il modello

Illustrerò Code First DataAnnotations con una semplice coppia di classi: Blog e post.

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

Così come sono, le classi di Blog e post seguono comodamente la convenzione Code First e non richiedono alcuna modifica per consentire la compatibilità di EF. Tuttavia, è anche possibile usare le annotazioni per fornire altre informazioni a EF sulle classi e sul database a cui vengono mappate.

 

## <a name="key"></a>Chiave

Entity Framework si basa su ogni entità con un valore di chiave utilizzato per il rilevamento delle entità. Una convenzione di Code First è proprietà chiave implicita. Code First cercherà una proprietà denominata "ID" o una combinazione di nome di classe e "ID", ad esempio "BlogId". Questa proprietà eseguirà il mapping a una colonna chiave primaria nel database.

Il Blog e le classi post seguono entrambi questa convenzione. Cosa accade se non lo facevano? Che cosa succede se il Blog usava invece il nome *PrimaryTrackingKey* o anche *foo*? Se Code First non trova una proprietà che corrisponde a questa convenzione, verrà generata un'eccezione a causa del requisito Entity Framework che è necessario disporre di una proprietà chiave. È possibile utilizzare l'annotazione chiave per specificare quale proprietà deve essere utilizzata come EntityKey.

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

Se si usa la funzionalità di generazione del database Code First, nella tabella di Blog sarà presente una colonna chiave primaria denominata PrimaryTrackingKey, definita anche come Identity per impostazione predefinita.

![Tabella Blog con chiave primaria](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>Chiavi composte

Entity Framework supporta chiavi composite-chiavi primarie costituite da più di una proprietà. Ad esempio, è possibile avere una classe Passport la cui chiave primaria è una combinazione di PassportNumber e IssuingCountry.

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Il tentativo di usare la classe precedente nel modello EF comporterebbe un errore `InvalidOperationException` :

*Impossibile determinare l'ordinamento della chiave primaria composita per il tipo ' Passport '. Usare il metodo ColumnAttribute o HasKey per specificare un ordine per le chiavi primarie composte.*

Per usare le chiavi composite, Entity Framework necessario definire un ordine per le proprietà chiave. Per eseguire questa operazione, è possibile utilizzare l'annotazione di colonna per specificare un ordine.

>[!NOTE]
> Il valore dell'ordine è relativo (anziché basato sull'indice), pertanto è possibile utilizzare qualsiasi valore. Ad esempio, 100 e 200 sarebbero accettabili al posto di 1 e 2.

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

Se sono presenti entità con chiavi esterne composite, è necessario specificare lo stesso ordine di colonna usato per le proprietà della chiave primaria corrispondente.

Solo l'ordinamento relativo all'interno delle proprietà della chiave esterna deve essere lo stesso, non è necessario che i valori esatti assegnati a **Order** corrispondano. Nella classe seguente, ad esempio, è possibile utilizzare 3 e 4 al posto di 1 e 2.

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>Obbligatoria

L' `Required` annotazione indica a EF che è richiesta una particolare proprietà.

L'aggiunta di required alla proprietà title forza EF (e MVC) a garantire che la proprietà disponga di dati.

``` csharp
    [Required]
    public string Title { get; set; }
```

Senza ulteriori modifiche al codice o al markup nell'applicazione, un'applicazione MVC eseguirà la convalida lato client, anche creando dinamicamente un messaggio utilizzando i nomi delle proprietà e delle annotazioni.

![Errore obbligatorio creazione pagina con titolo](~/ef6/media/jj591583-figure02.png)

L'attributo obbligatorio influirà anche sul database generato rendendo non nullable la proprietà mappata. Si noti che il campo title è stato modificato in "not null".

>[!NOTE]
> In alcuni casi potrebbe non essere possibile che la colonna nel database sia non nullable anche se la proprietà è obbligatoria. Ad esempio, quando si usano i dati della strategia di ereditarietà TPH per più tipi, viene archiviato in una singola tabella. Se un tipo derivato include una proprietà obbligatoria, la colonna non può essere resa non nullable perché non tutti i tipi nella gerarchia avranno questa proprietà.

 

![Tabella Blog](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength e MinLength

Gli `MaxLength` `MinLength` attributi e consentono di specificare convalide di proprietà aggiuntive, esattamente come è stato fatto con `Required` .

Ecco il BloggerName con i requisiti di lunghezza. Nell'esempio viene inoltre illustrato come combinare gli attributi.

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

L'annotazione MaxLength avrà un effetto sul database impostando la lunghezza della proprietà su 10.

![Tabella Blog che mostra la lunghezza massima nella colonna BloggerName](~/ef6/media/jj591583-figure04.png)

L'annotazione lato client MVC e l'annotazione lato server EF 4,1 rispettano questa convalida, generando nuovamente in modo dinamico un messaggio di errore: "il campo BloggerName deve essere un tipo stringa o matrice con una lunghezza massima di" 10 ". Il messaggio è leggermente lungo. Molte annotazioni consentono di specificare un messaggio di errore con l'attributo ErrorMessage.

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

È anche possibile specificare ErrorMessage nell'annotazione richiesta.

![Crea pagina con messaggio di errore personalizzato](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

La convenzione Code First impone che ogni proprietà di un tipo di dati supportato venga rappresentata nel database. Tuttavia, questo non è sempre il caso delle applicazioni. È ad esempio possibile avere una proprietà nella classe Blog che crea un codice basato sui campi title e BloggerName. Tale proprietà può essere creata dinamicamente e non deve essere archiviata. È possibile contrassegnare tutte le proprietà che non eseguono il mapping al database con l'annotazione NotMapped, ad esempio questa proprietà BlogCode.

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

Non è insolito descrivere le entità di dominio in un set di classi e quindi eseguire il layer di tali classi per descrivere un'entità completa. Ad esempio, è possibile aggiungere una classe denominata BlogDetails al modello.

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

Si noti che non `BlogDetails` ha alcun tipo di proprietà chiave. Nella progettazione basata su dominio, `BlogDetails` viene definito oggetto valore. Entity Framework fa riferimento a oggetti valore come tipi complessi.I tipi complessi non possono essere rilevati autonomamente.

Tuttavia, come proprietà nella `Blog` classe, `BlogDetails` verrà rilevata come parte di un `Blog` oggetto. Per poter riconoscere il codice prima, è necessario contrassegnare la `BlogDetails` classe come `ComplexType` .

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

A questo punto è possibile aggiungere una proprietà nella `Blog` classe per rappresentare `BlogDetails` per tale Blog.

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

Nel database, la tabella conterrà `Blog` tutte le proprietà del Blog, incluse le proprietà contenute nella relativa `BlogDetail` Proprietà. Per impostazione predefinita, ciascuna di esse è preceduta dal nome del tipo complesso "BlogDetail".

![Tabella Blog con tipo complesso](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

L' `ConcurrencyCheck` annotazione consente di contrassegnare una o più proprietà da utilizzare per il controllo della concorrenza nel database quando un utente modifica o Elimina un'entità. Se si è lavorato con la finestra di progettazione di Entity Framework, questo si allinea impostando la proprietà `ConcurrencyMode` su `Fixed` .

Verrà ora illustrato `ConcurrencyCheck` il funzionamento aggiungendolo alla `BloggerName` Proprietà.

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

Quando `SaveChanges` viene chiamato il metodo, a causa dell' `ConcurrencyCheck` annotazione nel `BloggerName` campo, il valore originale di tale proprietà verrà usato nell'aggiornamento. Il comando tenterà di individuare la riga corretta filtrando non solo sul valore della chiave, ma anche sul valore originale di `BloggerName` .Di seguito sono riportate le parti critiche del comando UPDATE inviate al database, in cui è possibile notare che il comando aggiornerà la riga che ha un `PrimaryTrackingKey` valore pari a 1 e a `BloggerName` di "Julie", che rappresenta il valore originale quando il Blog è stato recuperato dal database.

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

Se nel frattempo è stato modificato il nome del blogger per quel blog, l'aggiornamento avrà esito negativo e si otterrà una **DbUpdateConcurrencyException** che è necessario gestire.

 

## <a name="timestamp"></a>TimeStamp

È più comune usare i campi rowversion o timestamp per il controllo della concorrenza. Invece di usare l' `ConcurrencyCheck` annotazione, è possibile usare l'annotazione più specifica, purché `TimeStamp` il tipo della proprietà sia una matrice di byte. Code First considererà `Timestamp` le proprietà come le `ConcurrencyCheck` proprietà, ma assicurerà anche che il campo del database generato da Code First non ammette i valori null. È possibile avere una sola proprietà timestamp in una determinata classe.

Aggiunta della proprietà seguente alla classe Blog:

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

genera prima di tutto una colonna timestamp che non ammette i valori null nella tabella di database.

![Tabella Blogs con colonna timestamp](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>Tabella e colonna

Se si lascia Code First creare il database, può essere necessario modificare il nome delle tabelle e delle colonne che sta creando. È inoltre possibile utilizzare Code First con un database esistente. Non è sempre il caso che i nomi delle classi e delle proprietà nel dominio corrispondano ai nomi delle tabelle e delle colonne nel database.

La classe è denominata `Blog` e per convenzione, il Code First presuppone che questo venga mappato a una tabella denominata `Blogs` . In caso contrario, è possibile specificare il nome della tabella con l' `Table` attributo. Qui, ad esempio, l'annotazione specifica che il nome della tabella è **InternalBlogs**.

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

L' `Column` annotazione è più abile nella definizione degli attributi di una colonna mappata. È possibile specificare un nome, un tipo di dati o anche l'ordine in cui una colonna viene visualizzata nella tabella. Di seguito è riportato un esempio dell' `Column` attributo.

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

Non confondere `TypeName` l'attributo della colonna con DataType DataAnnotation. DataType è un'annotazione utilizzata per l'interfaccia utente e viene ignorata dal Code First.

Di seguito è riportata la tabella dopo che è stata rigenerata. Il nome della tabella è stato modificato in **InternalBlogs** e la `Description` colonna del tipo complesso è ora `BlogDescription` . Poiché il nome è stato specificato nell'annotazione, Code First non utilizzerà la convenzione di avvio del nome della colonna con il nome del tipo complesso.

![Tabella e colonna di Blog rinominati](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

Una funzionalità di database importante è la possibilità di disporre di proprietà calcolate. Se si esegue il mapping delle classi Code First a tabelle che contengono colonne calcolate, non si desidera che Entity Framework tenti di aggiornare tali colonne. Ma si vuole che EF restituisca tali valori dal database dopo l'inserimento o l'aggiornamento dei dati. È possibile usare l' `DatabaseGenerated` annotazione per contrassegnare le proprietà della classe insieme all' `Computed` enum. Altre enumerazioni sono `None` e `Identity` .

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

È possibile utilizzare il database generato su colonne di tipo byte o timestamp al momento della generazione del database da parte del codice; in caso contrario, è consigliabile utilizzare questa opzione solo quando si fa riferimento a database esistenti perché Code First non sarà in grado di determinare la formula per la colonna calcolata.

Per impostazione predefinita, una proprietà chiave che corrisponde a un numero intero diventerà una chiave di identità nel database. Corrisponderebbe `DatabaseGenerated` a impostare su `DatabaseGeneratedOption.Identity` . Se non si desidera che sia una chiave di identità, è possibile impostare il valore su `DatabaseGeneratedOption.None` .

 

## <a name="index"></a>Indice

> [!NOTE]
> **Ef 6.1 e versioni successive** : l' `Index` attributo è stato introdotto in Entity Framework 6,1. Se si usa una versione precedente, le informazioni contenute in questa sezione non sono valide.

È possibile creare un indice in una o più colonne usando **IndexAttribute**. Se si aggiunge l'attributo a una o più proprietà, Entity Framework creerà l'indice corrispondente nel database durante la creazione del database oppure esegue l'impalcatura delle chiamate **CreateIndex** corrispondenti se si utilizza Migrazioni Code First.

Il codice seguente, ad esempio, comporterà la creazione di un indice nella `Rating` colonna della `Posts` tabella nel database.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

Per impostazione predefinita, l'indice verrà denominato **IX \_ &lt; Property Name &gt; ** (IX \_ rating nell'esempio precedente). È anche possibile specificare un nome per l'indice. Nell'esempio seguente viene specificato che l'indice deve essere denominato `PostRatingIndex` .

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

Per impostazione predefinita, gli indici non sono univoci, ma è possibile usare il `IsUnique` parametro denominato per specificare che un indice deve essere univoco. Nell'esempio seguente viene introdotto un indice univoco per il `User` nome di accesso di un oggetto.

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>Indici a più colonne

Gli indici che si estendono su più colonne vengono specificati utilizzando lo stesso nome in più annotazioni di indice per una tabella specificata. Quando si creano indici a più colonne, è necessario specificare un ordine per le colonne nell'indice. Il codice seguente, ad esempio, consente di creare un indice a più colonne in `Rating` e `BlogId` denominato **IX \_ BlogIdAndRating**. `BlogId` è la prima colonna nell'indice e `Rating` è il secondo.

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>Attributi relazione: InverseProperty e ForeignKey

> [!NOTE]
> Questa pagina fornisce informazioni sulla configurazione delle relazioni nel modello di Code First usando le annotazioni dei dati. Per informazioni generali sulle relazioni in EF e su come accedere e modificare i dati usando le relazioni, vedere [relazioni & proprietà di navigazione](xref:ef6/fundamentals/relationships). *

La convenzione Code First si occuperà delle relazioni più comuni nel modello, ma in alcuni casi è necessario aiuto.

La modifica del nome della proprietà chiave nella `Blog` classe ha creato un problema con la relativa relazione con `Post` . 

Quando si genera il database, il codice Visualizza prima la `BlogId` proprietà nella classe post e la riconosce, in base alla convenzione che corrisponde al nome di una classe più l' **ID**, come chiave esterna della `Blog` classe. Ma non è presente alcuna `BlogId` proprietà nella classe Blog. La soluzione consiste nel creare una proprietà di navigazione in `Post` e utilizzare l'oggetto `ForeignKey` DataAnnotation per aiutare il codice a comprendere come compilare la relazione tra le due classi (utilizzando la `Post.BlogId` proprietà), nonché come specificare vincoli nel database.

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

Il vincolo nel database Mostra una relazione tra `InternalBlogs.PrimaryTrackingKey` e `Posts.BlogId` . 

![relazione tra InternalBlogs. PrimaryTrackingKey e Posts. BlogId](~/ef6/media/jj591583-figure09.png)

`InverseProperty`Viene utilizzato quando si dispone di più relazioni tra le classi.

Nella `Post` classe può essere utile tenere traccia dell'autore di un post di Blog, nonché di chi lo ha modificato. Di seguito sono riportate due nuove proprietà di navigazione per la classe post.

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

È anche necessario aggiungere la `Person` classe a cui fanno riferimento queste proprietà. Alla `Person` classe sono associate proprietà di navigazione `Post` , una per tutti i post scritti dalla persona e l'altra per tutti i post aggiornati da tale persona.

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code First non è in grado di associare le proprietà nelle due classi in modo autonomo. La tabella di database per `Posts` deve avere una chiave esterna per la `CreatedBy` persona e una per la `UpdatedBy` persona, ma Code First creerà quattro proprietà di chiave esterna: ** \_ ID persona**, **Person \_ ID1**, ** \_ ID CreatedBy** e ** \_ ID UpdatedBy**.

![Tabella post con chiavi esterne aggiuntive](~/ef6/media/jj591583-figure10.png)

Per risolvere questi problemi, è possibile utilizzare l' `InverseProperty` annotazione per specificare l'allineamento delle proprietà.

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

Poiché la `PostsWritten` Proprietà in person sa che si riferisce al `Post` tipo, la relazione verrà compilata con `Post.CreatedBy` . Analogamente, `PostsUpdated` verrà connesso a `Post.UpdatedBy` . E Code First non creerà le chiavi esterne aggiuntive.

![Tabella post senza chiavi esterne aggiuntive](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>Riepilogo

Le annotazioni DataAnnotations non solo consentono di descrivere la convalida lato client e server nelle classi code first, ma consentono anche di migliorare e persino correggere i presupposti che Code First farà sulle classi in base alle convenzioni. Con le annotazioni DataAnnotations non solo è possibile gestire la generazione dello schema del database, ma è anche possibile eseguire il mapping delle classi Code First a un database preesistente.

Sebbene siano molto flessibili, tenere presente che le annotazioni di DataAnnotations forniscono solo le modifiche di configurazione più comuni che è possibile apportare alle classi code first. Per configurare le classi per alcuni dei casi limite, è necessario esaminare il meccanismo di configurazione alternativo, l'API Fluent di Code First.
