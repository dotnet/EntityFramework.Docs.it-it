---
title: Testabilità ed Entity Framework 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434313"
---
# <a name="testability-and-entity-framework-40"></a>Testabilità ed Entity Framework 4.0
Scott Allen

Data di pubblicazione: maggio 2010

## <a name="introduction"></a>Introduzione

In questo white paper viene descritto e illustrato come scrivere codice teso con ADO.NET Entity Framework 4.0 e Visual Studio 2010. Questo documento non cerca di concentrarsi su una metodologia di test specifica, come la progettazione basata su test (TDD) o la progettazione basata sul comportamento (BDD). Questo documento si concentrerà invece su come scrivere codice che usa il ADO.NET Entity Framework ma rimane facile da isolare e testare in modo automatizzato. Esamineremo i modelli di progettazione comuni che facilitano i test in scenari di accesso ai dati e vedere come applicare tali modelli quando si usa il framework. Esamineremo anche le funzionalità specifiche del framework per vedere come tali funzionalità possono funzionare nel codice testrabile.

## <a name="what-is-testable-code"></a>Che cos'è il codice teso?

La possibilità di verificare un software utilizzando unit test automatizzati offre molti vantaggi desiderabili. Tutti sanno che buoni test ridurranno il numero di difetti del software in un'applicazione e aumenteranno la qualità dell'applicazione, ma avere unit test in atto va ben oltre la semplice ricerca di bug.

Un buon gruppo di unit test consente a un team di sviluppo di risparmiare tempo e mantenere il controllo del software che crea. Un team può apportare modifiche al codice esistente, effettuare il refactoring, riprogettare e ristrutturare il software per soddisfare i nuovi requisiti e aggiungere nuovi componenti in un'applicazione, conoscendo il gruppo di test in grado di verificare il comportamento dell'applicazione. Gli unit test fanno parte di un rapido ciclo di feedback per facilitare il cambiamento e preservare la manutenibilità del software con l'aumentare della complessità.

Unit test viene fornito con un prezzo, tuttavia. Un team deve investire il tempo necessario per creare e gestire unit test. La quantità di sforzo necessaria per creare questi test è direttamente correlata alla **testabilità** del software sottostante. Quanto è facile testare il software? Un team che progetta software tenendo presente la testabilità creerà test efficaci più velocemente rispetto al team che lavora con un software non testabile.

Microsoft ha progettato il ADO.NET Entity Framework 4.0 (EF4) con la testabilità in mente. Questo non significa che gli sviluppatori scriveranno unit test sul codice del framework stesso. Al contrario, gli obiettivi di testabilità per EF4 semplificano la creazione di codice testrabile che si basa sul framework. Prima di esaminare esempi specifici, vale la pena comprendere le qualità del codice testrabile.

### <a name="the-qualities-of-testable-code"></a>Le qualità del codice testrabile

Il codice facile da testare presenterà sempre almeno due tratti. In primo luogo, il codice testrabile è facile da **osservare**. Dato un certo set di input, dovrebbe essere facile osservare l'output del codice. Ad esempio, il test del metodo seguente è semplice perché il metodo restituisce direttamente il risultato di un calcolo.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Il test di un metodo è difficile se il metodo scrive il valore calcolato in un socket di rete, una tabella di database o un file come il codice seguente. Il test deve eseguire operazioni aggiuntive per recuperare il valore.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

In secondo luogo, il codice testrabile è facile da **isolare**. Usiamo il seguente pseudo-codice come un cattivo esempio di codice testrabile.

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

Il metodo è facile da osservare : possiamo passare una polizza assicurativa e verificare che il valore di ritorno corrisponda a un risultato atteso. Tuttavia, per testare il metodo è necessario disporre di un database installato con lo schema corretto e configurare il server SMTP nel caso in cui il metodo tenti di inviare un messaggio di posta elettronica.

Lo unit test desidera solo verificare la logica di calcolo all'interno del metodo, ma il test potrebbe non riuscire perché il server di posta elettronica non è in linea o perché il server di database è stato spostato. Entrambi questi errori non sono correlati al comportamento che il test desidera verificare. Il comportamento è difficile da isolare.

Gli sviluppatori di software che si sforzano di scrivere codice testrabile spesso si sforzano di mantenere una separazione delle preoccupazioni nel codice che scrivono. Il metodo precedente deve concentrarsi sui calcoli aziendali e delegare i dettagli di implementazione del database e della posta elettronica ad altri componenti. Robert C. Martin lo chiama principio di responsabilità unica. Un oggetto deve incapsulare una singola responsabilità limitata, ad esempio il calcolo del valore di un criterio. Tutti gli altri lavori di database e di notifica devono essere di competenza di un altro oggetto. Il codice scritto in questo modo è più facile da isolare perché è incentrato su una singola attività.

In .NET abbiamo le astrazioni che dobbiamo seguire il principio di responsabilità singola e raggiungere l'isolamento. Possiamo usare le definizioni di interfaccia e forzare il codice a usare l'astrazione dell'interfaccia anziché un tipo concreto. Più avanti in questo articolo vedremo come un metodo come l'esempio negativo presentato sopra può funzionare con interfacce che *sembrano* parlare con il database. In fase di test, tuttavia, è possibile sostituire un'implementazione fittizia che non comunica con il database ma contiene invece i dati in memoria. Questa implementazione fittizia isolare il codice da problemi non correlati nel codice di accesso ai dati o nella configurazione del database.

L'isolamento offre ulteriori vantaggi. Il calcolo aziendale nell'ultimo metodo dovrebbe richiedere solo pochi millisecondi per l'esecuzione, ma il test stesso potrebbe essere eseguito per alcuni secondi mentre il codice passa intorno alla rete e comunica con i vari server. Gli unit test devono essere eseguiti rapidamente per facilitare piccole modifiche. Gli unit test devono inoltre essere ripetibili e non avere esito negativo perché un componente non correlato al test presenta un problema. La scrittura di codice facile da osservare e da isolare significa che gli sviluppatori avranno un tempo più facile scrivere test per il codice, dedicare meno tempo in attesa di test da eseguire e, cosa più importante, dedicare meno tempo a rintracciare i bug che non esistono.

Speriamo che tu possa apprezzare i vantaggi del test e comprendere le qualità che presentano il codice testuono. Stiamo per capire come scrivere codice che funziona con EF4 per salvare i dati in un database pur rimanendo osservabile e facile da isolare, ma prima restringeremo il nostro obiettivo per discutere progetti testati per l'accesso ai dati.

## <a name="design-patterns-for-data-persistence"></a>Modelli di progettazione per la persistenza dei datiDesign Patterns for Data Persistence

Entrambi gli esempi negativi presentati in precedenza avevano troppe responsabilità. Il primo esempio non valido ha dovuto eseguire un calcolo *e* scrivere in un file. Il secondo esempio non valido è stato quello di leggere i dati da un database *ed* eseguire un calcolo aziendale *e* inviare messaggi di posta elettronica. Progettando metodi più piccoli che separano i problemi e delegano la responsabilità ad altri componenti, si farà grandi passi avanti verso la scrittura di codice testativo. L'obiettivo è quello di creare funzionalità componendo azioni da astrazioni piccole e mirate.

Quando si tratta di persistenza dei dati le astrazioni piccole e focalizzate che stiamo cercando sono così comuni che sono stati documentati come modelli di progettazione. Il libro Patterns of Enterprise Application Architecture di Martin Fowler è stato il primo lavoro a descrivere questi modelli in stampa. Verrà fornita una breve descrizione di questi modelli nelle sezioni seguenti prima di illustrare come questi ADO.NET Entity Framework implementa e funziona con questi modelli.

### <a name="the-repository-pattern"></a>Schema Repository

Fowler dice che un repository "media tra i livelli di mapping dei dati e del dominio utilizzando un'interfaccia simile a una raccolta per l'accesso agli oggetti di dominio". L'obiettivo del modello di repository è isolare il codice dalle minuzie dell'accesso ai dati e, come abbiamo visto in precedenza, l'isolamento è un tratto necessario per la testabilità.

La chiave per l'isolamento è il modo in cui il repository espone gli oggetti utilizzando un'interfaccia di tipo raccolta. La logica scritta per utilizzare il repository non ha idea di come il repository materializzerà gli oggetti che richiedi. Il repository potrebbe comunicare con un database o potrebbe semplicemente restituire oggetti da una raccolta in memoria. Tutto ciò che il codice deve sapere è che il repository viene visualizzato per gestire la raccolta ed è possibile recuperare, aggiungere ed eliminare oggetti dalla raccolta.

Nelle applicazioni .NET esistenti un repository concreto spesso eredita da un'interfaccia generica come la seguente:In existing .NET applications a concrete repository often inherits from a generic interface like the following:

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Apportiamo alcune modifiche alla definizione dell'interfaccia quando viene fornita un'implementazione per EF4, ma il concetto di base rimane lo stesso. Il codice può usare un repository concreto che implementa questa interfaccia per recuperare un'entità in base al relativo valore di chiave primaria, per recuperare una raccolta di entità in base alla valutazione di un predicato o semplicemente recuperare tutte le entità disponibili. Il codice può anche aggiungere e rimuovere entità tramite l'interfaccia del repository.

Dato un IRepository di Employee oggetti, codice può eseguire le seguenti operazioni.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Poiché il codice utilizza un'interfaccia (IRepository of Employee), è possibile fornire al codice diverse implementazioni dell'interfaccia. Un'implementazione potrebbe essere un'implementazione supportata da EF4 e la persistenza degli oggetti in un database di Microsoft SQL Server.One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database. Un'implementazione diversa (una usata durante il test) potrebbe essere supportata da un elenco di oggetti Employee in memoria. L'interfaccia consentirà di ottenere l'isolamento nel codice.

Si noti&lt;&gt; che l'interfaccia IRepository T non espone un'operazione di salvataggio. Come si aggiornano gli oggetti esistenti? Si potrebbe incontrare definizioni IRepository che includono il Save operazione e le implementazioni di questi repository sarà necessario rendere immediatamente persistente un oggetto nel database. Tuttavia, in molte applicazioni non si desidera rendere persistenti gli oggetti singolarmente. Al contrario, si desidera dare vita agli oggetti, ad esempio da repository diversi, modificare tali oggetti come parte di un'attività di business e quindi rendere persistenti tutti gli oggetti come parte di una singola operazione atomica. Fortunatamente, esiste un modello per consentire questo tipo di comportamento.

### <a name="the-unit-of-work-pattern"></a>L'unità di lavoro

Fowler dice che un'unità di lavoro "manterrà un elenco di oggetti interessati da una transazione aziendale e coordina la scrittura delle modifiche e la risoluzione dei problemi di concorrenza". È responsabilità dell'unità di lavoro tenere traccia delle modifiche apportate agli oggetti che diamo vita da un repository e rendere persistenti le modifiche apportate agli oggetti quando diciamo all'unità di lavoro di eseguire il commit delle modifiche. È anche responsabilità dell'unità di lavoro prendere i nuovi oggetti che abbiamo aggiunto a tutti i repository e inserire gli oggetti in un database, none anche l'eliminazione della gestione.

Se hai mai eseguito un lavoro con ADO.NET DataSet, avrai già familiarità con l'unità di lavoro. ADO.NET DataSet di ADO.NET aveva la possibilità di tenere traccia di aggiornamenti, eliminazioni e inserimento di oggetti DataRow e potrebbe (con l'aiuto di un TableAdapter) riconciliare tutte le modifiche apportate a un database. Tuttavia, gli oggetti DataSet modellano un subset disconnesso del database sottostante. L'unità di modello di lavoro presenta lo stesso comportamento, ma funziona con gli oggetti business e gli oggetti di dominio isolati dal codice di accesso ai dati e non a conoscenza del database.

Un'astrazione per modellare l'unità di lavoro nel codice .NET potrebbe essere simile alla seguente:An abstraction to model the unit of work in .NET code might look like the following:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Esponendo i riferimenti del repository dall'unità di lavoro possiamo garantire che una singola unità di oggetto di lavoro abbia la possibilità di tenere traccia di tutte le entità materializzate durante una transazione aziendale. L'implementazione del metodo Commit per un'unità di lavoro reale è dove tutte le magie si verificano per riconciliare le modifiche in memoria con il database. 

Dato un riferimento IUnitOfWork, il codice può apportare modifiche agli oggetti business recuperati da uno o più repository e salvare tutte le modifiche utilizzando l'operazione di commit atomica.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Il modello di carico lazyThe Lazy Load Pattern

Fowler usa il nome carico lazy per descrivere "un oggetto che non contiene tutti i dati necessari ma sa come ottenerli". Il caricamento lazy trasparente è una funzionalità importante da avere quando si scrive codice aziendale tepossibile e si lavora con un database relazionale. Ad esempio, si consideri il codice seguente.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Come viene popolata la raccolta TimeCards? Ci sono due possibili risposte. Una risposta è che l'archivio dei dipendenti, quando viene richiesto di recuperare un dipendente, invia una query per recuperare sia il dipendente che le informazioni della scheda oraria associate al dipendente. Nei database relazionali questa operazione richiede in genere una query con una clausola JOIN e può comportare il recupero di più informazioni di quelle necessarie per un'applicazione. Cosa succede se l'applicazione non ha mai bisogno di toccare la proprietà TimeCards?

Una seconda risposta consiste nel caricare la proprietà TimeCards "su richiesta". Questo caricamento lazy è implicito e trasparente per la logica di business perché il codice non richiama API speciali per recuperare le informazioni sulla scheda di tempo. Il codice presuppone che le informazioni sulla scheda dipendente siano presenti quando necessario. C'è una certa magia coinvolta con il caricamento lazy che generalmente comporta l'intercettazione di runtime delle chiamate al metodo. Il codice di intercettazione è responsabile per parlare con il database e recuperare le informazioni della scheda dipendente orario, lasciando la logica di business libero di essere logica di business. Questa magia di caricamento lazy consente al codice aziendale di isolarsi dalle operazioni di recupero dei dati e genera codice più teso.

Lo svantaggio di un carico lazy è che quando un'applicazione *ha* bisogno delle informazioni della scheda attività il codice eseguirà una query aggiuntiva. Questo non è un problema per molte applicazioni, ma per le applicazioni sensibili alle prestazioni o applicazioni che eseguono il ciclo attraverso un numero di oggetti dipendente e l'esecuzione di una query per recuperare le schede temporali durante ogni iterazione del ciclo (un problema spesso indicato come il problema di query N , 1 è un trascinamento. In questi scenari un'applicazione potrebbe voler caricare con entusiasmo le informazioni della scheda orario nel modo più efficiente possibile.

Fortunatamente, vedremo come EF4 supporta sia carichi lazy impliciti e carichi imposti efficienti come ci spostiamo nella sezione successiva e implementiamo questi modelli.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implementazione di modelli con Entity FrameworkImplementing Patterns with the Entity Framework

La buona notizia è che tutti i modelli di progettazione descritti nell'ultima sezione sono semplici da implementare con EF4. Per dimostrare che useremo una semplice applicazione MVC ASP.NET per modificare e visualizzare i dipendenti e le informazioni della scheda attività associata. Inizieremo utilizzando i seguenti "oggetti CLR semplici vecchi" (PoCO). 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

Queste definizioni di classe cambieranno leggermente man mano che esploriamo diversi approcci e funzionalità di EF4, ma lo scopo è mantenere queste classi il più possibile ignorate di persistenza (PI). Un oggetto PI non sa *come*, o anche *se*, lo stato che contiene vive all'interno di un database. PI e POCO vanno di pari passo con un software teso. Gli oggetti che utilizzano un approccio POCO sono meno vincolati, più flessibili e più facili da testare perché possono operare senza la presenza di un database.

Con i POCO sul posto è possibile creare un Entity Data Model (EDM) in Visual Studio (vedere figura 1). Non useremo l'EDM per generare codice per le nostre entità. Invece, vogliamo usare le entità che realizziamo con amore a mano. Utilizzeremo solo EDM per generare lo schema del database e fornire i metadati necessari eF4 per eseguire il mapping degli oggetti nel database.

![ef test_01](~/ef6/media/eftest-01.jpg)

**Figura 1**

Nota: se si desidera sviluppare prima il modello EDM, è possibile generare codice POCO pulito dall'EDM. È possibile farlo con un'estensione di Visual Studio 2010 fornita dal team di programmabilità dei dati. Per scaricare l'estensione, avviare Extension Manager dal menu Strumenti in Visual Studio e cercare "POCO" nella raccolta online di modelli (vedere figura 2). Ci sono diversi modelli POCO disponibili per EF. Per ulteriori informazioni sull'utilizzo del modello, vedere " [Procedura dettagliata: modello POCO per Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![test_02 ef](~/ef6/media/eftest-02.png)

**Figura 2**

Da questo punto di partenza POCO esploreremo due diversi approcci al codice testuabile. Il primo approccio chiamo l'approccio Entity Framework perché sfrutta le astrazioni dall'API di Entity Framework per implementare unità di lavoro e repository. Nel secondo approccio creeremo le nostre astrazioni di repository personalizzati e poi vedremo i vantaggi e gli svantaggi di ogni approccio. Inizieremo esplorando l'approccio di EF.  

### <a name="an-ef-centric-implementation"></a>Un'implementazione di EF Centric

Si consideri l'azione del controller seguente da un progetto MVC ASP.NET. L'azione recupera un oggetto Employee e restituisce un risultato per visualizzare una visualizzazione dettagliata del dipendente.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Il codice è testata? Ci sono almeno due test che avremmo bisogno di verificare il comportamento dell'azione. In primo luogo, vorremmo verificare che l'azione restituisca la visualizzazione corretta - un test semplice. Vorremmo anche scrivere un test per verificare che l'azione recuperi il dipendente corretto e vorremmo farlo senza eseguire codice per eseguire query sul database. Ricorda che vogliamo isolare il codice sotto test. L'isolamento garantirà che il test non abbia esito negativo a causa di un bug nel codice di accesso ai dati o nella configurazione del database. Se il test ha esito negativo, sapremo che abbiamo un bug nella logica del controller e non in un componente di sistema di livello inferiore.

Per ottenere l'isolamento avremo bisogno di alcune astrazioni come le interfacce che abbiamo presentato in precedenza per i repository e le unità di lavoro. Tenere presente che il modello di repository è progettato per mediare tra gli oggetti di dominio e il livello di mapping dei dati. In questo scenario EF4 *è* il livello di mapping dei dati e&lt;fornisce&gt; già un'astrazione simile a un repository denominata IObjectSet T (dallo spazio dei nomi System.Data.Objects ). La definizione dell'interfaccia è simile alla seguente.

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet&lt;&gt; T soddisfa i requisiti per un repository perché è simile&lt;&gt;a una raccolta di oggetti (tramite IEnumerable T ) e fornisce metodi per aggiungere e rimuovere oggetti dalla raccolta simulata. I metodi Attach e Detach espongono funzionalità aggiuntive dell'API EF4. Per utilizzare IObjectSet&lt;T&gt; come interfaccia per i repository è necessaria un'unità di astrazione del lavoro per associare i repository.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Un'implementazione concreta di questa interfaccia parlerà con SQL Server ed è facile da creare utilizzando la classe ObjectContext da EF4. La classe ObjectContext è l'unità di lavoro reale nell'API EF4.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

Dare&gt; vita a&lt;un oggetto T IObjectSet è facile come richiamare il metodo CreateObjectSet dell'oggetto ObjectContext. Dietro le quinte il framework utilizzerà i metadati forniti nell'EDM per produrre un oggetto ObjectSet&lt;T&gt;. Ci atterremo con la restituzione di IObjectSet&lt;T&gt; interfaccia perché contribuirà a preservare la testabilità nel codice client.

Questa implementazione concreta è utile nell'ambiente di produzione, ma è necessario concentrarsi su come useremo l'astrazione IUnitOfWork per facilitare il test.

### <a name="the-test-doubles"></a>Il test raddoppia

Per isolare l'azione del controller è necessaria la possibilità di passare tra l'unità di lavoro reale (supportata da un ObjectContext) e un test doppio o "falso" unità di lavoro (esecuzione di operazioni in memoria). L'approccio comune per eseguire questo tipo di commutazione consiste nel non consentire al controller MVC creare un'istanza di un'unità di lavoro, ma passare invece l'unità di lavoro nel controller come parametro del costruttore.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Il codice precedente è un esempio di inserimento delle dipendenze. Non è consentito al controller di creare la dipendenza (l'unità di lavoro) ma inseriamo la dipendenza nel controller. In un progetto MVC è comune usare una factory del controller personalizzata in combinazione con un contenitore di inversione del controllo (IoC) per automatizzare l'inserimento delle dipendenze. Questi argomenti esulano dall'ambito di questo articolo, ma è possibile leggere di più seguendo i riferimenti alla fine di questo articolo.

Un'unità di lavoro di implementazione fittizia che è possibile utilizzare per il test potrebbe essere simile alla seguente.

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

Si noti che l'unità di lavoro fittizia espone una proprietà Commited.Notice the fake unit of work exposes a Commited property. A volte è utile aggiungere funzionalità a una classe fittizia che facilitano il test. In questo caso è facile osservare se il codice esegue il commit di un'unità di lavoro controllando il Commited proprietà.

Avremo anche bisogno di un&lt;falso&gt; IObjectSet T per tenere Employee e TimeCard oggetti in memoria. Possiamo fornire una singola implementazione usando i generics.

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

Questo test raddoppia delega la maggior parte&lt;&gt; del proprio lavoro a un oggetto HashSet T sottostante. Si noti&lt;che&gt; IObjectSet T richiede un vincolo generico che impone T come&lt;classe&gt;(un tipo di riferimento) e ci costringe anche a implementare IQueryable T . È facile fare in modo che una raccolta&lt;in&gt; memoria venga visualizzata come un IQueryable T usando l'operatore LINQ standard AsQueryable.

### <a name="the-tests"></a>I test

Gli unit test tradizionali utilizzeranno una singola classe di test per contenere tutti i test per tutte le azioni in un singolo controller MVC. Possiamo scrivere questi test, o qualsiasi tipo di unit test, usando i falsi in memoria che abbiamo costruito. Tuttavia, per questo articolo si eviterà l'approccio alla classe di test monolitico e raggruppare invece i test per concentrarsi su un pezzo specifico di funzionalità.Ad esempio, "crea nuovo dipendente" potrebbe essere la funzionalità che si desidera testare, pertanto verrà utilizzata una singola classe di test per verificare l'azione del singolo controller responsabile della creazione di un nuovo dipendente.

C'è un codice di installazione comune che è necessario per tutte queste classi di test con granularità fine. Ad esempio, abbiamo sempre bisogno di creare i nostri repository in memoria e unità di lavoro falso. Abbiamo anche bisogno di un'istanza del controller dipendente con l'unità di lavoro falso iniettato. Questo codice di installazione comune verrà condiviso tra le classi di test usando una classe base.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

La "madre dell'oggetto" usata nella classe base è un modello comune per la creazione di dati di test. Un madre oggetto contiene metodi factory per creare un'istanza di entità di test da utilizzare in più dispositivi di test.

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

È possibile utilizzare il EmployeeControllerTestBase come classe di base per un numero di impianti di test (vedere figura 3). Ogni dispositivo di test testerà un'azione specifica del controller. Ad esempio, un dispositivo di test si concentrerà sul test dell'azione Create utilizzata durante una richiesta HTTP GET (per visualizzare la visualizzazione per la creazione di un dipendente) e un dispositivo diverso si concentrerà sull'azione Crea utilizzata in una richiesta HTTP POST (per eseguire le informazioni inviate dall'utente per creare un dipendente). Ogni classe derivata è responsabile solo dell'impostazione necessaria nel contesto specifico e di fornire le asserzioni necessarie per verificare i risultati per il contesto di test specifico.

![test_03 ef](~/ef6/media/eftest-03.png)

**Figura 3**

La convenzione di denominazione e lo stile di test presentati qui non sono necessari per il codice testrabile, ovvero un solo approccio. Figura 4 Mostra i test in esecuzione nel plug-in test runner Jet Brains Resharper per Visual Studio 2010.

![test_04 ef](~/ef6/media/eftest-04.png)

**Figura 4**

Con una classe base per gestire il codice di installazione condiviso, gli unit test per ogni azione del controller sono piccoli e facili da scrivere. I test verranno eseguiti rapidamente (poiché si eseguono operazioni in memoria) e non dovrebbero avere esito negativo a causa di problemi ambientali o infrastrutturali non correlati (perché abbiamo isolato l'unità sottoposta a test).

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

In questi test, la classe base esegue la maggior parte del lavoro di installazione. Tenere presente che il costruttore della classe base crea l'archivio in memoria, un'unità di lavoro fittizia e un'istanza della classe EmployeeController. La classe di test deriva da questa classe di base e si concentra sulle specifiche del test del metodo Create. In questo caso le specifiche si equivaliscono ai passaggi "organizzare, agire e asserire" che verrà visualizzata in qualsiasi procedura di unit test:

-   Creare un oggetto newEmployee per simulare i dati in ingresso.
-   Richiamare l'azione Create di EmployeeController e passare newEmployee.
-   Verificare che l'azione Crea produca i risultati previsti (il dipendente viene visualizzato nel repository).

Ciò che abbiamo creato ci permette di testare una qualsiasi delle azioni EmployeeController. Ad esempio, quando si scrivono test per l'azione indice del controller Employee è possibile ereditare dalla classe di base di test per stabilire la stessa impostazione di base per i test. Anche in questo caso la classe di base creerà il repository in memoria, l'unità di lavoro fittizia e un'istanza di EmployeeController. I test per l'azione Indice devono solo concentrarsi sulla chiamata dell'azione Indice e sul test delle qualità del modello restituito dall'azione.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

I test che stiamo creando con i falsi in memoria sono orientati verso il test dello *stato* del software. Ad esempio, quando si esegue il test dell'azione Crea, si desidera controllare lo stato del repository dopo l'esecuzione dell'azione di creazione: il repository contiene il nuovo dipendente?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

In seguito esamineremo i test basati sull'interazione. Test basati sull'interazione chiederanno se il codice sottoposto a test ha richiamato i metodi appropriati sui nostri oggetti e ha passato i parametri corretti. Per ora ci sposteremo sulla copertina un altro modello di progettazione - il carico pigro.

## <a name="eager-loading-and-lazy-loading"></a>Caricamento eager e caricamento lazy

A un certo punto del ASP.NET applicazione Web MVC è possibile visualizzare le informazioni di un dipendente e includere le schede dipendente associate del dipendente. Ad esempio, è possibile che nel sistema venga visualizzato un riepilogo scheda dipendente che mostra il nome del dipendente e il numero totale di schede attività nel sistema. Esistono diversi approcci che è possibile adottare per implementare questa funzionalità.

### <a name="projection"></a>Proiezione

Un approccio semplice per creare il riepilogo consiste nel costruire un modello dedicato alle informazioni che si desidera visualizzare nella visualizzazione. In questo scenario il modello potrebbe essere simile al seguente.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Si noti che il EmployeeSummaryViewModel non è un'entità – in altre parole non è qualcosa che si desidera rendere persistente nel database. Useremo questa classe solo per mischiare i dati nella visualizzazione in modo fortemente tipizzato. Il modello di visualizzazione è simile a un oggetto di trasferimento dati (DTO) perché non contiene alcun comportamento (nessun metodo) - solo proprietà. Le proprietà conterranno i dati che dobbiamo spostare. È facile creare un'istanza di questo modello di visualizzazione utilizzando l'operatore di proiezione standard di LINQ, ovvero l'operatore Select.It is easy to instantiate this view model using LINQ's standard projection operator – the Select operator.

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

Ci sono due caratteristiche notevoli per il codice sopra. Primo: il codice è facile da testare perché è ancora facile da osservare e isolare. L'operatore Select funziona altrettanto bene contro i nostri falsi in memoria come lo fa contro la vera unità di lavoro.

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

La seconda caratteristica notevole è come il codice consente EF4 per generare una singola query efficiente per assemblare insieme le informazioni dipendente e scheda ora. Abbiamo caricato le informazioni sui dipendenti e le informazioni sulla scheda attività nello stesso oggetto senza usare LE API speciali. Il codice si limitava a esprimere le informazioni necessarie utilizzando operatori LINQ standard che funzionano con origini dati in memoria e origini dati remote. EF4 è stato in grado di convertire le\# strutture ad albero dell'espressione generate dalla query LINQ e dal compilatore C in una query T-SQL singola ed efficiente.

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

Ci sono altri momenti in cui non vogliamo lavorare con un modello di visualizzazione o un oggetto DTO, ma con entità reali. Quando sappiamo che abbiamo bisogno di un dipendente *e* le schede temporali del dipendente, possiamo caricare con entusiasmo i dati correlati in modo discreto ed efficiente.

### <a name="explicit-eager-loading"></a>Caricamento esplicito dei eagerExplicit Eager Loading

Quando si desidera caricare con entusiasmo le informazioni sulle entità correlate è necessario un meccanismo per la logica di business (o in questo scenario, la logica di azione del controller) per esprimere il proprio desiderio al repository. La classe EF4 ObjectQuery&lt;T&gt; definisce un metodo Include per specificare gli oggetti correlati da recuperare durante una query. Tenere presente che EF4 ObjectContext espone le&lt;&gt; entità tramite la&lt;classe&gt;T Oggetto ObjectSet concreta che eredita da ObjectQuery T .Se si usassero&lt;&gt; riferimenti ObjectSet T nell'azione del controller, è possibile scrivere il codice seguente per specificare un carico entusiasta di informazioni sulla scheda oraria per ogni dipendente.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Tuttavia, poiché stiamo cercando di mantenere il codice testabili, non esponiamo ObjectSet&lt;T&gt; dall'esterno dell'unità reale della classe di lavoro. Invece, ci affidiamo&lt;&gt; all'interfaccia IObjectSet T che&lt;&gt; è più facile da falsificare, ma IObjectSet T non definisce un metodo Include. La bellezza di LINQ è che possiamo creare il nostro operatore Include.

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

Si noti che questo operatore Include&lt;è&gt; definito come&lt;metodo&gt;di estensione per IQueryable T anziché IObjectSet T . In questo modo è possibile utilizzare il metodo con una gamma&lt;&gt;più ampia&lt;di&gt;tipi&lt;possibili, tra cui IQueryable T , IObjectSet T , ObjectQuery T&gt;e ObjectSet&lt;T&gt;. Nel caso in cui la sequenza sottostante&lt;&gt;non sia un oggetto EF4 ObjectQuery T autentico , non vi è alcun danno e l'operatore Include è un no-op. Se la sequenza sottostante&gt; *è* un ObjectQuery&lt;T (o derivato da ObjectQuery&lt;T&gt;), EF4 visualizzerà il requisito per i dati aggiuntivi e formulare la query SQL corretta.

Con questo nuovo operatore sul posto possiamo richiedere esplicitamente un carico ansioso di informazioni sulla scheda orario dal repository.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Quando viene eseguito su un objectContext reale, il codice produce la query singola seguente. La query raccoglie informazioni sufficienti dal database in un unico viaggio per materializzare gli oggetti dipendente e popolare completamente la proprietà TimeCards.

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

La grande notizia è che il codice all'interno del metodo di azione rimane completamente teso. Non abbiamo bisogno di fornire funzionalità aggiuntive per i nostri falsi per supportare l'operatore Include. La cattiva notizia è che abbiamo dovuto usare l'operatore Include all'interno del codice che volevamo mantenere la persistenza ignorante. Questo è un primo esempio del tipo di compromessi che è necessario valutare durante la compilazione di codice testrabile. In alcuni casi è necessario lasciare che i problemi di persistenza perdano all'esterno dell'astrazione del repository per soddisfare gli obiettivi di prestazioni.

L'alternativa al caricamento eager è il caricamento lazy. Il caricamento lazy significa che *non* è necessario il codice aziendale per annunciare in modo esplicito il requisito per i dati associati. Al contrario, utilizziamo le entità nell'applicazione e se sono necessari dati aggiuntivi Entity Framework caricherà i dati su richiesta.

### <a name="lazy-loading"></a>Caricamento lazy

È facile immaginare uno scenario in cui non sappiamo quali dati sarà necessario un pezzo di logica di business. Potremmo sapere che la logica ha bisogno di un oggetto dipendente, ma possiamo diramare in percorsi di esecuzione diversi in cui alcuni di questi percorsi richiedono informazioni scheda dipendente dal dipendente, e alcuni non lo fanno. Scenari come questo sono perfetti per il caricamento lazy implicito perché i dati vengono magicamente visualizzati in base alle esigenze.

Il caricamento lazy, noto anche come caricamento posticipato, commette alcuni requisiti per gli oggetti entità. I POCO con vera ignoranza di persistenza non sarebbero all'indirizzo di alcun requisito a causa dello strato di persistenza, ma la vera ignoranza della persistenza è praticamente impossibile da raggiungere.Invece misuriamo l'ignoranza della persistenza in gradi relativi. Sarebbe un peccato se avessimo bisogno di ereditare da una classe di base orientata alla persistenza o di usare una raccolta specializzata per ottenere il caricamento lazy nei POCO. Fortunatamente, EF4 ha una soluzione meno intrusiva.

### <a name="virtually-undetectable"></a>Praticamente inosservabile

Quando si usano oggetti POCO, EF4 può generare dinamicamente proxy di runtime per le entità. Questi proxy eseguono in modo invisibilmente il wrapping delle POCO materializzate e forniscono servizi aggiuntivi intercettando ogni operazione get e set di proprietà per eseguire ulteriori operazioni. Uno di questi servizi è la funzione di caricamento lazy che stiamo cercando. Un altro servizio è un meccanismo efficiente di rilevamento delle modifiche che può registrare quando il programma modifica i valori delle proprietà di un'entità. L'elenco delle modifiche viene utilizzato da ObjectContext durante il SaveChanges metodo per rendere persistenti tutte le entità modificate utilizzando i comandi UPDATE.

Affinché questi proxy funzionino, tuttavia, è necessario un modo per eseguire l'hook nelle operazioni get e set di proprietà su un'entità e i proxy raggiungono questo obiettivo eseguendo l'override dei membri virtuali. Pertanto, se vogliamo avere il caricamento lazy implicito e il monitoraggio efficiente delle modifiche, è necessario tornare alle nostre definizioni di classe POCO e contrassegnare le proprietà come virtuali.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Possiamo ancora dire che l'entità Employee è per lo più persistence ignorant. L'unico requisito consiste nell'utilizzare membri virtuali e ciò non influisce sulla testabilità del codice. Non è necessario derivare da alcuna classe di base speciale o anche utilizzare una raccolta speciale dedicata al caricamento lazy. Come illustrato nel codice, qualsiasi&lt;&gt; classe che implementa ICollection T è disponibile per contenere le entità correlate.

C'è anche un piccolo cambiamento che dobbiamo fare all'interno della nostra unità di lavoro. Il caricamento lazy è disattivato per impostazione predefinita quando si lavora direttamente con un oggetto ObjectContext.Lazy loading is *off* by default when working directly with an ObjectContext object. C'è una proprietà che possiamo impostare sul ContextOptions proprietà per abilitare il caricamento posticipato, e possiamo impostare questa proprietà all'interno della nostra unità di lavoro reale se vogliamo abilitare il caricamento lazy ovunque.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

Con il caricamento lazy implicito abilitato, il codice dell'applicazione può utilizzare un dipendente e le schede orari associate del dipendente, pur rimanendo beatamente inconsapevoli del lavoro necessario per EF per caricare i dati aggiuntivi.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Il caricamento lazy rende il codice dell'applicazione più facile da scrivere e con la magia del proxy il codice rimane completamente teso. I falsi in memoria dell'unità di lavoro possono semplicemente precaricare entità false con dati associati quando necessario durante un test.

A questo punto si rivolgerà la nostra attenzione dalla&lt;creazione&gt; di repository utilizzando IObjectSet T e guardare le astrazioni per nascondere tutti i segni del framework di persistenza.

## <a name="custom-repositories"></a>Repository personalizzati

Quando abbiamo presentato per la prima volta il modello di progettazione dell'unità di lavoro in questo articolo abbiamo fornito del codice di esempio per l'aspetto dell'unità di lavoro. Ripresentiamo questa idea originale utilizzando lo scenario del biglietto da stato per dipendenti e dipendenti con cui abbiamo lavorato.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

La differenza principale tra questa unità di lavoro e l'unità di lavoro creata nell'ultima sezione è come questa unità di lavoro&lt;non&gt;utilizza alcuna astrazioni dal framework EF4 (non esiste IObjectSet T ). IObjectSet&lt;&gt; T funziona bene come un'interfaccia del repository, ma l'API che espone potrebbe non essere perfettamente allineata con le esigenze dell'applicazione. In questo approccio imminente rappresenteremo i repository&lt;&gt; utilizzando un'astrazione IRepository T personalizzata.

Molti sviluppatori che seguono la progettazione basata su test, la progettazione basata&lt;&gt; sul comportamento e la progettazione di metodologie basate su domini preferiscono l'approccio IRepository T per diversi motivi. In primo luogo, l'interfaccia IRepository&lt;T&gt; rappresenta un livello "anti-corruzione". Come descritto da Eric Evans nel suo libro Domain Driven Design, un livello anti-corruzione mantiene il codice di dominio lontano dalle API dell'infrastruttura, ad esempio un'API di persistenza. In secondo luogo, gli sviluppatori possono compilare metodi nel repository che soddisfano le esigenze esatte di un'applicazione (come rilevato durante la scrittura di test). Ad esempio, potrebbe essere spesso necessario individuare una singola entità usando un valore ID, pertanto è possibile aggiungere un metodo FindById all'interfaccia del repository.La definizione di IRepository&lt;T&gt; sarà simile alla seguente.

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Si noti che verrà rilasciata&lt;l'elemento all'utilizzo di un IQueryable T&gt; interfaccia per esporre le raccolte di entità. IQueryable&lt;&gt; T consente agli alberi di espressione LINQ di fluire nel provider EF4 e fornire al provider una visualizzazione olistica della query. Una seconda opzione consiste nel&lt;&gt;restituire IEnumerable T , il che significa che il provider LINQ EF4 vedrà solo le espressioni compilate all'interno del repository. Qualsiasi raggruppamento, ordinamento e proiezione eseguiti all'esterno del repository non verranno composti nel comando SQL inviato al database, il che può compromettere le prestazioni. D'altra parte, un repository che&lt;&gt; restituisce solo IEnumerable T risultati non vi sorprenderà mai con un nuovo comando SQL. Entrambi gli approcci funzioneranno e entrambi rimangono testati.

È semplice fornire una singola implementazione&lt;dell'interfaccia IRepository T&gt; utilizzando i generics e l'API ObjectContext di EF4.

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

L'approccio IRepository&lt;T&gt; offre un controllo aggiuntivo sulle query perché un client deve richiamare un metodo per accedere a un'entità. All'interno del metodo è possibile fornire ulteriori controlli e operatori LINQ per applicare i vincoli dell'applicazione. Si noti che l'interfaccia ha due vincoli sul parametro di tipo generico. Il primo vincolo è la classe cons&lt;taint richiesta dall'oggetto ObjectSet T&gt;e il secondo vincolo impone alle entità di implementare IEntity, ovvero un'astrazione creata per l'applicazione. L'interfaccia IEntity impone alle entità di avere una proprietà Id leggibile e possiamo quindi usare questa proprietà nel metodo FindById.The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method. IEntity è definito con il codice seguente.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity potrebbe essere considerata una piccola violazione dell'ignoranza della persistenza poiché le entità sono necessarie per implementare questa interfaccia. Tenere presente che l'ignoranza della persistenza riguarda i compromessi e per molti la funzionalità FindById supererà il vincolo imposto dall'interfaccia. L'interfaccia non ha alcun impatto sulla testabilità.

La creazione di un'istanza di un IRepository&lt;T&gt; live richiede un oggetto EF4 ObjectContext, pertanto un'unità di lavoro concreta di implementazione deve gestire la creazione di istanze.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>Utilizzo del repository personalizzato

L'utilizzo del repository personalizzato non è significativamente diverso&lt;&gt;dall'utilizzo del repository basato su IObjectSet T . Invece di applicare gli operatori LINQ direttamente a una proprietà, è necessario innanzitutto richiamare uno dei metodi del repository per acquisire un riferimento IQueryable&lt;T.&gt;

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Si noti che l'operatore include personalizzato implementato in precedenza funzionerà senza modifiche. Il metodo FindById del repository rimuove la logica duplicata dalle azioni che tentano di recuperare una singola entità.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Non vi è alcuna differenza significativa nella testabilità dei due approcci che abbiamo esaminato. Potremmo fornire implementazioni false&lt;di&gt; IRepository T creando&lt;classi&gt; concrete supportate da HashSet Employee - proprio come quello che abbiamo fatto nell'ultima sezione. Tuttavia, alcuni sviluppatori preferiscono utilizzare oggetti fittizi e framework di oggetti fittizi anziché creare falsi. Esamineremo l'utilizzo di simulazioni per testare la nostra implementazione e discutere le differenze tra simulazioni e falsi nella sezione successiva.

### <a name="testing-with-mocks"></a>Test con Mocks

Ci sono diversi approcci per costruire quello che Martin Fowler chiama un "test double". Un test double (come un film stunt double) è un oggetto che si costruisce per "stare in" per oggetti reali di produzione durante i test. I repository in memoria che abbiamo creato sono test doppi per i repository che comunicano con SQL Server. Abbiamo visto come usare questi test-double durante gli unit test per isolare il codice e mantenere i test in esecuzione veloce.

Il test raddoppia ha implementazioni reali e funzionanti. Dietro le quinte ognuno memorizza una raccolta concreta di oggetti, e aggiungerà e rimuoverà oggetti da questa raccolta come si manipola il repository durante un test. Alcuni sviluppatori amano costruire il loro test raddoppia in questo modo - con codice reale e implementazioni di lavoro.Questi doppi test sono ciò che chiamiamo *falsi*. Hanno implementazioni funzionanti, ma non sono abbastanza reali per l'uso in produzione. Il repository falso in realtà non scrive al database. Il server SMTP falso in realtà non invia un messaggio di posta elettronica sulla rete.

### <a name="mocks-versus-fakes"></a>Mocks versus Fakes

C'è un altro tipo di test double noto come un *mock*. Mentre i falsi hanno implementazioni di lavoro, le simulazioni sono senza implementazione. Con l'aiuto di un framework di oggetti fittizi costruiamo questi oggetti fittizi in fase di esecuzione e li usiamo come test double. In questa sezione useremo il framework di simulazione open source Moq. Ecco un semplice esempio di utilizzo di Moq per creare dinamicamente un test double per un repository di dipendenti.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Chiediamo a Moq un'implementazione di IRepository&lt;Employee&gt; e ne crea uno in modo dinamico. È possibile ottenere l'oggetto&lt;&gt; che implementa IRepository Employee&lt;accedendo alla proprietà Object dell'oggetto Mock T.&gt; È questo oggetto interno che possiamo passare nei nostri controller, e non sapranno se questo è un test double o il repository reale. È possibile richiamare metodi sull'oggetto proprio come si richiamano i metodi su un oggetto con un'implementazione reale.

È necessario chiedersi cosa farà il repository fittizio quando si richiama il Add metodo. Poiché non vi è alcuna implementazione dietro l'oggetto fittizio, Add non esegue alcuna operazione. Non c'è collezione concreta dietro le quinte come abbiamo avuto con i falsi che abbiamo scritto, quindi il dipendente viene scartato. Che dire del valore restituito di FindById? In questo caso l'oggetto fittizio esegue l'unica operazione che può eseguire, ovvero restituire un valore predefinito. Poiché viene restituito un tipo di riferimento (un Employee), il valore restituito è un valore null.

Mocks potrebbe sembrare inutile; tuttavia, ci sono altre due caratteristiche di scherni di cui non abbiamo parlato. In primo luogo, il framework Moq registra tutte le chiamate effettuate sull'oggetto fittizio. Più avanti nel codice possiamo chiedere a Moq se qualcuno ha richiamato il add metodo, o se qualcuno ha richiamato il FindById metodo. Vedremo più tardi come possiamo usare questa funzione di registrazione "scatola nera" nei test.

La seconda grande caratteristica è come possiamo usare Moq per programmare un oggetto fittizio con *aspettative*. Un'aspettativa indica all'oggetto fittizio come rispondere a qualsiasi interazione specificata. Ad esempio, è possibile programmare un'aspettativa nel nostro mock e dire di restituire un oggetto dipendente quando qualcuno richiama FindById.For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById. Il framework Moq utilizza un'API di installazione ed espressioni lambda per programmare queste aspettative.

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

In questo esempio chiediamo a Moq di creare dinamicamente un repository e quindi programmiamo il repository con un'aspettativa. L'aspettativa indica all'oggetto fittizio di restituire un nuovo oggetto dipendente con un valore Id pari a 5 quando un utente richiama il metodo FindById passando un valore pari a 5. Questo test viene superato e non è necessario creare&lt;un'implementazione completa per falsificare IRepository T&gt;.

Rivediamo i test che abbiamo scritto in precedenza e li riproviamo per usare i beffe invece dei falsi. Proprio come prima, useremo una classe di base per configurare le parti comuni dell'infrastruttura necessarie per tutti i test del controller.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

Il codice di installazione rimane per lo più lo stesso. Invece di usare i falsi, useremo Moq per costruire oggetti fittizi. La classe base organizza per l'unità di lavoro fittizia per restituire un repository fittizio quando il codice richiama il Employees proprietà. Il resto della configurazione fittizia avrà luogo all'interno dei dispositivi di test dedicati a ogni scenario specifico. Ad esempio, il dispositivo di test per l'azione Indice imposta il repository fittizio per restituire un elenco di dipendenti quando l'azione richiama il metodo FindAll del repository fittizio.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

Fatta eccezione per le aspettative, i nostri test sembrano simili ai test che avevamo prima. Tuttavia, con la capacità di registrazione di un framework fittizio possiamo affrontare il test da un'angolazione diversa. Esamineremo questa nuova prospettiva nella sezione successiva.

### <a name="state-versus-interaction-testing"></a>Test di stato e interazione

Esistono diverse tecniche che è possibile utilizzare per testare il software con oggetti fittizi. Un approccio consiste nell'utilizzare test basati sullo stato, che è quello che abbiamo fatto finora in questo articolo. I test basati sullo stato fanno asserzioni sullo stato del software. Nell'ultimo test abbiamo richiamato un metodo di azione sul controller e fatto un'asserzione sul modello che deve compilare. Di seguito sono riportati alcuni altri esempi di stato di test:Here are some other examples of testing state:

-   Verificare che il repository contenga il nuovo oggetto dipendente dopo l'esecuzione di Create.
-   Verificare che il modello contenga un elenco di tutti i dipendenti dopo l'esecuzione dell'indice.
-   Verificare che il repository non contenga un determinato dipendente dopo l'esecuzione di Delete.

Un altro approccio che si vedrà con gli oggetti fittizi consiste nel verificare *le interazioni*. Mentre il test basato sullo stato crea asserzioni sullo stato degli oggetti, il test basato sull'interazione fa sapere come gli oggetti interagiscono. Ad esempio:

-   Verificare che il controller richiami il metodo Add del repository durante l'esecuzione di Create.
-   Verificare che il controller richiami il metodo FindAll del repository durante l'esecuzione di Index.
-   Verificare che il controller richiami l'unità di lavoro Commit metodo per salvare le modifiche durante l'esecuzione di Edit.

I test di interazione spesso richiedono meno dati di test, perché non stiamo frugando all'interno delle raccolte e la verifica dei conteggi. Ad esempio, se si conosce l'azione Dettagli richiama il metodo FindById di un repository con il valore corretto, è probabile che l'azione si comporti correttamente. È possibile verificare questo comportamento senza impostare i dati di test da restituire da FindById.We can verify this behavior without setting up any test data to return from FindById.

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

L'unica configurazione richiesta nel dispositivo di test precedente è l'impostazione fornita dalla classe base. Quando richiamiamo l'azione del controller, Moq registrerà le interazioni con il repository fittizio. Usando l'API di verifica di Moq, possiamo chiedere a Moq se il controller ha richiamato FindById con il valore dell'ID corretto. Se il controller non ha richiamato il metodo o ha richiamato il metodo con un valore di parametro imprevisto, il metodo Verify genererà un'eccezione e il test avrà esito negativo.

Ecco un altro esempio per verificare che l'azione Create richiami Commit sull'unità di lavoro corrente.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Un pericolo con il test di interazione è la tendenza a specificare eccessivamente le interazioni. La capacità dell'oggetto fittizio di registrare e verificare ogni interazione con l'oggetto fittizio non significa che il test debba tentare di verificare ogni interazione. Alcune interazioni sono dettagli di implementazione ed è necessario verificare solo le interazioni *necessarie* per soddisfare il test corrente.

La scelta tra simulazioni o falsi dipende in gran parte dal sistema che stai testando e dalle tue preferenze personali (o di squadra). Gli oggetti fittizi possono ridurre drasticamente la quantità di codice necessaria per implementare il doppio dei test, ma non tutti sono a proprio agio nelle aspettative di programmazione e nella verifica delle interazioni.

## <a name="conclusions"></a>Conclusioni

In questo articolo sono stati illustrati diversi approcci alla creazione di codice tepossibile durante l'utilizzo del ADO.NET Entity Framework per la persistenza dei dati. Possiamo sfruttare le astrazioni incorporate&lt;come&gt;IObjectSet T , o creare&lt;&gt;le proprie astrazioni come IRepository T .In entrambi i casi, il supporto POCO nell'ADO.NET Entity Framework 4.0 consente ai consumer di queste astrazioni di rimanere persistenti ignoranti e altamente teabili. Funzionalità aggiuntive di EF4, ad esempio il caricamento lazy implicito, il codice del servizio business e dell'applicazione funziona senza preoccuparsi dei dettagli di un Data Store relazionale. Infine, le astrazioni che creiamo sono facili da simulare o falsificare all'interno degli unit test e possiamo usare questi test doppi per ottenere test veloci, altamente isolati e affidabili.

### <a name="additional-resources"></a>Risorse aggiuntive

-   Robert C. Martin, " Il principio di [responsabilità unica](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler, [catalogo di pattern](https://www.martinfowler.com/eaaCatalog/index.html) da modelli di architettura di applicazioni *aziendali*
-   Griffin Caprio, " [Iniezione](https://msdn.microsoft.com/magazine/cc163739.aspx)di dipendenza "
-   Blog sulla programmabilità dei dati, " [Procedura dettagliata: test driveed development con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Blog sulla programmabilità dei dati, " [Utilizzo dei modelli di repository e unità di lavoro con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, " [Introduzione alle specifiche della macchina](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee, " [BDD con MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, " [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biografia

Scott Allen è un membro dello staff tecnico di Pluralsight e il fondatore di OdeToCode.com. In 15 anni di sviluppo software commerciale, Scott ha lavorato su soluzioni per tutto, dai dispositivi embedded a 8 bit alle applicazioni web ASP.NET altamente scalabili. È possibile raggiungere Scott sul suo blog a OdeToCode, o su Twitter a [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).
