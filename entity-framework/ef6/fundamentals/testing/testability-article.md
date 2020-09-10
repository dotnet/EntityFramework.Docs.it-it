---
title: Testabilità e Entity Framework 4,0-EF6
description: Testabilità e Entity Framework 4,0
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: e1ded772bfee4f1870a891eaa525b33da8e9d3bc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618351"
---
# <a name="testability-and-entity-framework-40"></a>Testabilità e Entity Framework 4,0
Scott Allen

Data di pubblicazione: maggio 2010

## <a name="introduction"></a>Introduzione

In questa white paper viene descritto e illustrato come scrivere codice testabile con ADO.NET Entity Framework 4,0 e Visual Studio 2010. Questo documento non tenta di concentrarsi su una metodologia di test specifica, ad esempio la progettazione basata su test (TDD) o la progettazione basata sul comportamento (BDD). Questo documento è invece incentrato sulla scrittura di codice che usa il Entity Framework ADO.NET, ma rimane facile da isolare e testare in modo automatico. Verranno esaminati i modelli di progettazione comuni che semplificano i test negli scenari di accesso ai dati e viene illustrato come applicare tali modelli quando si usa il Framework. Verranno inoltre esaminate le funzionalità specifiche del Framework per vedere come tali funzionalità possono essere utilizzate nel codice testabile.

## <a name="what-is-testable-code"></a>Che cos'è il codice testabile?

La possibilità di verificare un componente software con unit test automatizzati offre molti vantaggi desiderati. Tutti sanno che i test corretti ridurranno il numero di difetti software in un'applicazione e aumenteranno la qualità dell'applicazione, ma la presenza di unit test non sarà più sufficiente per individuare i bug.

Una suite di unit test efficace consente a un team di sviluppo di risparmiare tempo e mantenere il controllo del software creato. Un team può apportare modifiche al codice esistente, effettuare il refactoring, riprogettare e ristrutturare il software per soddisfare i nuovi requisiti e aggiungere nuovi componenti in un'applicazione, pur sapendo che il gruppo di test può verificare il comportamento dell'applicazione. Gli unit test fanno parte di un rapido ciclo di feedback per facilitare le modifiche e mantenere la gestibilità del software Man mano che aumenta la complessità.

Il testing unità presenta tuttavia un prezzo. Un team deve investire il tempo necessario per creare e gestire gli unit test. La quantità di lavoro richiesto per creare questi test è direttamente correlata alla **testabilità** del software sottostante. Quanto è facile testare il software? Un team che progetta software con la testabilità può creare test efficaci più velocemente rispetto al team che lavora con software non testabile.

Microsoft ha progettato ADO.NET Entity Framework 4,0 (EF4) con la testabilità. Ciò non significa che gli sviluppatori scriveranno unit test sul codice del Framework stesso. Gli obiettivi di testabilità per EF4 semplificano invece la creazione di codice testabile basato sul Framework. Prima di esaminare esempi specifici, è utile comprendere le qualità del codice testabile.

### <a name="the-qualities-of-testable-code"></a>Qualità del codice testabile

Il codice di facile testing mostrerà sempre almeno due tratti. Per prima cosa, è facile **osservare**il codice testabile. Dato un set di input, dovrebbe essere facile osservare l'output del codice. Il test del metodo seguente, ad esempio, è semplice perché il metodo restituisce direttamente il risultato di un calcolo.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Il test di un metodo è difficile se il metodo scrive il valore calcolato in un socket di rete, in una tabella di database o in un file come il codice seguente. Il test deve eseguire operazioni aggiuntive per recuperare il valore.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

In secondo luogo, è facile **isolare**il codice testabile. Si userà lo pseudo-codice seguente come esempio errato di codice testabile.

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

Il metodo è facile da osservare: è possibile passare un criterio assicurativo e verificare che il valore restituito corrisponda a un risultato previsto. Tuttavia, per testare il metodo, è necessario disporre di un database installato con lo schema corretto e configurare il server SMTP se il metodo tenta di inviare un messaggio di posta elettronica.

Il unit test desidera solo verificare la logica di calcolo all'interno del metodo, ma il test potrebbe non riuscire perché il server di posta elettronica è offline o perché il server di database è stato spostato. Entrambi questi errori non sono correlati al comportamento che il test vuole verificare. Il comportamento è difficile da isolare.

Gli sviluppatori di software che tentano di scrivere codice testabile si impegnano spesso a mantenere una separazione delle problematiche nel codice che scrivono. Il metodo precedente dovrebbe concentrarsi sui calcoli aziendali e delegare i dettagli di implementazione del database e della posta elettronica ad altri componenti. Robert C. Martin chiama questo principio di singola responsabilità. Un oggetto deve incapsulare una singola responsabilità limitata, ad esempio il calcolo del valore di un criterio. Tutte le altre operazioni di database e di notifica dovrebbero essere responsabilità di un altro oggetto. Il codice scritto in questo modo è più semplice da isolare perché si concentra su una singola attività.

In .NET abbiamo le astrazioni necessarie per seguire il principio di singola responsabilità e ottenere l'isolamento. È possibile usare le definizioni di interfaccia e forzare il codice a usare l'astrazione dell'interfaccia anziché un tipo concreto. Più avanti in questo articolo verrà illustrato come un metodo come l'esempio non valido presentato sopra possa funzionare con le *interfacce che* comunicano con il database. In fase di test, tuttavia, è possibile sostituire un'implementazione fittizia che non comunica con il database ma che contenga i dati in memoria. Questa implementazione fittizia isola il codice da problemi non correlati nel codice di accesso ai dati o nella configurazione del database.

L'isolamento presenta vantaggi aggiuntivi. Il calcolo dell'attività nell'ultimo metodo deve richiedere solo pochi millisecondi, ma è possibile che il test venga eseguito per diversi secondi, in quanto il codice viene hop in rete e comunica con diversi server. Gli unit test devono essere eseguiti rapidamente per facilitare le piccole modifiche. Gli unit test devono essere anche ripetibili e non hanno esito negativo perché un componente non correlato al test presenta un problema. La scrittura di codice facile da osservare e per isolare significa che gli sviluppatori avranno più tempo per scrivere test per il codice, dedicare meno tempo ad attendere l'esecuzione dei test e, più importante, dedicare meno tempo alla verifica dei bug che non esistono.

È auspicabile apprezzare i vantaggi dei test e comprendere le qualità esposte dal codice testabile. Si sta per risolvere il modo in cui scrivere il codice che funziona con EF4 per salvare i dati in un database, rimanendo osservabili e facili da isolare, ma prima di tutto verrà ristretta la nostra attenzione per discutere delle progettazioni testabili per l'accesso ai dati.

## <a name="design-patterns-for-data-persistence"></a>Modelli di progettazione per la persistenza dei dati

Entrambi gli esempi non validi presentati in precedenza avevano troppe responsabilità. Il primo esempio non valido doveva eseguire un calcolo *e* scrivere in un file. Il secondo esempio non valido doveva leggere i dati da un database *ed* eseguire un calcolo aziendale *e* inviare messaggi di posta elettronica. Progettando metodi più piccoli che separano le problematiche e delegano la responsabilità ad altri componenti, potrai compiere grandi progressi per la scrittura di codice testabile. Lo scopo è quello di compilare le funzionalità componendo azioni da piccole e mirate astrazioni.

Per quanto riguarda la persistenza dei dati, le astrazioni piccole e mirate che stiamo cercando sono così comuni che sono state documentate come modelli di progettazione. I modelli di libro dell'architettura delle applicazioni aziendali di Martin Fowler sono il primo lavoro per descrivere questi modelli in stampa. Verrà fornita una breve descrizione di questi modelli nelle sezioni seguenti prima di illustrare il modo in cui questi ADO.NET Entity Framework implementano e funzionano con tali modelli.

### <a name="the-repository-pattern"></a>Schema Repository

Fowler afferma un repository "media tra i livelli di dominio e di mapping dei dati usando un'interfaccia simile a una raccolta per accedere agli oggetti di dominio". L'obiettivo del modello di repository è quello di isolare il codice dalle minuzie di accesso ai dati e, come abbiamo visto, l'isolamento precedente è un tratto obbligatorio per la testabilità.

La chiave per l'isolamento è il modo in cui il repository espone oggetti usando un'interfaccia simile a una raccolta. La logica scritta per l'uso del repository non ha idea di come il repository materializza gli oggetti richiesti. Il repository potrebbe comunicare con un database o solo restituire oggetti da una raccolta in memoria. Tutto il codice deve essere in grado di verificare che il repository sia in grado di gestire la raccolta e di recuperare, aggiungere ed eliminare oggetti dalla raccolta.

Nelle applicazioni .NET esistenti un repository concreto eredita spesso da un'interfaccia generica come la seguente:

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Verranno apportate alcune modifiche alla definizione dell'interfaccia quando si fornisce un'implementazione per EF4, ma il concetto di base rimane lo stesso. Il codice può usare un repository concreto che implementa questa interfaccia per recuperare un'entità in base al valore di chiave primaria, per recuperare una raccolta di entità in base alla valutazione di un predicato o semplicemente recuperare tutte le entità disponibili. Il codice può anche aggiungere e rimuovere entità tramite l'interfaccia del repository.

Dato un IRepository di oggetti Employee, il codice può eseguire le operazioni seguenti.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Poiché il codice usa un'interfaccia (IRepository of Employee), è possibile fornire il codice con implementazioni diverse dell'interfaccia. Un'implementazione potrebbe essere un'implementazione supportata da EF4 e salvare in modo permanente gli oggetti in un database Microsoft SQL Server. Un'implementazione diversa (uno usato durante il test) potrebbe essere supportata da un elenco in memoria di oggetti Employee. L'interfaccia consente di ottenere l'isolamento nel codice.

Si noti che l' &lt; interfaccia IRepository T non &gt; espone un'operazione di salvataggio. Come si aggiornano gli oggetti esistenti? È possibile che si trovino le definizioni IRepository che includono l'operazione di salvataggio e le implementazioni di questi repository dovranno immediatamente salvare in modo permanente un oggetto nel database. In molte applicazioni, tuttavia, non si desidera salvare in modo permanente gli oggetti singolarmente. Al contrario, si vuole che gli oggetti vengano portati in vita, ad esempio da repository diversi, modificare tali oggetti come parte di un'attività di business e quindi rendere permanente tutti gli oggetti come parte di una singola operazione atomica. Fortunatamente, esiste un modello per consentire questo tipo di comportamento.

### <a name="the-unit-of-work-pattern"></a>Modello di unità di lavoro

Fowler afferma che un'unità di lavoro "gestisce un elenco di oggetti interessati da una transazione aziendale e coordina la scrittura delle modifiche e la risoluzione dei problemi di concorrenza". È responsabilità dell'unità di lavoro tenere traccia delle modifiche apportate agli oggetti da un repository e rendere persistenti le modifiche apportate agli oggetti quando si indica all'unità di lavoro di eseguire il commit delle modifiche. È anche responsabilità dell'unità di lavoro prendere i nuovi oggetti aggiunti a tutti i repository e inserire gli oggetti in un database e anche l'eliminazione di Mange.

Se hai mai lavorato con i set di impostazioni di ADO.NET, avrai già familiarità con il modello di unità di lavoro. I set di dati ADO.NET hanno la possibilità di tenere traccia degli aggiornamenti, delle eliminazioni e dell'inserimento di oggetti DataRow e possono (con l'ausilio di un TableAdapter) riconciliare tutte le modifiche apportate a un database. Tuttavia, gli oggetti DataSet modellano un subset scollegato del database sottostante. Il modello di unità di lavoro presenta lo stesso comportamento, ma funziona con gli oggetti business e gli oggetti di dominio isolati dal codice di accesso ai dati e non è a conoscenza del database.

Un'astrazione per modellare l'unità di lavoro nel codice .NET potrebbe essere simile alla seguente:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Esponendo i riferimenti del repository dall'unità di lavoro, è possibile garantire che una singola unità di lavoro abbia la possibilità di tenere traccia di tutte le entità materializzate durante una transazione di business. L'implementazione del metodo commit per un'unità di lavoro reale è il punto in cui si verificano tutte le magie per riconciliare le modifiche in memoria con il database. 

Dato un riferimento IUnitOfWork, il codice può apportare modifiche agli oggetti business recuperati da uno o più repository e salvare tutte le modifiche usando l'operazione di commit Atomic.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Modello di carico Lazy

Fowler usa il nome Lazy Load per descrivere "un oggetto che non contiene tutti i dati necessari ma che sa come ottenerli". Il caricamento lazy trasparente è una funzionalità importante da usare quando si scrive codice aziendale testabile e si usa un database relazionale. Si consideri, ad esempio, il codice seguente.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Come viene popolata la raccolta cartellini? Esistono due possibili risposte. Una risposta è che il repository dei dipendenti, quando viene richiesto di recuperare un dipendente, emette una query per recuperare il dipendente insieme alle informazioni sulla scheda tempo associate del dipendente. Nei database relazionali questa operazione richiede in genere una query con una clausola JOIN e può determinare il recupero di più informazioni rispetto a quelle richieste da un'applicazione. Che cosa accade se l'applicazione non deve mai toccare la proprietà cartellini?

Una seconda risposta consiste nel caricare la proprietà cartellini "on demand". Questo caricamento lazy è implicito e trasparente alla logica di business, perché il codice non richiama API speciali per recuperare le informazioni sulla scheda tempo. Il codice presuppone che le informazioni sulle schede temporali siano presenti quando necessario. Il caricamento lazy presenta alcune magie che in genere comportano l'intercettazione in fase di esecuzione delle chiamate al metodo. Il codice di intercettazione è responsabile della comunicazione con il database e del recupero delle informazioni sulle schede temporali lasciando la logica di business gratuita per la logica di business. Questo Lazy Load Magic consente al codice aziendale di isolarsi da operazioni di recupero dei dati e di ottenere codice più testabile.

Lo svantaggio di un carico lazy è che, quando un' *applicazione necessita* delle informazioni sulle schede temporali, il codice eseguirà una query aggiuntiva. Questo non è un problema per molte applicazioni, ma per le applicazioni sensibili alle prestazioni o per le applicazioni che eseguono il ciclo di un numero di oggetti Employee e l'esecuzione di una query per recuperare le schede temporali durante ogni iterazione del ciclo (un problema spesso indicato come problema di query N + 1), il caricamento lazy è un trascinamento. In questi scenari, un'applicazione potrebbe voler caricare avidamente le informazioni sulle schede temporali nel modo più efficiente possibile.

Fortunatamente, si vedrà come EF4 supporta sia i carichi Lazy impliciti che i caricamenti eager efficienti quando si passa alla sezione successiva e si implementano questi schemi.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implementazione di modelli con il Entity Framework

La novità è che tutti i modelli di progettazione descritti nell'ultima sezione sono semplici da implementare con EF4. Per dimostrare che verrà usata una semplice applicazione MVC ASP.NET per modificare e visualizzare i dipendenti e le informazioni relative alle schede temporali associate. Si inizierà usando i seguenti "Plain Old CLR Objects" (POCO). 

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

Queste definizioni di classe cambiano leggermente quando si esplorano approcci e funzionalità diversi di EF4, ma lo scopo è quello di impedire al più possibile la persistenza di queste classi come la persistenza ignorata (PI). Un oggetto PI non sa *come*, o anche *se*, lo stato che possiede si trova all'interno di un database. PI e POCOs passano a mano con il software testabile. Gli oggetti che usano un approccio POCO sono meno vincolati, più flessibili e più semplici da testare, in quanto possono funzionare senza che sia presente un database.

Con i POCO disponibili è possibile creare un Entity Data Model (EDM) in Visual Studio (vedere la figura 1). Non utilizzeremo EDM per generare codice per le entità. Al contrario, si vogliono usare le entità in modo amorevole. Il modello EDM viene utilizzato solo per generare lo schema del database e fornire i metadati necessari per eseguire il mapping degli oggetti nel database EF4.

![test_01 EF](~/ef6/media/eftest-01.jpg)

**Figura 1**

Nota: se si desidera sviluppare prima il modello EDM, è possibile generare codice POCO pulito da EDM. A tale scopo, è possibile usare un'estensione di Visual Studio 2010 fornita dal team di programmabilità dei dati. Per scaricare l'estensione, avviare Gestione estensioni dal menu strumenti in Visual Studio ed eseguire una ricerca nella raccolta online di modelli per "POCO" (vedere la figura 2). Sono disponibili diversi modelli POCO per EF. Per ulteriori informazioni sull'utilizzo del modello, vedere " [procedura dettagliata: modello poco per il Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![test_02 EF](~/ef6/media/eftest-02.png)

**Figura 2**

Da questo punto di partenza POCO si analizzeranno due diversi approcci al codice testabile. Il primo approccio viene chiamato l'approccio EF perché sfrutta le astrazioni dall'API Entity Framework per implementare unità di lavoro e repository. Nel secondo approccio si creeranno le astrazioni del repository personalizzate e quindi si vedranno i vantaggi e gli svantaggi di ogni approccio. Si inizierà con l'esplorazione dell'approccio EF.  

### <a name="an-ef-centric-implementation"></a>Implementazione incentrata su EF

Si consideri l'azione del controller seguente da un progetto MVC ASP.NET. L'azione recupera un oggetto Employee e restituisce un risultato per visualizzare una visualizzazione dettagliata del dipendente.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Il codice è testabile? Sono necessari almeno due test per verificare il comportamento dell'azione. Prima di tutto, è necessario verificare che l'azione restituisca la visualizzazione corretta, ovvero un semplice test. Si vuole anche scrivere un test per verificare che l'azione recuperi il dipendente corretto e che si desideri eseguire questa operazione senza eseguire il codice per eseguire query sul database. Tenere presente che si vuole isolare il codice sottoposto a test. L'isolamento assicurerà che il test non riesca a causa di un bug nel codice di accesso ai dati o nella configurazione del database. Se il test ha esito negativo, è possibile che sia presente un bug nella logica del controller e non in un componente di sistema di livello inferiore.

Per ottenere l'isolamento, sono necessarie alcune astrazioni, come le interfacce presentate in precedenza per i repository e le unità di lavoro. Tenere presente che il modello di repository è progettato per mediare tra gli oggetti di dominio e il livello di mapping dei dati. In questo scenario EF4 *è* il livello di mapping dei dati e fornisce già un'astrazione di tipo repository denominata IObjectSet &lt; T &gt; (dallo spazio dei nomi System. Data. Objects). La definizione dell'interfaccia ha un aspetto simile al seguente.

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

IObjectSet &lt; t &gt; soddisfa i requisiti per un repository perché è simile a una raccolta di oggetti (tramite IEnumerable &lt; T &gt; ) e fornisce metodi per aggiungere e rimuovere oggetti dalla raccolta simulata. I metodi di collegamento e scollegamento espongono funzionalità aggiuntive dell'API EF4. Per usare IObjectSet &lt; T &gt; come interfaccia per i repository è necessaria un'astrazione di unità di lavoro per associare i repository.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Un'implementazione concreta di questa interfaccia comunicherà con SQL Server ed è facile da creare usando la classe ObjectContext da EF4. La classe ObjectContext è l'unità di lavoro reale nell'API EF4.

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

L'introduzione di un IObjectSet &lt; T &gt; è semplice come richiamare il Metodo CreateObjectSet dell'oggetto ObjectContext. Dietro le quinte il Framework utilizzerà i metadati forniti in EDM per produrre un oggetto concreto &lt; T &gt; . Si tratterà di restituire l' &lt; interfaccia IObjectSet T &gt; perché consente di mantenere la testabilità nel codice client.

Questa implementazione concreta è utile nell'ambiente di produzione, ma è necessario concentrarsi sull'uso dell'astrazione IUnitOfWork per semplificare i test.

### <a name="the-test-doubles"></a>Il test viene raddoppiato

Per isolare l'azione del controller, è necessario avere la possibilità di passare tra l'unità di lavoro reale (supportata da un ObjectContext) e un'unità di lavoro di tipo Double o "Fake" (esecuzione di operazioni in memoria). L'approccio comune per eseguire questo tipo di cambio consiste nel non consentire al controller MVC di creare un'istanza di un'unità di lavoro, ma di passare l'unità di lavoro al controller come parametro del costruttore.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Il codice precedente è un esempio di inserimento di dipendenze. Il controller non consente al controller di creare la dipendenza (unità di lavoro), ma di inserire la dipendenza nel controller. In un progetto MVC è comune usare una factory del controller personalizzata insieme a un contenitore di inversione del controllo (IoC) per automatizzare l'inserimento delle dipendenze. Questi argomenti esulano dall'ambito di questo articolo, ma è possibile ottenere altre informazioni seguendo i riferimenti alla fine di questo articolo.

Un'implementazione fittizia di un'unità di lavoro che è possibile usare per il test potrebbe essere simile alla seguente.

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

Si noti che l'unità di lavoro fittizia espone una proprietà sottoposta a commit. A volte è utile aggiungere funzionalità a una classe fittizia che facilita i test. In questo caso è facile osservare se il codice consente di eseguire il commit di un'unità di lavoro controllando la proprietà di cui è stato eseguito il commit.

È necessario anche un IObjectSet fittizio &lt; &gt; per mantenere gli oggetti Employee e cartellino in memoria. È possibile fornire una singola implementazione usando i generics.

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

Questo test doppia delega la maggior parte del lavoro a un &lt; oggetto HashSet T sottostante &gt; . Si noti che IObjectSet &lt; t &gt; richiede un vincolo generico che applica t come classe (un tipo di riferimento) e impone anche l'implementazione di IQueryable &lt; T &gt; . È facile fare in modo che una raccolta in memoria appaia come IQueryable &lt; T &gt; usando l'operatore LINQ standard AsQueryable.

### <a name="the-tests"></a>Test

Gli unit test tradizionali utilizzeranno una singola classe di test per conservare tutti i test per tutte le azioni in un singolo controller MVC. È possibile scrivere questi test, o qualsiasi tipo di unit test, usando i Fakes in memoria compilati. Tuttavia, per questo articolo si eviterà l'approccio della classe di test monolitico e si raggruppano i test per concentrarsi su un componente specifico di funzionalità.Ad esempio, "Crea nuovo dipendente" potrebbe essere la funzionalità che si desidera testare, quindi verrà utilizzata una singola classe di test per verificare l'azione del singolo controller responsabile della creazione di un nuovo dipendente.

Per tutte queste classi di test con granularità fine sono necessari alcuni codici di configurazione comuni. Ad esempio, è sempre necessario creare i repository in memoria e l'unità di lavoro falsa. È anche necessaria un'istanza del controller dipendente con l'unità di lavoro fittizia inserita. Questo codice di installazione comune verrà condiviso tra le classi di test usando una classe base.

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

Il "oggetto Mother" usato nella classe base è un modello comune per la creazione di dati di test. Un oggetto Mother contiene metodi factory per creare un'istanza delle entità di test da usare in più fixture di test.

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

È possibile usare EmployeeControllerTestBase come classe di base per un numero di fixture di test (vedere la figura 3). Ogni fixture di test eseguirà il test di un'azione specifica del controller. Una fixture di test, ad esempio, si concentra sul test dell'azione di creazione usata durante una richiesta HTTP GET (per visualizzare la visualizzazione per la creazione di un dipendente) e un'altra fixture si concentrerà sull'azione di creazione usata in una richiesta HTTP POST (per ottenere informazioni inviate dall'utente per creare un dipendente). Ogni classe derivata è responsabile solo per la configurazione necessaria nel contesto specifico e per fornire le asserzioni necessarie per verificare i risultati per il contesto di test specifico.

![test_03 EF](~/ef6/media/eftest-03.png)

**Figura 3**

La convenzione di denominazione e lo stile di test presentati qui non sono necessari per il codice testabile. si tratta solo di un approccio. La figura 4 illustra i test in esecuzione nel plug-in Test Runner di Jet Brains per Visual Studio 2010.

![test_04 EF](~/ef6/media/eftest-04.png)

**Figura 4**

Con una classe di base per gestire il codice di installazione condiviso, gli unit test per ogni azione del controller sono di piccole dimensioni e facili da scrivere. I test verranno eseguiti rapidamente (dal momento che si eseguono operazioni in memoria) e non dovrebbero avere esito negativo a causa dell'infrastruttura non correlata o delle preoccupazioni ambientali (perché l'unità sottoposta a test è isolata).

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

In questi test la classe base esegue la maggior parte delle operazioni di installazione. Tenere presente che il costruttore della classe base crea il repository in memoria, un'unità di lavoro fittizia e un'istanza della classe EmployeeController. La classe di test deriva da questa classe di base e si concentra sulle specifiche di test del metodo create. In questo caso, le specifiche si riducono ai passaggi "Arrange, Act e Assert" che verranno visualizzati in qualsiasi procedura di unit test:

-   Creare un oggetto newEmployee per simulare i dati in ingresso.
-   Richiamare l'azione di creazione di EmployeeController e passare newEmployee.
-   Verificare che l'azione di creazione produca i risultati previsti (il dipendente viene visualizzato nel repository).

Ciò che abbiamo creato ci permette di testare qualsiasi azione EmployeeController. Ad esempio, quando si scrivono test per l'azione index del controller Employee, è possibile ereditare dalla classe base di test per stabilire la stessa configurazione di base per i test. Anche in questo caso la classe base creerà il repository in memoria, l'unità di lavoro falsa e un'istanza di EmployeeController. I test per l'azione di indice devono essere concentrati solo sulla chiamata all'azione dell'indice e sul test delle qualità del modello restituito dall'azione.

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

I test creati con Fakes in memoria sono orientati a testare lo *stato* del software. Ad esempio, durante il test dell'azione di creazione si vuole controllare lo stato del repository dopo l'esecuzione dell'azione di creazione: il repository è in possesso del nuovo dipendente?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

In seguito verrà esaminato il test basato sull'interazione. Il test basato sull'interazione chiederà se il codice sottoposto a test ha richiamato i metodi appropriati sugli oggetti e ha passato i parametri corretti. Per il momento si passerà alla copertura di un altro modello di progettazione, ovvero il carico Lazy.

## <a name="eager-loading-and-lazy-loading"></a>Caricamento eager e caricamento lazy

A un certo punto dell'applicazione Web MVC ASP.NET, è possibile che si desideri visualizzare le informazioni di un dipendente e includere le schede temporali associate del dipendente. Ad esempio, è possibile che sia presente una visualizzazione Riepilogo della scheda tempo che mostra il nome del dipendente e il numero totale di schede temporali nel sistema. Per implementare questa funzionalità, è possibile adottare diversi approcci.

### <a name="projection"></a>Proiezione

Un semplice approccio per creare il riepilogo consiste nel costruire un modello dedicato alle informazioni che si desidera visualizzare nella visualizzazione. In questo scenario il modello potrebbe essere simile al seguente.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Si noti che EmployeeSummaryViewModel non è un'entità, in altre parole non è un elemento che si desidera salvare in modo permanente nel database. Questa classe verrà usata solo per eseguire il shuffling dei dati nella visualizzazione in modo fortemente tipizzato. Il modello di visualizzazione è simile a un oggetto di trasferimento dati (DTO) perché non contiene alcun comportamento (nessun metodo): solo proprietà. Le proprietà conterranno i dati che è necessario spostare. È facile creare un'istanza di questo modello di visualizzazione utilizzando l'operatore di proiezione standard di LINQ, ovvero l'operatore Select.

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

Sono disponibili due funzionalità rilevanti per il codice precedente. Primo: il codice è facile da testare perché è ancora facile da osservare e isolare. L'operatore Select funziona anche con i falsi in memoria, come avviene per l'unità di lavoro reale.

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

Il secondo aspetto rilevante è il modo in cui il codice consente a EF4 di generare una singola query efficiente per assemblare le informazioni sui dipendenti e sulle schede temporali insieme. Sono state caricate informazioni sui dipendenti e informazioni sulle schede temporali nello stesso oggetto senza usare API particolari. Il codice ha semplicemente espresso le informazioni necessarie usando gli operatori LINQ standard che funzionano con origini dati in memoria e origini dati remote. EF4 è stato in grado di convertire gli alberi delle espressioni generati dalla query LINQ e dal \# compilatore C in un'unica ed efficiente query T-SQL.

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

In altri casi non si vuole usare un modello di visualizzazione o un oggetto DTO, ma con entità reali. Quando sappiamo che abbiamo bisogno di un dipendente *e* delle schede temporali dei dipendenti, possiamo caricare con entusiasmo i dati correlati in modo non intrusivo ed efficiente.

### <a name="explicit-eager-loading"></a>Caricamento eager esplicito

Quando si desidera caricare in modo eager le informazioni sulle entità correlate, è necessario un meccanismo per la logica di business (o in questo scenario, la logica dell'azione del controller) per esprimere il proprio desiderio al repository. La classe EF4 ObjectQuery &lt; T &gt; definisce un metodo include per specificare gli oggetti correlati da recuperare durante una query. Tenere presente che l'ObjectContext EF4 espone le entità tramite la &lt; classe concrete t &gt; di oggetti che eredita da ObjectQuery &lt; t &gt; .Se si usano &lt; &gt; i riferimenti a ObjectSet T nell'azione del controller, è possibile scrivere il codice seguente per specificare un carico eager delle informazioni sulle schede temporali per ogni dipendente.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Tuttavia, poiché si sta tentando di evitare che il codice sia testabile, non è possibile esporre ObjectSet &lt; T &gt; dall'esterno della classe Real Unit of work. Si basa invece sull' &lt; interfaccia IObjectSet t &gt; , che è più semplice da falsificare, ma IObjectSet t non &lt; &gt; definisce un metodo di inclusione. La bellezza di LINQ è la possibilità di creare un proprio operatore di inclusione.

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

Si noti che l'operatore di inclusione è definito come metodo di estensione per IQueryable &lt; t &gt; anziché IObjectSet &lt; t &gt; . Questo consente di usare il metodo con una gamma più ampia di tipi possibili, tra cui IQueryable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; e ObjectSet t &lt; &gt; . Nel caso in cui la sequenza sottostante non sia un vero e proprio EF4 ObjectQuery &lt; T &gt; , non si verifica alcun danno e l'operatore di inclusione non è un'operazione. Se la sequenza sottostante *è* un oggetto ObjectQuery &lt; t &gt; (o derivato da OBJECTQUERY &lt; t &gt; ), EF4 visualizzerà il requisito per i dati aggiuntivi e formulerà la query SQL corretta.

Con questo nuovo operatore, è possibile richiedere in modo esplicito un carico eager delle informazioni sulle schede temporali dal repository.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Quando viene eseguito su un ObjectContext reale, il codice produce la seguente query singola. La query raccoglie informazioni sufficienti dal database in un unico viaggio per materializzare gli oggetti Employee e popolare completamente la relativa proprietà cartellini.

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

Il bel notizia è che il codice all'interno del metodo di azione rimane completamente testabile. Non è necessario fornire funzionalità aggiuntive per i Fakes per supportare l'operatore di inclusione. La cattiva notizia è che è stato necessario usare l'operatore di inclusione all'interno del codice che volevamo ignorare la persistenza. Questo è un esempio principale del tipo di compromessi che è necessario valutare quando si compila codice testabile. In alcuni casi è necessario lasciare che la persistenza riguardi la perdita al di fuori dell'astrazione del repository per soddisfare gli obiettivi delle prestazioni.

L'alternativa al caricamento eager è il caricamento lazy. Il caricamento lazy significa che il codice aziendale *non* è necessario per annunciare in modo esplicito il requisito per i dati associati. Vengono invece utilizzate le entità nell'applicazione e se sono necessari dati aggiuntivi Entity Framework caricherà i dati su richiesta.

### <a name="lazy-loading"></a>Caricamento lazy

È facile immaginare uno scenario in cui non si conoscono i dati necessari per una logica di business. È possibile che la logica necessiti di un oggetto dipendente, ma è possibile creare branch in percorsi di esecuzione diversi, in cui alcuni di questi percorsi richiedono informazioni sulle schede temporali del dipendente. Scenari come questo sono perfetti per il caricamento lazy implicito perché i dati vengono visualizzati magicamente in base alle esigenze.

Il caricamento lazy, anche noto come caricamento posticipato, comporta alcuni requisiti sugli oggetti entità. POCO con la vera ignoranza di persistenza non deve soddisfare i requisiti del livello di persistenza, ma la vera ignoranza persistenza è praticamente impossibile da raggiungere.Viene invece misurata l'ignoranza della persistenza nei gradi relativi. Sarebbe deplorevole se avessimo dovuto ereditare da una classe base orientata alla persistenza o usare una raccolta specializzata per ottenere il caricamento lazy in POCO. Fortunatamente, EF4 dispone di una soluzione meno intrusiva.

### <a name="virtually-undetectable"></a>Praticamente non rilevabile

Quando si usano oggetti POCO, EF4 può generare dinamicamente proxy di runtime per le entità. Questi proxy incapsulano in modo invisibile i POCO materializzati e forniscono servizi aggiuntivi intercettando ogni operazione get e set di proprietà per eseguire operazioni aggiuntive. Un servizio di questo tipo è la funzionalità di caricamento lazy che si sta cercando. Un altro servizio è un meccanismo efficiente di rilevamento delle modifiche che può registrare quando il programma modifica i valori delle proprietà di un'entità. L'elenco di modifiche viene utilizzato da ObjectContext durante il metodo SaveChanges per salvare in modo permanente tutte le entità modificate utilizzando i comandi UPDATE.

Per il funzionamento di questi proxy, tuttavia, è necessario un modo per associare le operazioni get e set di proprietà a un'entità e i proxy raggiungono questo obiettivo eseguendo l'override dei membri virtuali. Pertanto, se si desidera eseguire il caricamento lazy implicito e il rilevamento delle modifiche efficiente, è necessario tornare alle definizioni delle classi POCO e contrassegnare le proprietà come virtuali.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Possiamo comunque affermare che l'entità Employee è per lo più ignorante per la persistenza. L'unico requisito consiste nell'usare i membri virtuali e questa operazione non influisca sulla testabilità del codice. Non è necessario derivare da alcuna classe di base speciale o persino utilizzare una raccolta speciale dedicata al caricamento lazy. Come illustrato nel codice, qualsiasi classe che implementa ICollection &lt; T &gt; è disponibile per l'esecuzione di entità correlate.

È anche necessario apportare una piccola modifica all'interno dell'unità di lavoro. Il caricamento lazy è *disattivato* per impostazione predefinita quando si utilizza direttamente un oggetto ObjectContext. È possibile impostare una proprietà per la proprietà ContextOptions per abilitare il caricamento posticipato. è possibile impostare questa proprietà all'interno dell'unità di lavoro reale se si vuole abilitare il caricamento lazy ovunque.

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

Con il caricamento lazy implicito abilitato, il codice dell'applicazione può usare un dipendente e le schede temporali associate del dipendente, pur rimanendo a conoscenza del lavoro necessario per EF per caricare i dati aggiuntivi.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Il caricamento lazy rende più semplice la scrittura del codice dell'applicazione e, con il proxy magico, il codice rimane completamente testabile. I fake in memoria dell'unità di lavoro possono semplicemente precaricare entità fittizie con dati associati quando necessario durante un test.

A questo punto, l'attenzione verrà ricavata dalla creazione di repository con IObjectSet &lt; T &gt; e si osserveranno le astrazioni per nascondere tutti i segni del Framework di persistenza.

## <a name="custom-repositories"></a>Repository personalizzati

Quando è stato presentato per la prima volta il modello di progettazione unità di lavoro in questo articolo, è stato fornito un codice di esempio per l'aspetto dell'unità di lavoro. Verrà ripresentata questa idea originale usando lo scenario Employee e Employee Time Card che abbiamo lavorato.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

La differenza principale tra questa unità di lavoro e l'unità di lavoro creata nell'ultima sezione è il modo in cui questa unità di lavoro non usa astrazioni dal Framework EF4 (non esiste alcun IObjectSet &lt; T &gt; ). IObjectSet &lt; T &gt; funziona bene come un'interfaccia del repository, ma l'API che espone potrebbe non essere perfettamente allineata alle esigenze dell'applicazione. In questo prossimo approccio si rappresenteranno i repository usando un' &lt; astrazione IRepository T personalizzata &gt; .

Molti sviluppatori che seguono progettazione basata su test, progettazione basata su comportamenti e metodologie basate su dominio preferiscono &lt; l' &gt; approccio IRepository T per diversi motivi. In primo luogo, &lt; l' &gt; interfaccia IRepository T rappresenta un livello "anti-danneggiamento". Come descritto da Eric Evans nel suo libro di progettazione basato su dominio, un livello Anti-danneggiamento mantiene il codice del dominio dalle API dell'infrastruttura, ad esempio un'API di persistenza. In secondo luogo, gli sviluppatori possono compilare metodi nel repository che soddisfano le esigenze esatte di un'applicazione (come rilevato durante la scrittura dei test). Ad esempio, potrebbe essere necessario individuare spesso una singola entità usando un valore ID, quindi è possibile aggiungere un metodo FindById all'interfaccia del repository.&lt; &gt; La definizione di IRepository T sarà simile alla seguente.

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

Si noti che verrà eseguito il rollback dell'utilizzo di un' &lt; interfaccia IQueryable T &gt; per esporre le raccolte di entità. IQueryable &lt; T &gt; consente la propagazione degli alberi delle espressioni LINQ nel provider EF4 e fornisce al provider una visualizzazione olistica della query. Una seconda opzione consiste nel restituire IEnumerable &lt; T &gt; , il che significa che il provider LINQ EF4 visualizzerà solo le espressioni compilate all'interno del repository. Eventuali operazioni di raggruppamento, ordinamento e proiezione eseguite all'esterno del repository non verranno composte nel comando SQL inviato al database, il che può influire negativamente sulle prestazioni. D'altra parte, un repository che restituisce solo &lt; &gt; i risultati IEnumerable T non sorprenderà mai con un nuovo comando SQL. Entrambi gli approcci funzioneranno ed entrambi gli approcci rimarranno testabili.

È semplice fornire un'unica implementazione dell' &lt; interfaccia IRepository T &gt; usando i generics e l'API EF4 ObjectContext.

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

L' &lt; approccio IRepository T &gt; fornisce un controllo aggiuntivo sulle query perché un client deve richiamare un metodo per ottenere un'entità. All'interno del metodo è possibile fornire controlli aggiuntivi e operatori LINQ per applicare i vincoli dell'applicazione. Si noti che l'interfaccia presenta due vincoli per il parametro di tipo generico. Il primo vincolo è la classe const Taint required by ObjectSet &lt; T &gt; e il secondo vincolo impone alle entità di implementare IEntity, ovvero un'astrazione creata per l'applicazione. L'interfaccia IEntity impone alle entità di avere una proprietà ID leggibile ed è quindi possibile usare questa proprietà nel metodo FindById. IEntity è definito con il codice seguente.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity può essere considerato una piccola violazione dell'ignoranza di persistenza, poiché le entità sono necessarie per implementare questa interfaccia. Tenere presente che la persistenza dell'ignoranza riguarda i compromessi e per molte delle funzionalità di FindById supererà il vincolo imposto dall'interfaccia. L'interfaccia non ha alcun effetto sulla testabilità.

La creazione di un'istanza di un IRepository Live &lt; &gt; richiede un ObjectContext EF4, pertanto un'implementazione di un'unità di lavoro concreta deve gestire la creazione di istanze.

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

### <a name="using-the-custom-repository"></a>Uso del repository personalizzato

L'uso del repository personalizzato non è significativamente diverso dall'uso del repository basato su IObjectSet &lt; T &gt; . Anziché applicare gli operatori LINQ direttamente a una proprietà, è necessario prima richiamare uno dei metodi del repository per ottenere un &lt; riferimento IQueryable T &gt; .

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Si noti che l'operatore di inclusione personalizzato implementato in precedenza funzionerà senza alcuna modifica. Il metodo FindById del repository rimuove la logica duplicata dalle azioni che tentano di recuperare una singola entità.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Non esiste alcuna differenza significativa per la testabilità dei due approcci esaminati. Potremmo fornire implementazioni fittizie di IRepository &lt; T &gt; compilando classi concrete supportate dal &lt; dipendente di HashSet &gt; , proprio come quello che abbiamo fatto nell'ultima sezione. Tuttavia, alcuni sviluppatori preferiscono usare oggetti fittizi e Framework di oggetti fittizi anziché creare Fake. Si esaminerà l'uso di simulazioni per testare l'implementazione e discutere le differenze tra simulazioni e Fakes nella sezione successiva.

### <a name="testing-with-mocks"></a>Test con simulazioni

Esistono diversi approcci per la creazione di un "Double di test" di Martin Fowler. Un doppio di test (ad esempio una doppia acrobazia del film) è un oggetto compilato in "stand in" per gli oggetti di produzione reali durante i test. I repository in memoria creati sono test doppi per i repository che comunicano con SQL Server. Abbiamo visto come usare questi test-doppi durante gli unit test per isolare il codice e tenere i test eseguiti rapidamente.

I doppi test creati hanno implementazioni reali e funzionanti. Dietro le quinte viene archiviata una raccolta concreta di oggetti che aggiungono e rimuovono oggetti da questa raccolta quando si modifica il repository durante un test. Alcuni sviluppatori desiderano creare un test doppio in questo modo, con codice reale e implementazioni di lavoro.Questi doppi test sono quelli che chiamiamo *Fakes*. Hanno implementazioni di lavoro, ma non sono abbastanza reali per l'uso in produzione. Il repository Fake non esegue effettivamente la scrittura nel database. Il fake server SMTP non invia effettivamente un messaggio di posta elettronica tramite la rete.

### <a name="mocks-versus-fakes"></a>Simulazioni rispetto a Fakes

Esiste un altro tipo di test doppio noto come *simulazione*. Sebbene Fakes disponga di implementazioni di lavoro, le simulazioni sono prive di implementazione. Con l'ausilio di un Framework di oggetti fittizio è possibile costruire questi oggetti fittizi in fase di esecuzione e usarli come doppi di test. In questa sezione verrà usato il Framework di simulazione open source MOQ. Ecco un semplice esempio di uso di MOQ per creare dinamicamente un doppio di test per un repository Employee.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Per l'implementazione di un dipendente IRepository è richiesta la &lt; compilazione dinamica di un dipendente &gt; . È possibile ottenere l'oggetto che implementa il &lt; dipendente IRepository &gt; accedendo alla proprietà dell'oggetto dell'oggetto fittizio &lt; T &gt; . Si tratta di un oggetto interno che è possibile passare ai controller, che non sapranno se si tratta di un doppio test o di un repository reale. È possibile richiamare i metodi sull'oggetto esattamente come si richiamerebbe metodi su un oggetto con un'implementazione reale.

Quando viene richiamato il metodo Add, è necessario chiedersi quale sarà il repository fittizio. Poiché non esiste alcuna implementazione dietro l'oggetto fittizio, Add non esegue alcuna operazione. Non esiste alcuna raccolta concreta dietro le quinte, come abbiamo fatto con i Fakes scritti, quindi il dipendente viene eliminato. Per quanto riguarda il valore restituito di FindById? In questo caso, l'oggetto fittizio esegue l'unica operazione che può essere eseguita, che restituisce un valore predefinito. Poiché viene restituito un tipo di riferimento (un dipendente), il valore restituito è un valore null.

Le simulazioni potrebbero sembrare inutili; Tuttavia, esistono altre due funzionalità di simulazioni di cui non abbiamo parlato. In primo luogo, il Framework MOQ registra tutte le chiamate effettuate sull'oggetto fittizio. Più avanti nel codice è possibile richiedere MOQ se qualcuno ha richiamato il metodo Add o se chiunque ha richiamato il metodo FindById. In seguito verrà illustrato in che modo è possibile utilizzare la funzionalità di registrazione "black box" nei test.

La seconda grande funzionalità è il modo in cui è possibile usare MOQ per programmare un oggetto fittizio con le *aspettative*. Una previsione indica all'oggetto fittizio come rispondere a una determinata interazione. È possibile, ad esempio, programmare un'attesa nella simulazione e indicare che restituirà un oggetto dipendente quando un utente richiama FindById. Il Framework MOQ usa un'API di installazione e le espressioni lambda per programmare queste aspettative.

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

In questo esempio si chiede a MOQ di compilare dinamicamente un repository, quindi si programma il repository con una previsione. L'aspettativa indica all'oggetto fittizio di restituire un nuovo oggetto dipendente con un valore ID pari a 5 quando un utente richiama il metodo FindById passando un valore di 5. Questo test ha esito positivo e non è stato necessario creare un'implementazione completa di Fake IRepository &lt; t &gt; .

Esaminiamo nuovamente i test scritti in precedenza e li rielaborare per usare le simulazioni invece di Fakes. Proprio come in precedenza, si userà una classe base per configurare le parti comuni dell'infrastruttura necessarie per tutti i test del controller.

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

Il codice di installazione rimane lo stesso. Invece di usare Fakes, verrà usato MOQ per costruire oggetti fittizi. La classe base dispone che l'unità di lavoro fittizia restituisca un repository fittizio quando il codice richiama la proprietà Employees. Il resto dell'installazione fittizia verrà eseguita all'interno delle fixture di test dedicate a ogni scenario specifico. Ad esempio, la fixture di test per l'azione index configurerà il repository fittizio per restituire un elenco di dipendenti quando l'azione richiama il metodo FindAll del repository fittizio.

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

Ad eccezione delle aspettative, i test hanno un aspetto simile a quello dei test precedenti. Tuttavia, con la capacità di registrazione di un Framework fittizio è possibile affrontare i test da un angolo diverso. Questa nuova prospettiva verrà esaminata nella sezione successiva.

### <a name="state-versus-interaction-testing"></a>Stato rispetto al test di interazione

Esistono diverse tecniche che è possibile utilizzare per testare il software con oggetti fittizi. Un approccio consiste nell'usare il test basato sullo stato, che è quello che abbiamo fatto finora in questo articolo. Il test basato sullo stato esegue asserzioni sullo stato del software. Nell'ultimo test è stato richiamato un metodo di azione sul controller ed è stata creata un'asserzione sul modello da compilare. Ecco alcuni altri esempi di stato di test:

-   Verificare che il repository contenga il nuovo oggetto Employee dopo l'esecuzione di create.
-   Verificare che il modello contenga un elenco di tutti i dipendenti dopo l'esecuzione dell'indice.
-   Verificare che il repository non contenga un determinato dipendente dopo l'esecuzione dell'operazione Delete.

Un altro approccio che verrà visualizzato con oggetti fittizi consiste nel verificare le *interazioni*. Mentre il test basato sullo stato esegue asserzioni sullo stato degli oggetti, il test basato sull'interazione esegue asserzioni sulla modalità di interazione degli oggetti. Ad esempio:

-   Verificare che il controller richiami il metodo Add del repository quando viene eseguita l'istruzione create.
-   Verificare che il controller richiami il metodo FindAll del repository quando l'indice viene eseguito.
-   Verificare che il controller richiami il metodo commit dell'unità di lavoro per salvare le modifiche quando viene eseguita la modifica.

I test di interazione spesso richiedono un minor numero di dati di test, perché non viene eseguito il poke all'interno delle raccolte e la verifica dei conteggi. Ad esempio, se si sa che l'azione dettagli richiama il metodo FindById di un repository con il valore corretto, è probabile che l'azione si comportano correttamente. È possibile verificare questo comportamento senza configurare i dati di test da restituire da FindById.

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

L'unica configurazione richiesta nella fixture di test precedente è la configurazione fornita dalla classe base. Quando si richiama l'azione del controller, MOQ registrerà le interazioni con il repository fittizio. Usando l'API verify di MOQ, è possibile richiedere MOQ se il controller ha richiamato FindById con il valore ID appropriato. Se il controller non ha richiamato il metodo o ha richiamato il metodo con un valore di parametro imprevisto, il metodo Verify genererà un'eccezione e il test avrà esito negativo.

Di seguito è riportato un altro esempio per verificare che l'azione di creazione richiami commit sull'unità di lavoro corrente.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Un pericolo per i test di interazione è la tendenza a sovraspecificare le interazioni. La capacità dell'oggetto fittizio di registrare e verificare ogni interazione con l'oggetto fittizio non significa che il test deve provare a verificare ogni interazione. Alcune interazioni sono i dettagli di implementazione ed è necessario verificare solo le interazioni *necessarie* per soddisfare il test corrente.

La scelta tra simulazioni o fake dipende in gran parte dal sistema che si sta testando e dalle preferenze personali (o del team). Gli oggetti fittizi possono ridurre drasticamente la quantità di codice necessario per implementare i doppi test, ma non tutti sono le aspettative di programmazione e la verifica delle interazioni.

## <a name="conclusions"></a>Conclusioni

In questo articolo sono stati illustrati diversi approcci alla creazione di codice testabile quando si usa il Entity Framework ADO.NET per la persistenza dei dati. Possiamo sfruttare le astrazioni compilate come IObjectSet &lt; t &gt; o creare astrazioni personalizzate come IRepository &lt; t &gt; .In entrambi i casi, il supporto POCO in ADO.NET Entity Framework 4,0 consente agli utenti di queste astrazioni di rimanere ignoranti ed estremamente testabili. Funzionalità aggiuntive di EF4 come il caricamento lazy implicito consentono al codice del servizio aziendale e dell'applicazione di funzionare senza doversi preoccupare dei dettagli di un archivio dati relazionale. Infine, le astrazioni create sono facili da simulare o falsificare all'interno degli unit test e possono essere usate per ottenere test veloci, altamente isolati e affidabili.

### <a name="additional-resources"></a>Risorse aggiuntive

-   Robert C. Martin, [il principio di singola responsabilità](https://www.objectmentor.com/resources/articles/srp.pdf)
-   Martin Fowler, [Catalogo dei modelli](https://www.martinfowler.com/eaaCatalog/index.html) di *modelli di architettura delle applicazioni aziendali*
-   Griffin Caprio, " [inserimento delle dipendenze](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Blog di programmabilità dei dati, " [procedura dettagliata: sviluppo basato su test con la Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Blog di programmabilità dei dati, " [uso di repository e modelli di unità di lavoro con Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, " [Introduzione alle specifiche del computer](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee, " [BDD con MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans, " [progettazione basata su dominio](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, " [Mocks is Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler, " [test doppio](https://martinfowler.com/bliki/TestDouble.html)"
-   [MOQ](https://code.google.com/p/moq/)

### <a name="biography"></a>Biografia

Scott Allen è membro del personale tecnico di Pluralsight e fondatore di OdeToCode.com. In 15 anni di sviluppo di software commerciale, Scott ha lavorato alle soluzioni per tutto ciò che dai dispositivi embedded a 8 bit alle applicazioni Web ASP.NET altamente scalabili. È possibile raggiungere Scott sul suo Blog in OdeToCode o su Twitter all'indirizzo [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .
