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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="32005-102">Testabilità ed Entity Framework 4.0</span><span class="sxs-lookup"><span data-stu-id="32005-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="32005-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="32005-103">Scott Allen</span></span>

<span data-ttu-id="32005-104">Data di pubblicazione: maggio 2010</span><span class="sxs-lookup"><span data-stu-id="32005-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="32005-105">Introduzione</span><span class="sxs-lookup"><span data-stu-id="32005-105">Introduction</span></span>

<span data-ttu-id="32005-106">In questo white paper viene descritto e illustrato come scrivere codice teso con ADO.NET Entity Framework 4.0 e Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="32005-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="32005-107">Questo documento non cerca di concentrarsi su una metodologia di test specifica, come la progettazione basata su test (TDD) o la progettazione basata sul comportamento (BDD).</span><span class="sxs-lookup"><span data-stu-id="32005-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="32005-108">Questo documento si concentrerà invece su come scrivere codice che usa il ADO.NET Entity Framework ma rimane facile da isolare e testare in modo automatizzato.</span><span class="sxs-lookup"><span data-stu-id="32005-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="32005-109">Esamineremo i modelli di progettazione comuni che facilitano i test in scenari di accesso ai dati e vedere come applicare tali modelli quando si usa il framework.</span><span class="sxs-lookup"><span data-stu-id="32005-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="32005-110">Esamineremo anche le funzionalità specifiche del framework per vedere come tali funzionalità possono funzionare nel codice testrabile.</span><span class="sxs-lookup"><span data-stu-id="32005-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="32005-111">Che cos'è il codice teso?</span><span class="sxs-lookup"><span data-stu-id="32005-111">What Is Testable Code?</span></span>

<span data-ttu-id="32005-112">La possibilità di verificare un software utilizzando unit test automatizzati offre molti vantaggi desiderabili.</span><span class="sxs-lookup"><span data-stu-id="32005-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="32005-113">Tutti sanno che buoni test ridurranno il numero di difetti del software in un'applicazione e aumenteranno la qualità dell'applicazione, ma avere unit test in atto va ben oltre la semplice ricerca di bug.</span><span class="sxs-lookup"><span data-stu-id="32005-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="32005-114">Un buon gruppo di unit test consente a un team di sviluppo di risparmiare tempo e mantenere il controllo del software che crea.</span><span class="sxs-lookup"><span data-stu-id="32005-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="32005-115">Un team può apportare modifiche al codice esistente, effettuare il refactoring, riprogettare e ristrutturare il software per soddisfare i nuovi requisiti e aggiungere nuovi componenti in un'applicazione, conoscendo il gruppo di test in grado di verificare il comportamento dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="32005-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="32005-116">Gli unit test fanno parte di un rapido ciclo di feedback per facilitare il cambiamento e preservare la manutenibilità del software con l'aumentare della complessità.</span><span class="sxs-lookup"><span data-stu-id="32005-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="32005-117">Unit test viene fornito con un prezzo, tuttavia.</span><span class="sxs-lookup"><span data-stu-id="32005-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="32005-118">Un team deve investire il tempo necessario per creare e gestire unit test.</span><span class="sxs-lookup"><span data-stu-id="32005-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="32005-119">La quantità di sforzo necessaria per creare questi test è direttamente correlata alla **testabilità** del software sottostante.</span><span class="sxs-lookup"><span data-stu-id="32005-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="32005-120">Quanto è facile testare il software?</span><span class="sxs-lookup"><span data-stu-id="32005-120">How easy is the software to test?</span></span> <span data-ttu-id="32005-121">Un team che progetta software tenendo presente la testabilità creerà test efficaci più velocemente rispetto al team che lavora con un software non testabile.</span><span class="sxs-lookup"><span data-stu-id="32005-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="32005-122">Microsoft ha progettato il ADO.NET Entity Framework 4.0 (EF4) con la testabilità in mente.</span><span class="sxs-lookup"><span data-stu-id="32005-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="32005-123">Questo non significa che gli sviluppatori scriveranno unit test sul codice del framework stesso.</span><span class="sxs-lookup"><span data-stu-id="32005-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="32005-124">Al contrario, gli obiettivi di testabilità per EF4 semplificano la creazione di codice testrabile che si basa sul framework.</span><span class="sxs-lookup"><span data-stu-id="32005-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="32005-125">Prima di esaminare esempi specifici, vale la pena comprendere le qualità del codice testrabile.</span><span class="sxs-lookup"><span data-stu-id="32005-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="32005-126">Le qualità del codice testrabile</span><span class="sxs-lookup"><span data-stu-id="32005-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="32005-127">Il codice facile da testare presenterà sempre almeno due tratti.</span><span class="sxs-lookup"><span data-stu-id="32005-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="32005-128">In primo luogo, il codice testrabile è facile da **osservare**.</span><span class="sxs-lookup"><span data-stu-id="32005-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="32005-129">Dato un certo set di input, dovrebbe essere facile osservare l'output del codice.</span><span class="sxs-lookup"><span data-stu-id="32005-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="32005-130">Ad esempio, il test del metodo seguente è semplice perché il metodo restituisce direttamente il risultato di un calcolo.</span><span class="sxs-lookup"><span data-stu-id="32005-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="32005-131">Il test di un metodo è difficile se il metodo scrive il valore calcolato in un socket di rete, una tabella di database o un file come il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="32005-132">Il test deve eseguire operazioni aggiuntive per recuperare il valore.</span><span class="sxs-lookup"><span data-stu-id="32005-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="32005-133">In secondo luogo, il codice testrabile è facile da **isolare**.</span><span class="sxs-lookup"><span data-stu-id="32005-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="32005-134">Usiamo il seguente pseudo-codice come un cattivo esempio di codice testrabile.</span><span class="sxs-lookup"><span data-stu-id="32005-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="32005-135">Il metodo è facile da osservare : possiamo passare una polizza assicurativa e verificare che il valore di ritorno corrisponda a un risultato atteso.</span><span class="sxs-lookup"><span data-stu-id="32005-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="32005-136">Tuttavia, per testare il metodo è necessario disporre di un database installato con lo schema corretto e configurare il server SMTP nel caso in cui il metodo tenti di inviare un messaggio di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="32005-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="32005-137">Lo unit test desidera solo verificare la logica di calcolo all'interno del metodo, ma il test potrebbe non riuscire perché il server di posta elettronica non è in linea o perché il server di database è stato spostato.</span><span class="sxs-lookup"><span data-stu-id="32005-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="32005-138">Entrambi questi errori non sono correlati al comportamento che il test desidera verificare.</span><span class="sxs-lookup"><span data-stu-id="32005-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="32005-139">Il comportamento è difficile da isolare.</span><span class="sxs-lookup"><span data-stu-id="32005-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="32005-140">Gli sviluppatori di software che si sforzano di scrivere codice testrabile spesso si sforzano di mantenere una separazione delle preoccupazioni nel codice che scrivono.</span><span class="sxs-lookup"><span data-stu-id="32005-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="32005-141">Il metodo precedente deve concentrarsi sui calcoli aziendali e delegare i dettagli di implementazione del database e della posta elettronica ad altri componenti.</span><span class="sxs-lookup"><span data-stu-id="32005-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="32005-142">Robert C. Martin lo chiama principio di responsabilità unica.</span><span class="sxs-lookup"><span data-stu-id="32005-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="32005-143">Un oggetto deve incapsulare una singola responsabilità limitata, ad esempio il calcolo del valore di un criterio.</span><span class="sxs-lookup"><span data-stu-id="32005-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="32005-144">Tutti gli altri lavori di database e di notifica devono essere di competenza di un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="32005-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="32005-145">Il codice scritto in questo modo è più facile da isolare perché è incentrato su una singola attività.</span><span class="sxs-lookup"><span data-stu-id="32005-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="32005-146">In .NET abbiamo le astrazioni che dobbiamo seguire il principio di responsabilità singola e raggiungere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="32005-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="32005-147">Possiamo usare le definizioni di interfaccia e forzare il codice a usare l'astrazione dell'interfaccia anziché un tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="32005-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="32005-148">Più avanti in questo articolo vedremo come un metodo come l'esempio negativo presentato sopra può funzionare con interfacce che *sembrano* parlare con il database.</span><span class="sxs-lookup"><span data-stu-id="32005-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="32005-149">In fase di test, tuttavia, è possibile sostituire un'implementazione fittizia che non comunica con il database ma contiene invece i dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="32005-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="32005-150">Questa implementazione fittizia isolare il codice da problemi non correlati nel codice di accesso ai dati o nella configurazione del database.</span><span class="sxs-lookup"><span data-stu-id="32005-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="32005-151">L'isolamento offre ulteriori vantaggi.</span><span class="sxs-lookup"><span data-stu-id="32005-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="32005-152">Il calcolo aziendale nell'ultimo metodo dovrebbe richiedere solo pochi millisecondi per l'esecuzione, ma il test stesso potrebbe essere eseguito per alcuni secondi mentre il codice passa intorno alla rete e comunica con i vari server.</span><span class="sxs-lookup"><span data-stu-id="32005-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="32005-153">Gli unit test devono essere eseguiti rapidamente per facilitare piccole modifiche.</span><span class="sxs-lookup"><span data-stu-id="32005-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="32005-154">Gli unit test devono inoltre essere ripetibili e non avere esito negativo perché un componente non correlato al test presenta un problema.</span><span class="sxs-lookup"><span data-stu-id="32005-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="32005-155">La scrittura di codice facile da osservare e da isolare significa che gli sviluppatori avranno un tempo più facile scrivere test per il codice, dedicare meno tempo in attesa di test da eseguire e, cosa più importante, dedicare meno tempo a rintracciare i bug che non esistono.</span><span class="sxs-lookup"><span data-stu-id="32005-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="32005-156">Speriamo che tu possa apprezzare i vantaggi del test e comprendere le qualità che presentano il codice testuono.</span><span class="sxs-lookup"><span data-stu-id="32005-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="32005-157">Stiamo per capire come scrivere codice che funziona con EF4 per salvare i dati in un database pur rimanendo osservabile e facile da isolare, ma prima restringeremo il nostro obiettivo per discutere progetti testati per l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="32005-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="32005-158">Modelli di progettazione per la persistenza dei datiDesign Patterns for Data Persistence</span><span class="sxs-lookup"><span data-stu-id="32005-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="32005-159">Entrambi gli esempi negativi presentati in precedenza avevano troppe responsabilità.</span><span class="sxs-lookup"><span data-stu-id="32005-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="32005-160">Il primo esempio non valido ha dovuto eseguire un calcolo *e* scrivere in un file.</span><span class="sxs-lookup"><span data-stu-id="32005-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="32005-161">Il secondo esempio non valido è stato quello di leggere i dati da un database *ed* eseguire un calcolo aziendale *e* inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="32005-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="32005-162">Progettando metodi più piccoli che separano i problemi e delegano la responsabilità ad altri componenti, si farà grandi passi avanti verso la scrittura di codice testativo.</span><span class="sxs-lookup"><span data-stu-id="32005-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="32005-163">L'obiettivo è quello di creare funzionalità componendo azioni da astrazioni piccole e mirate.</span><span class="sxs-lookup"><span data-stu-id="32005-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="32005-164">Quando si tratta di persistenza dei dati le astrazioni piccole e focalizzate che stiamo cercando sono così comuni che sono stati documentati come modelli di progettazione.</span><span class="sxs-lookup"><span data-stu-id="32005-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="32005-165">Il libro Patterns of Enterprise Application Architecture di Martin Fowler è stato il primo lavoro a descrivere questi modelli in stampa.</span><span class="sxs-lookup"><span data-stu-id="32005-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="32005-166">Verrà fornita una breve descrizione di questi modelli nelle sezioni seguenti prima di illustrare come questi ADO.NET Entity Framework implementa e funziona con questi modelli.</span><span class="sxs-lookup"><span data-stu-id="32005-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="32005-167">Schema Repository</span><span class="sxs-lookup"><span data-stu-id="32005-167">The Repository Pattern</span></span>

<span data-ttu-id="32005-168">Fowler dice che un repository "media tra i livelli di mapping dei dati e del dominio utilizzando un'interfaccia simile a una raccolta per l'accesso agli oggetti di dominio".</span><span class="sxs-lookup"><span data-stu-id="32005-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="32005-169">L'obiettivo del modello di repository è isolare il codice dalle minuzie dell'accesso ai dati e, come abbiamo visto in precedenza, l'isolamento è un tratto necessario per la testabilità.</span><span class="sxs-lookup"><span data-stu-id="32005-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="32005-170">La chiave per l'isolamento è il modo in cui il repository espone gli oggetti utilizzando un'interfaccia di tipo raccolta.</span><span class="sxs-lookup"><span data-stu-id="32005-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="32005-171">La logica scritta per utilizzare il repository non ha idea di come il repository materializzerà gli oggetti che richiedi.</span><span class="sxs-lookup"><span data-stu-id="32005-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="32005-172">Il repository potrebbe comunicare con un database o potrebbe semplicemente restituire oggetti da una raccolta in memoria.</span><span class="sxs-lookup"><span data-stu-id="32005-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="32005-173">Tutto ciò che il codice deve sapere è che il repository viene visualizzato per gestire la raccolta ed è possibile recuperare, aggiungere ed eliminare oggetti dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="32005-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="32005-174">Nelle applicazioni .NET esistenti un repository concreto spesso eredita da un'interfaccia generica come la seguente:In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span><span class="sxs-lookup"><span data-stu-id="32005-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="32005-175">Apportiamo alcune modifiche alla definizione dell'interfaccia quando viene fornita un'implementazione per EF4, ma il concetto di base rimane lo stesso.</span><span class="sxs-lookup"><span data-stu-id="32005-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="32005-176">Il codice può usare un repository concreto che implementa questa interfaccia per recuperare un'entità in base al relativo valore di chiave primaria, per recuperare una raccolta di entità in base alla valutazione di un predicato o semplicemente recuperare tutte le entità disponibili.</span><span class="sxs-lookup"><span data-stu-id="32005-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="32005-177">Il codice può anche aggiungere e rimuovere entità tramite l'interfaccia del repository.</span><span class="sxs-lookup"><span data-stu-id="32005-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="32005-178">Dato un IRepository di Employee oggetti, codice può eseguire le seguenti operazioni.</span><span class="sxs-lookup"><span data-stu-id="32005-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="32005-179">Poiché il codice utilizza un'interfaccia (IRepository of Employee), è possibile fornire al codice diverse implementazioni dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32005-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="32005-180">Un'implementazione potrebbe essere un'implementazione supportata da EF4 e la persistenza degli oggetti in un database di Microsoft SQL Server.One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span><span class="sxs-lookup"><span data-stu-id="32005-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="32005-181">Un'implementazione diversa (una usata durante il test) potrebbe essere supportata da un elenco di oggetti Employee in memoria.</span><span class="sxs-lookup"><span data-stu-id="32005-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="32005-182">L'interfaccia consentirà di ottenere l'isolamento nel codice.</span><span class="sxs-lookup"><span data-stu-id="32005-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="32005-183">Si noti&lt;&gt; che l'interfaccia IRepository T non espone un'operazione di salvataggio.</span><span class="sxs-lookup"><span data-stu-id="32005-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="32005-184">Come si aggiornano gli oggetti esistenti?</span><span class="sxs-lookup"><span data-stu-id="32005-184">How do we update existing objects?</span></span> <span data-ttu-id="32005-185">Si potrebbe incontrare definizioni IRepository che includono il Save operazione e le implementazioni di questi repository sarà necessario rendere immediatamente persistente un oggetto nel database.</span><span class="sxs-lookup"><span data-stu-id="32005-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="32005-186">Tuttavia, in molte applicazioni non si desidera rendere persistenti gli oggetti singolarmente.</span><span class="sxs-lookup"><span data-stu-id="32005-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="32005-187">Al contrario, si desidera dare vita agli oggetti, ad esempio da repository diversi, modificare tali oggetti come parte di un'attività di business e quindi rendere persistenti tutti gli oggetti come parte di una singola operazione atomica.</span><span class="sxs-lookup"><span data-stu-id="32005-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="32005-188">Fortunatamente, esiste un modello per consentire questo tipo di comportamento.</span><span class="sxs-lookup"><span data-stu-id="32005-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="32005-189">L'unità di lavoro</span><span class="sxs-lookup"><span data-stu-id="32005-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="32005-190">Fowler dice che un'unità di lavoro "manterrà un elenco di oggetti interessati da una transazione aziendale e coordina la scrittura delle modifiche e la risoluzione dei problemi di concorrenza".</span><span class="sxs-lookup"><span data-stu-id="32005-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="32005-191">È responsabilità dell'unità di lavoro tenere traccia delle modifiche apportate agli oggetti che diamo vita da un repository e rendere persistenti le modifiche apportate agli oggetti quando diciamo all'unità di lavoro di eseguire il commit delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="32005-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="32005-192">È anche responsabilità dell'unità di lavoro prendere i nuovi oggetti che abbiamo aggiunto a tutti i repository e inserire gli oggetti in un database, none anche l'eliminazione della gestione.</span><span class="sxs-lookup"><span data-stu-id="32005-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="32005-193">Se hai mai eseguito un lavoro con ADO.NET DataSet, avrai già familiarità con l'unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="32005-194">ADO.NET DataSet di ADO.NET aveva la possibilità di tenere traccia di aggiornamenti, eliminazioni e inserimento di oggetti DataRow e potrebbe (con l'aiuto di un TableAdapter) riconciliare tutte le modifiche apportate a un database.</span><span class="sxs-lookup"><span data-stu-id="32005-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="32005-195">Tuttavia, gli oggetti DataSet modellano un subset disconnesso del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="32005-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="32005-196">L'unità di modello di lavoro presenta lo stesso comportamento, ma funziona con gli oggetti business e gli oggetti di dominio isolati dal codice di accesso ai dati e non a conoscenza del database.</span><span class="sxs-lookup"><span data-stu-id="32005-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="32005-197">Un'astrazione per modellare l'unità di lavoro nel codice .NET potrebbe essere simile alla seguente:An abstraction to model the unit of work in .NET code might look like the following:</span><span class="sxs-lookup"><span data-stu-id="32005-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="32005-198">Esponendo i riferimenti del repository dall'unità di lavoro possiamo garantire che una singola unità di oggetto di lavoro abbia la possibilità di tenere traccia di tutte le entità materializzate durante una transazione aziendale.</span><span class="sxs-lookup"><span data-stu-id="32005-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="32005-199">L'implementazione del metodo Commit per un'unità di lavoro reale è dove tutte le magie si verificano per riconciliare le modifiche in memoria con il database.</span><span class="sxs-lookup"><span data-stu-id="32005-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="32005-200">Dato un riferimento IUnitOfWork, il codice può apportare modifiche agli oggetti business recuperati da uno o più repository e salvare tutte le modifiche utilizzando l'operazione di commit atomica.</span><span class="sxs-lookup"><span data-stu-id="32005-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="32005-201">Il modello di carico lazyThe Lazy Load Pattern</span><span class="sxs-lookup"><span data-stu-id="32005-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="32005-202">Fowler usa il nome carico lazy per descrivere "un oggetto che non contiene tutti i dati necessari ma sa come ottenerli".</span><span class="sxs-lookup"><span data-stu-id="32005-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="32005-203">Il caricamento lazy trasparente è una funzionalità importante da avere quando si scrive codice aziendale tepossibile e si lavora con un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="32005-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="32005-204">Ad esempio, si consideri il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="32005-205">Come viene popolata la raccolta TimeCards?</span><span class="sxs-lookup"><span data-stu-id="32005-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="32005-206">Ci sono due possibili risposte.</span><span class="sxs-lookup"><span data-stu-id="32005-206">There are two possible answers.</span></span> <span data-ttu-id="32005-207">Una risposta è che l'archivio dei dipendenti, quando viene richiesto di recuperare un dipendente, invia una query per recuperare sia il dipendente che le informazioni della scheda oraria associate al dipendente.</span><span class="sxs-lookup"><span data-stu-id="32005-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="32005-208">Nei database relazionali questa operazione richiede in genere una query con una clausola JOIN e può comportare il recupero di più informazioni di quelle necessarie per un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="32005-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="32005-209">Cosa succede se l'applicazione non ha mai bisogno di toccare la proprietà TimeCards?</span><span class="sxs-lookup"><span data-stu-id="32005-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="32005-210">Una seconda risposta consiste nel caricare la proprietà TimeCards "su richiesta".</span><span class="sxs-lookup"><span data-stu-id="32005-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="32005-211">Questo caricamento lazy è implicito e trasparente per la logica di business perché il codice non richiama API speciali per recuperare le informazioni sulla scheda di tempo.</span><span class="sxs-lookup"><span data-stu-id="32005-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="32005-212">Il codice presuppone che le informazioni sulla scheda dipendente siano presenti quando necessario.</span><span class="sxs-lookup"><span data-stu-id="32005-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="32005-213">C'è una certa magia coinvolta con il caricamento lazy che generalmente comporta l'intercettazione di runtime delle chiamate al metodo.</span><span class="sxs-lookup"><span data-stu-id="32005-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="32005-214">Il codice di intercettazione è responsabile per parlare con il database e recuperare le informazioni della scheda dipendente orario, lasciando la logica di business libero di essere logica di business.</span><span class="sxs-lookup"><span data-stu-id="32005-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="32005-215">Questa magia di caricamento lazy consente al codice aziendale di isolarsi dalle operazioni di recupero dei dati e genera codice più teso.</span><span class="sxs-lookup"><span data-stu-id="32005-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="32005-216">Lo svantaggio di un carico lazy è che quando un'applicazione *ha* bisogno delle informazioni della scheda attività il codice eseguirà una query aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="32005-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="32005-217">Questo non è un problema per molte applicazioni, ma per le applicazioni sensibili alle prestazioni o applicazioni che eseguono il ciclo attraverso un numero di oggetti dipendente e l'esecuzione di una query per recuperare le schede temporali durante ogni iterazione del ciclo (un problema spesso indicato come il problema di query N , 1 è un trascinamento.</span><span class="sxs-lookup"><span data-stu-id="32005-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="32005-218">In questi scenari un'applicazione potrebbe voler caricare con entusiasmo le informazioni della scheda orario nel modo più efficiente possibile.</span><span class="sxs-lookup"><span data-stu-id="32005-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="32005-219">Fortunatamente, vedremo come EF4 supporta sia carichi lazy impliciti e carichi imposti efficienti come ci spostiamo nella sezione successiva e implementiamo questi modelli.</span><span class="sxs-lookup"><span data-stu-id="32005-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="32005-220">Implementazione di modelli con Entity FrameworkImplementing Patterns with the Entity Framework</span><span class="sxs-lookup"><span data-stu-id="32005-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="32005-221">La buona notizia è che tutti i modelli di progettazione descritti nell'ultima sezione sono semplici da implementare con EF4.</span><span class="sxs-lookup"><span data-stu-id="32005-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="32005-222">Per dimostrare che useremo una semplice applicazione MVC ASP.NET per modificare e visualizzare i dipendenti e le informazioni della scheda attività associata.</span><span class="sxs-lookup"><span data-stu-id="32005-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="32005-223">Inizieremo utilizzando i seguenti "oggetti CLR semplici vecchi" (PoCO).</span><span class="sxs-lookup"><span data-stu-id="32005-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="32005-224">Queste definizioni di classe cambieranno leggermente man mano che esploriamo diversi approcci e funzionalità di EF4, ma lo scopo è mantenere queste classi il più possibile ignorate di persistenza (PI).</span><span class="sxs-lookup"><span data-stu-id="32005-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="32005-225">Un oggetto PI non sa *come*, o anche *se*, lo stato che contiene vive all'interno di un database.</span><span class="sxs-lookup"><span data-stu-id="32005-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="32005-226">PI e POCO vanno di pari passo con un software teso.</span><span class="sxs-lookup"><span data-stu-id="32005-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="32005-227">Gli oggetti che utilizzano un approccio POCO sono meno vincolati, più flessibili e più facili da testare perché possono operare senza la presenza di un database.</span><span class="sxs-lookup"><span data-stu-id="32005-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="32005-228">Con i POCO sul posto è possibile creare un Entity Data Model (EDM) in Visual Studio (vedere figura 1).</span><span class="sxs-lookup"><span data-stu-id="32005-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="32005-229">Non useremo l'EDM per generare codice per le nostre entità.</span><span class="sxs-lookup"><span data-stu-id="32005-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="32005-230">Invece, vogliamo usare le entità che realizziamo con amore a mano.</span><span class="sxs-lookup"><span data-stu-id="32005-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="32005-231">Utilizzeremo solo EDM per generare lo schema del database e fornire i metadati necessari eF4 per eseguire il mapping degli oggetti nel database.</span><span class="sxs-lookup"><span data-stu-id="32005-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="32005-233">**Figura 1**</span><span class="sxs-lookup"><span data-stu-id="32005-233">**Figure 1**</span></span>

<span data-ttu-id="32005-234">Nota: se si desidera sviluppare prima il modello EDM, è possibile generare codice POCO pulito dall'EDM.</span><span class="sxs-lookup"><span data-stu-id="32005-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="32005-235">È possibile farlo con un'estensione di Visual Studio 2010 fornita dal team di programmabilità dei dati.</span><span class="sxs-lookup"><span data-stu-id="32005-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="32005-236">Per scaricare l'estensione, avviare Extension Manager dal menu Strumenti in Visual Studio e cercare "POCO" nella raccolta online di modelli (vedere figura 2).</span><span class="sxs-lookup"><span data-stu-id="32005-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="32005-237">Ci sono diversi modelli POCO disponibili per EF.</span><span class="sxs-lookup"><span data-stu-id="32005-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="32005-238">Per ulteriori informazioni sull'utilizzo del modello, vedere " [Procedura dettagliata: modello POCO per Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".</span><span class="sxs-lookup"><span data-stu-id="32005-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![test_02 ef](~/ef6/media/eftest-02.png)

<span data-ttu-id="32005-240">**Figura 2**</span><span class="sxs-lookup"><span data-stu-id="32005-240">**Figure 2**</span></span>

<span data-ttu-id="32005-241">Da questo punto di partenza POCO esploreremo due diversi approcci al codice testuabile.</span><span class="sxs-lookup"><span data-stu-id="32005-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="32005-242">Il primo approccio chiamo l'approccio Entity Framework perché sfrutta le astrazioni dall'API di Entity Framework per implementare unità di lavoro e repository.</span><span class="sxs-lookup"><span data-stu-id="32005-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="32005-243">Nel secondo approccio creeremo le nostre astrazioni di repository personalizzati e poi vedremo i vantaggi e gli svantaggi di ogni approccio.</span><span class="sxs-lookup"><span data-stu-id="32005-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="32005-244">Inizieremo esplorando l'approccio di EF.</span><span class="sxs-lookup"><span data-stu-id="32005-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="32005-245">Un'implementazione di EF Centric</span><span class="sxs-lookup"><span data-stu-id="32005-245">An EF Centric Implementation</span></span>

<span data-ttu-id="32005-246">Si consideri l'azione del controller seguente da un progetto MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="32005-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="32005-247">L'azione recupera un oggetto Employee e restituisce un risultato per visualizzare una visualizzazione dettagliata del dipendente.</span><span class="sxs-lookup"><span data-stu-id="32005-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="32005-248">Il codice è testata?</span><span class="sxs-lookup"><span data-stu-id="32005-248">Is the code testable?</span></span> <span data-ttu-id="32005-249">Ci sono almeno due test che avremmo bisogno di verificare il comportamento dell'azione.</span><span class="sxs-lookup"><span data-stu-id="32005-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="32005-250">In primo luogo, vorremmo verificare che l'azione restituisca la visualizzazione corretta - un test semplice.</span><span class="sxs-lookup"><span data-stu-id="32005-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="32005-251">Vorremmo anche scrivere un test per verificare che l'azione recuperi il dipendente corretto e vorremmo farlo senza eseguire codice per eseguire query sul database.</span><span class="sxs-lookup"><span data-stu-id="32005-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="32005-252">Ricorda che vogliamo isolare il codice sotto test.</span><span class="sxs-lookup"><span data-stu-id="32005-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="32005-253">L'isolamento garantirà che il test non abbia esito negativo a causa di un bug nel codice di accesso ai dati o nella configurazione del database.</span><span class="sxs-lookup"><span data-stu-id="32005-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="32005-254">Se il test ha esito negativo, sapremo che abbiamo un bug nella logica del controller e non in un componente di sistema di livello inferiore.</span><span class="sxs-lookup"><span data-stu-id="32005-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="32005-255">Per ottenere l'isolamento avremo bisogno di alcune astrazioni come le interfacce che abbiamo presentato in precedenza per i repository e le unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="32005-256">Tenere presente che il modello di repository è progettato per mediare tra gli oggetti di dominio e il livello di mapping dei dati.</span><span class="sxs-lookup"><span data-stu-id="32005-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="32005-257">In questo scenario EF4 *è* il livello di mapping dei dati e&lt;fornisce&gt; già un'astrazione simile a un repository denominata IObjectSet T (dallo spazio dei nomi System.Data.Objects ).</span><span class="sxs-lookup"><span data-stu-id="32005-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="32005-258">La definizione dell'interfaccia è simile alla seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="32005-259">IObjectSet&lt;&gt; T soddisfa i requisiti per un repository perché è simile&lt;&gt;a una raccolta di oggetti (tramite IEnumerable T ) e fornisce metodi per aggiungere e rimuovere oggetti dalla raccolta simulata.</span><span class="sxs-lookup"><span data-stu-id="32005-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="32005-260">I metodi Attach e Detach espongono funzionalità aggiuntive dell'API EF4.</span><span class="sxs-lookup"><span data-stu-id="32005-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="32005-261">Per utilizzare IObjectSet&lt;T&gt; come interfaccia per i repository è necessaria un'unità di astrazione del lavoro per associare i repository.</span><span class="sxs-lookup"><span data-stu-id="32005-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="32005-262">Un'implementazione concreta di questa interfaccia parlerà con SQL Server ed è facile da creare utilizzando la classe ObjectContext da EF4.</span><span class="sxs-lookup"><span data-stu-id="32005-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="32005-263">La classe ObjectContext è l'unità di lavoro reale nell'API EF4.</span><span class="sxs-lookup"><span data-stu-id="32005-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="32005-264">Dare&gt; vita a&lt;un oggetto T IObjectSet è facile come richiamare il metodo CreateObjectSet dell'oggetto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="32005-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="32005-265">Dietro le quinte il framework utilizzerà i metadati forniti nell'EDM per produrre un oggetto ObjectSet&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="32005-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="32005-266">Ci atterremo con la restituzione di IObjectSet&lt;T&gt; interfaccia perché contribuirà a preservare la testabilità nel codice client.</span><span class="sxs-lookup"><span data-stu-id="32005-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="32005-267">Questa implementazione concreta è utile nell'ambiente di produzione, ma è necessario concentrarsi su come useremo l'astrazione IUnitOfWork per facilitare il test.</span><span class="sxs-lookup"><span data-stu-id="32005-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="32005-268">Il test raddoppia</span><span class="sxs-lookup"><span data-stu-id="32005-268">The Test Doubles</span></span>

<span data-ttu-id="32005-269">Per isolare l'azione del controller è necessaria la possibilità di passare tra l'unità di lavoro reale (supportata da un ObjectContext) e un test doppio o "falso" unità di lavoro (esecuzione di operazioni in memoria).</span><span class="sxs-lookup"><span data-stu-id="32005-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="32005-270">L'approccio comune per eseguire questo tipo di commutazione consiste nel non consentire al controller MVC creare un'istanza di un'unità di lavoro, ma passare invece l'unità di lavoro nel controller come parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="32005-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="32005-271">Il codice precedente è un esempio di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="32005-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="32005-272">Non è consentito al controller di creare la dipendenza (l'unità di lavoro) ma inseriamo la dipendenza nel controller.</span><span class="sxs-lookup"><span data-stu-id="32005-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="32005-273">In un progetto MVC è comune usare una factory del controller personalizzata in combinazione con un contenitore di inversione del controllo (IoC) per automatizzare l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="32005-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="32005-274">Questi argomenti esulano dall'ambito di questo articolo, ma è possibile leggere di più seguendo i riferimenti alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="32005-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="32005-275">Un'unità di lavoro di implementazione fittizia che è possibile utilizzare per il test potrebbe essere simile alla seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="32005-276">Si noti che l'unità di lavoro fittizia espone una proprietà Commited.Notice the fake unit of work exposes a Commited property.</span><span class="sxs-lookup"><span data-stu-id="32005-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="32005-277">A volte è utile aggiungere funzionalità a una classe fittizia che facilitano il test.</span><span class="sxs-lookup"><span data-stu-id="32005-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="32005-278">In questo caso è facile osservare se il codice esegue il commit di un'unità di lavoro controllando il Commited proprietà.</span><span class="sxs-lookup"><span data-stu-id="32005-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="32005-279">Avremo anche bisogno di un&lt;falso&gt; IObjectSet T per tenere Employee e TimeCard oggetti in memoria.</span><span class="sxs-lookup"><span data-stu-id="32005-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="32005-280">Possiamo fornire una singola implementazione usando i generics.</span><span class="sxs-lookup"><span data-stu-id="32005-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="32005-281">Questo test raddoppia delega la maggior parte&lt;&gt; del proprio lavoro a un oggetto HashSet T sottostante.</span><span class="sxs-lookup"><span data-stu-id="32005-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="32005-282">Si noti&lt;che&gt; IObjectSet T richiede un vincolo generico che impone T come&lt;classe&gt;(un tipo di riferimento) e ci costringe anche a implementare IQueryable T .</span><span class="sxs-lookup"><span data-stu-id="32005-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="32005-283">È facile fare in modo che una raccolta&lt;in&gt; memoria venga visualizzata come un IQueryable T usando l'operatore LINQ standard AsQueryable.</span><span class="sxs-lookup"><span data-stu-id="32005-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="32005-284">I test</span><span class="sxs-lookup"><span data-stu-id="32005-284">The Tests</span></span>

<span data-ttu-id="32005-285">Gli unit test tradizionali utilizzeranno una singola classe di test per contenere tutti i test per tutte le azioni in un singolo controller MVC.</span><span class="sxs-lookup"><span data-stu-id="32005-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="32005-286">Possiamo scrivere questi test, o qualsiasi tipo di unit test, usando i falsi in memoria che abbiamo costruito.</span><span class="sxs-lookup"><span data-stu-id="32005-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="32005-287">Tuttavia, per questo articolo si eviterà l'approccio alla classe di test monolitico e raggruppare invece i test per concentrarsi su un pezzo specifico di funzionalità.</span><span class="sxs-lookup"><span data-stu-id="32005-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="32005-288">Ad esempio, "crea nuovo dipendente" potrebbe essere la funzionalità che si desidera testare, pertanto verrà utilizzata una singola classe di test per verificare l'azione del singolo controller responsabile della creazione di un nuovo dipendente.</span><span class="sxs-lookup"><span data-stu-id="32005-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="32005-289">C'è un codice di installazione comune che è necessario per tutte queste classi di test con granularità fine.</span><span class="sxs-lookup"><span data-stu-id="32005-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="32005-290">Ad esempio, abbiamo sempre bisogno di creare i nostri repository in memoria e unità di lavoro falso.</span><span class="sxs-lookup"><span data-stu-id="32005-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="32005-291">Abbiamo anche bisogno di un'istanza del controller dipendente con l'unità di lavoro falso iniettato.</span><span class="sxs-lookup"><span data-stu-id="32005-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="32005-292">Questo codice di installazione comune verrà condiviso tra le classi di test usando una classe base.</span><span class="sxs-lookup"><span data-stu-id="32005-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="32005-293">La "madre dell'oggetto" usata nella classe base è un modello comune per la creazione di dati di test.</span><span class="sxs-lookup"><span data-stu-id="32005-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="32005-294">Un madre oggetto contiene metodi factory per creare un'istanza di entità di test da utilizzare in più dispositivi di test.</span><span class="sxs-lookup"><span data-stu-id="32005-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="32005-295">È possibile utilizzare il EmployeeControllerTestBase come classe di base per un numero di impianti di test (vedere figura 3).</span><span class="sxs-lookup"><span data-stu-id="32005-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="32005-296">Ogni dispositivo di test testerà un'azione specifica del controller.</span><span class="sxs-lookup"><span data-stu-id="32005-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="32005-297">Ad esempio, un dispositivo di test si concentrerà sul test dell'azione Create utilizzata durante una richiesta HTTP GET (per visualizzare la visualizzazione per la creazione di un dipendente) e un dispositivo diverso si concentrerà sull'azione Crea utilizzata in una richiesta HTTP POST (per eseguire le informazioni inviate dall'utente per creare un dipendente).</span><span class="sxs-lookup"><span data-stu-id="32005-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="32005-298">Ogni classe derivata è responsabile solo dell'impostazione necessaria nel contesto specifico e di fornire le asserzioni necessarie per verificare i risultati per il contesto di test specifico.</span><span class="sxs-lookup"><span data-stu-id="32005-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![test_03 ef](~/ef6/media/eftest-03.png)

<span data-ttu-id="32005-300">**Figura 3**</span><span class="sxs-lookup"><span data-stu-id="32005-300">**Figure 3**</span></span>

<span data-ttu-id="32005-301">La convenzione di denominazione e lo stile di test presentati qui non sono necessari per il codice testrabile, ovvero un solo approccio.</span><span class="sxs-lookup"><span data-stu-id="32005-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="32005-302">Figura 4 Mostra i test in esecuzione nel plug-in test runner Jet Brains Resharper per Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="32005-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![test_04 ef](~/ef6/media/eftest-04.png)

<span data-ttu-id="32005-304">**Figura 4**</span><span class="sxs-lookup"><span data-stu-id="32005-304">**Figure 4**</span></span>

<span data-ttu-id="32005-305">Con una classe base per gestire il codice di installazione condiviso, gli unit test per ogni azione del controller sono piccoli e facili da scrivere.</span><span class="sxs-lookup"><span data-stu-id="32005-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="32005-306">I test verranno eseguiti rapidamente (poiché si eseguono operazioni in memoria) e non dovrebbero avere esito negativo a causa di problemi ambientali o infrastrutturali non correlati (perché abbiamo isolato l'unità sottoposta a test).</span><span class="sxs-lookup"><span data-stu-id="32005-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="32005-307">In questi test, la classe base esegue la maggior parte del lavoro di installazione.</span><span class="sxs-lookup"><span data-stu-id="32005-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="32005-308">Tenere presente che il costruttore della classe base crea l'archivio in memoria, un'unità di lavoro fittizia e un'istanza della classe EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="32005-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="32005-309">La classe di test deriva da questa classe di base e si concentra sulle specifiche del test del metodo Create.</span><span class="sxs-lookup"><span data-stu-id="32005-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="32005-310">In questo caso le specifiche si equivaliscono ai passaggi "organizzare, agire e asserire" che verrà visualizzata in qualsiasi procedura di unit test:</span><span class="sxs-lookup"><span data-stu-id="32005-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="32005-311">Creare un oggetto newEmployee per simulare i dati in ingresso.</span><span class="sxs-lookup"><span data-stu-id="32005-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="32005-312">Richiamare l'azione Create di EmployeeController e passare newEmployee.</span><span class="sxs-lookup"><span data-stu-id="32005-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="32005-313">Verificare che l'azione Crea produca i risultati previsti (il dipendente viene visualizzato nel repository).</span><span class="sxs-lookup"><span data-stu-id="32005-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="32005-314">Ciò che abbiamo creato ci permette di testare una qualsiasi delle azioni EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="32005-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="32005-315">Ad esempio, quando si scrivono test per l'azione indice del controller Employee è possibile ereditare dalla classe di base di test per stabilire la stessa impostazione di base per i test.</span><span class="sxs-lookup"><span data-stu-id="32005-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="32005-316">Anche in questo caso la classe di base creerà il repository in memoria, l'unità di lavoro fittizia e un'istanza di EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="32005-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="32005-317">I test per l'azione Indice devono solo concentrarsi sulla chiamata dell'azione Indice e sul test delle qualità del modello restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="32005-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="32005-318">I test che stiamo creando con i falsi in memoria sono orientati verso il test dello *stato* del software.</span><span class="sxs-lookup"><span data-stu-id="32005-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="32005-319">Ad esempio, quando si esegue il test dell'azione Crea, si desidera controllare lo stato del repository dopo l'esecuzione dell'azione di creazione: il repository contiene il nuovo dipendente?</span><span class="sxs-lookup"><span data-stu-id="32005-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="32005-320">In seguito esamineremo i test basati sull'interazione.</span><span class="sxs-lookup"><span data-stu-id="32005-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="32005-321">Test basati sull'interazione chiederanno se il codice sottoposto a test ha richiamato i metodi appropriati sui nostri oggetti e ha passato i parametri corretti.</span><span class="sxs-lookup"><span data-stu-id="32005-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="32005-322">Per ora ci sposteremo sulla copertina un altro modello di progettazione - il carico pigro.</span><span class="sxs-lookup"><span data-stu-id="32005-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="32005-323">Caricamento eager e caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="32005-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="32005-324">A un certo punto del ASP.NET applicazione Web MVC è possibile visualizzare le informazioni di un dipendente e includere le schede dipendente associate del dipendente.</span><span class="sxs-lookup"><span data-stu-id="32005-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="32005-325">Ad esempio, è possibile che nel sistema venga visualizzato un riepilogo scheda dipendente che mostra il nome del dipendente e il numero totale di schede attività nel sistema.</span><span class="sxs-lookup"><span data-stu-id="32005-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="32005-326">Esistono diversi approcci che è possibile adottare per implementare questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="32005-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="32005-327">Proiezione</span><span class="sxs-lookup"><span data-stu-id="32005-327">Projection</span></span>

<span data-ttu-id="32005-328">Un approccio semplice per creare il riepilogo consiste nel costruire un modello dedicato alle informazioni che si desidera visualizzare nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="32005-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="32005-329">In questo scenario il modello potrebbe essere simile al seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="32005-330">Si noti che il EmployeeSummaryViewModel non è un'entità – in altre parole non è qualcosa che si desidera rendere persistente nel database.</span><span class="sxs-lookup"><span data-stu-id="32005-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="32005-331">Useremo questa classe solo per mischiare i dati nella visualizzazione in modo fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="32005-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="32005-332">Il modello di visualizzazione è simile a un oggetto di trasferimento dati (DTO) perché non contiene alcun comportamento (nessun metodo) - solo proprietà.</span><span class="sxs-lookup"><span data-stu-id="32005-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="32005-333">Le proprietà conterranno i dati che dobbiamo spostare.</span><span class="sxs-lookup"><span data-stu-id="32005-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="32005-334">È facile creare un'istanza di questo modello di visualizzazione utilizzando l'operatore di proiezione standard di LINQ, ovvero l'operatore Select.It is easy to instantiate this view model using LINQ's standard projection operator – the Select operator.</span><span class="sxs-lookup"><span data-stu-id="32005-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="32005-335">Ci sono due caratteristiche notevoli per il codice sopra.</span><span class="sxs-lookup"><span data-stu-id="32005-335">There are two notable features to the above code.</span></span> <span data-ttu-id="32005-336">Primo: il codice è facile da testare perché è ancora facile da osservare e isolare.</span><span class="sxs-lookup"><span data-stu-id="32005-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="32005-337">L'operatore Select funziona altrettanto bene contro i nostri falsi in memoria come lo fa contro la vera unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="32005-338">La seconda caratteristica notevole è come il codice consente EF4 per generare una singola query efficiente per assemblare insieme le informazioni dipendente e scheda ora.</span><span class="sxs-lookup"><span data-stu-id="32005-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="32005-339">Abbiamo caricato le informazioni sui dipendenti e le informazioni sulla scheda attività nello stesso oggetto senza usare LE API speciali.</span><span class="sxs-lookup"><span data-stu-id="32005-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="32005-340">Il codice si limitava a esprimere le informazioni necessarie utilizzando operatori LINQ standard che funzionano con origini dati in memoria e origini dati remote.</span><span class="sxs-lookup"><span data-stu-id="32005-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="32005-341">EF4 è stato in grado di convertire le\# strutture ad albero dell'espressione generate dalla query LINQ e dal compilatore C in una query T-SQL singola ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="32005-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="32005-342">Ci sono altri momenti in cui non vogliamo lavorare con un modello di visualizzazione o un oggetto DTO, ma con entità reali.</span><span class="sxs-lookup"><span data-stu-id="32005-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="32005-343">Quando sappiamo che abbiamo bisogno di un dipendente *e* le schede temporali del dipendente, possiamo caricare con entusiasmo i dati correlati in modo discreto ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="32005-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="32005-344">Caricamento esplicito dei eagerExplicit Eager Loading</span><span class="sxs-lookup"><span data-stu-id="32005-344">Explicit Eager Loading</span></span>

<span data-ttu-id="32005-345">Quando si desidera caricare con entusiasmo le informazioni sulle entità correlate è necessario un meccanismo per la logica di business (o in questo scenario, la logica di azione del controller) per esprimere il proprio desiderio al repository.</span><span class="sxs-lookup"><span data-stu-id="32005-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="32005-346">La classe EF4 ObjectQuery&lt;T&gt; definisce un metodo Include per specificare gli oggetti correlati da recuperare durante una query.</span><span class="sxs-lookup"><span data-stu-id="32005-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="32005-347">Tenere presente che EF4 ObjectContext espone le&lt;&gt; entità tramite la&lt;classe&gt;T Oggetto ObjectSet concreta che eredita da ObjectQuery T .</span><span class="sxs-lookup"><span data-stu-id="32005-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="32005-348">Se si usassero&lt;&gt; riferimenti ObjectSet T nell'azione del controller, è possibile scrivere il codice seguente per specificare un carico entusiasta di informazioni sulla scheda oraria per ogni dipendente.</span><span class="sxs-lookup"><span data-stu-id="32005-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="32005-349">Tuttavia, poiché stiamo cercando di mantenere il codice testabili, non esponiamo ObjectSet&lt;T&gt; dall'esterno dell'unità reale della classe di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="32005-350">Invece, ci affidiamo&lt;&gt; all'interfaccia IObjectSet T che&lt;&gt; è più facile da falsificare, ma IObjectSet T non definisce un metodo Include.</span><span class="sxs-lookup"><span data-stu-id="32005-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="32005-351">La bellezza di LINQ è che possiamo creare il nostro operatore Include.</span><span class="sxs-lookup"><span data-stu-id="32005-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="32005-352">Si noti che questo operatore Include&lt;è&gt; definito come&lt;metodo&gt;di estensione per IQueryable T anziché IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="32005-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="32005-353">In questo modo è possibile utilizzare il metodo con una gamma&lt;&gt;più ampia&lt;di&gt;tipi&lt;possibili, tra cui IQueryable T , IObjectSet T , ObjectQuery T&gt;e ObjectSet&lt;T&gt;.</span><span class="sxs-lookup"><span data-stu-id="32005-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="32005-354">Nel caso in cui la sequenza sottostante&lt;&gt;non sia un oggetto EF4 ObjectQuery T autentico , non vi è alcun danno e l'operatore Include è un no-op.</span><span class="sxs-lookup"><span data-stu-id="32005-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="32005-355">Se la sequenza sottostante&gt; *è* un ObjectQuery&lt;T (o derivato da ObjectQuery&lt;T&gt;), EF4 visualizzerà il requisito per i dati aggiuntivi e formulare la query SQL corretta.</span><span class="sxs-lookup"><span data-stu-id="32005-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="32005-356">Con questo nuovo operatore sul posto possiamo richiedere esplicitamente un carico ansioso di informazioni sulla scheda orario dal repository.</span><span class="sxs-lookup"><span data-stu-id="32005-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="32005-357">Quando viene eseguito su un objectContext reale, il codice produce la query singola seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="32005-358">La query raccoglie informazioni sufficienti dal database in un unico viaggio per materializzare gli oggetti dipendente e popolare completamente la proprietà TimeCards.</span><span class="sxs-lookup"><span data-stu-id="32005-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="32005-359">La grande notizia è che il codice all'interno del metodo di azione rimane completamente teso.</span><span class="sxs-lookup"><span data-stu-id="32005-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="32005-360">Non abbiamo bisogno di fornire funzionalità aggiuntive per i nostri falsi per supportare l'operatore Include.</span><span class="sxs-lookup"><span data-stu-id="32005-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="32005-361">La cattiva notizia è che abbiamo dovuto usare l'operatore Include all'interno del codice che volevamo mantenere la persistenza ignorante.</span><span class="sxs-lookup"><span data-stu-id="32005-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="32005-362">Questo è un primo esempio del tipo di compromessi che è necessario valutare durante la compilazione di codice testrabile.</span><span class="sxs-lookup"><span data-stu-id="32005-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="32005-363">In alcuni casi è necessario lasciare che i problemi di persistenza perdano all'esterno dell'astrazione del repository per soddisfare gli obiettivi di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="32005-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="32005-364">L'alternativa al caricamento eager è il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="32005-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="32005-365">Il caricamento lazy significa che *non* è necessario il codice aziendale per annunciare in modo esplicito il requisito per i dati associati.</span><span class="sxs-lookup"><span data-stu-id="32005-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="32005-366">Al contrario, utilizziamo le entità nell'applicazione e se sono necessari dati aggiuntivi Entity Framework caricherà i dati su richiesta.</span><span class="sxs-lookup"><span data-stu-id="32005-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="32005-367">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="32005-367">Lazy Loading</span></span>

<span data-ttu-id="32005-368">È facile immaginare uno scenario in cui non sappiamo quali dati sarà necessario un pezzo di logica di business.</span><span class="sxs-lookup"><span data-stu-id="32005-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="32005-369">Potremmo sapere che la logica ha bisogno di un oggetto dipendente, ma possiamo diramare in percorsi di esecuzione diversi in cui alcuni di questi percorsi richiedono informazioni scheda dipendente dal dipendente, e alcuni non lo fanno.</span><span class="sxs-lookup"><span data-stu-id="32005-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="32005-370">Scenari come questo sono perfetti per il caricamento lazy implicito perché i dati vengono magicamente visualizzati in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="32005-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="32005-371">Il caricamento lazy, noto anche come caricamento posticipato, commette alcuni requisiti per gli oggetti entità.</span><span class="sxs-lookup"><span data-stu-id="32005-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="32005-372">I POCO con vera ignoranza di persistenza non sarebbero all'indirizzo di alcun requisito a causa dello strato di persistenza, ma la vera ignoranza della persistenza è praticamente impossibile da raggiungere.</span><span class="sxs-lookup"><span data-stu-id="32005-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="32005-373">Invece misuriamo l'ignoranza della persistenza in gradi relativi.</span><span class="sxs-lookup"><span data-stu-id="32005-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="32005-374">Sarebbe un peccato se avessimo bisogno di ereditare da una classe di base orientata alla persistenza o di usare una raccolta specializzata per ottenere il caricamento lazy nei POCO.</span><span class="sxs-lookup"><span data-stu-id="32005-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="32005-375">Fortunatamente, EF4 ha una soluzione meno intrusiva.</span><span class="sxs-lookup"><span data-stu-id="32005-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="32005-376">Praticamente inosservabile</span><span class="sxs-lookup"><span data-stu-id="32005-376">Virtually Undetectable</span></span>

<span data-ttu-id="32005-377">Quando si usano oggetti POCO, EF4 può generare dinamicamente proxy di runtime per le entità.</span><span class="sxs-lookup"><span data-stu-id="32005-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="32005-378">Questi proxy eseguono in modo invisibilmente il wrapping delle POCO materializzate e forniscono servizi aggiuntivi intercettando ogni operazione get e set di proprietà per eseguire ulteriori operazioni.</span><span class="sxs-lookup"><span data-stu-id="32005-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="32005-379">Uno di questi servizi è la funzione di caricamento lazy che stiamo cercando.</span><span class="sxs-lookup"><span data-stu-id="32005-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="32005-380">Un altro servizio è un meccanismo efficiente di rilevamento delle modifiche che può registrare quando il programma modifica i valori delle proprietà di un'entità.</span><span class="sxs-lookup"><span data-stu-id="32005-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="32005-381">L'elenco delle modifiche viene utilizzato da ObjectContext durante il SaveChanges metodo per rendere persistenti tutte le entità modificate utilizzando i comandi UPDATE.</span><span class="sxs-lookup"><span data-stu-id="32005-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="32005-382">Affinché questi proxy funzionino, tuttavia, è necessario un modo per eseguire l'hook nelle operazioni get e set di proprietà su un'entità e i proxy raggiungono questo obiettivo eseguendo l'override dei membri virtuali.</span><span class="sxs-lookup"><span data-stu-id="32005-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="32005-383">Pertanto, se vogliamo avere il caricamento lazy implicito e il monitoraggio efficiente delle modifiche, è necessario tornare alle nostre definizioni di classe POCO e contrassegnare le proprietà come virtuali.</span><span class="sxs-lookup"><span data-stu-id="32005-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="32005-384">Possiamo ancora dire che l'entità Employee è per lo più persistence ignorant.</span><span class="sxs-lookup"><span data-stu-id="32005-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="32005-385">L'unico requisito consiste nell'utilizzare membri virtuali e ciò non influisce sulla testabilità del codice.</span><span class="sxs-lookup"><span data-stu-id="32005-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="32005-386">Non è necessario derivare da alcuna classe di base speciale o anche utilizzare una raccolta speciale dedicata al caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="32005-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="32005-387">Come illustrato nel codice, qualsiasi&lt;&gt; classe che implementa ICollection T è disponibile per contenere le entità correlate.</span><span class="sxs-lookup"><span data-stu-id="32005-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="32005-388">C'è anche un piccolo cambiamento che dobbiamo fare all'interno della nostra unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="32005-389">Il caricamento lazy è disattivato per impostazione predefinita quando si lavora direttamente con un oggetto ObjectContext.Lazy loading is *off* by default when working directly with an ObjectContext object.</span><span class="sxs-lookup"><span data-stu-id="32005-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="32005-390">C'è una proprietà che possiamo impostare sul ContextOptions proprietà per abilitare il caricamento posticipato, e possiamo impostare questa proprietà all'interno della nostra unità di lavoro reale se vogliamo abilitare il caricamento lazy ovunque.</span><span class="sxs-lookup"><span data-stu-id="32005-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="32005-391">Con il caricamento lazy implicito abilitato, il codice dell'applicazione può utilizzare un dipendente e le schede orari associate del dipendente, pur rimanendo beatamente inconsapevoli del lavoro necessario per EF per caricare i dati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="32005-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="32005-392">Il caricamento lazy rende il codice dell'applicazione più facile da scrivere e con la magia del proxy il codice rimane completamente teso.</span><span class="sxs-lookup"><span data-stu-id="32005-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="32005-393">I falsi in memoria dell'unità di lavoro possono semplicemente precaricare entità false con dati associati quando necessario durante un test.</span><span class="sxs-lookup"><span data-stu-id="32005-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="32005-394">A questo punto si rivolgerà la nostra attenzione dalla&lt;creazione&gt; di repository utilizzando IObjectSet T e guardare le astrazioni per nascondere tutti i segni del framework di persistenza.</span><span class="sxs-lookup"><span data-stu-id="32005-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="32005-395">Repository personalizzati</span><span class="sxs-lookup"><span data-stu-id="32005-395">Custom Repositories</span></span>

<span data-ttu-id="32005-396">Quando abbiamo presentato per la prima volta il modello di progettazione dell'unità di lavoro in questo articolo abbiamo fornito del codice di esempio per l'aspetto dell'unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="32005-397">Ripresentiamo questa idea originale utilizzando lo scenario del biglietto da stato per dipendenti e dipendenti con cui abbiamo lavorato.</span><span class="sxs-lookup"><span data-stu-id="32005-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="32005-398">La differenza principale tra questa unità di lavoro e l'unità di lavoro creata nell'ultima sezione è come questa unità di lavoro&lt;non&gt;utilizza alcuna astrazioni dal framework EF4 (non esiste IObjectSet T ).</span><span class="sxs-lookup"><span data-stu-id="32005-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="32005-399">IObjectSet&lt;&gt; T funziona bene come un'interfaccia del repository, ma l'API che espone potrebbe non essere perfettamente allineata con le esigenze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="32005-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="32005-400">In questo approccio imminente rappresenteremo i repository&lt;&gt; utilizzando un'astrazione IRepository T personalizzata.</span><span class="sxs-lookup"><span data-stu-id="32005-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="32005-401">Molti sviluppatori che seguono la progettazione basata su test, la progettazione basata&lt;&gt; sul comportamento e la progettazione di metodologie basate su domini preferiscono l'approccio IRepository T per diversi motivi.</span><span class="sxs-lookup"><span data-stu-id="32005-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="32005-402">In primo luogo, l'interfaccia IRepository&lt;T&gt; rappresenta un livello "anti-corruzione".</span><span class="sxs-lookup"><span data-stu-id="32005-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="32005-403">Come descritto da Eric Evans nel suo libro Domain Driven Design, un livello anti-corruzione mantiene il codice di dominio lontano dalle API dell'infrastruttura, ad esempio un'API di persistenza.</span><span class="sxs-lookup"><span data-stu-id="32005-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="32005-404">In secondo luogo, gli sviluppatori possono compilare metodi nel repository che soddisfano le esigenze esatte di un'applicazione (come rilevato durante la scrittura di test).</span><span class="sxs-lookup"><span data-stu-id="32005-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="32005-405">Ad esempio, potrebbe essere spesso necessario individuare una singola entità usando un valore ID, pertanto è possibile aggiungere un metodo FindById all'interfaccia del repository.</span><span class="sxs-lookup"><span data-stu-id="32005-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="32005-406">La definizione di IRepository&lt;T&gt; sarà simile alla seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="32005-407">Si noti che verrà rilasciata&lt;l'elemento all'utilizzo di un IQueryable T&gt; interfaccia per esporre le raccolte di entità.</span><span class="sxs-lookup"><span data-stu-id="32005-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="32005-408">IQueryable&lt;&gt; T consente agli alberi di espressione LINQ di fluire nel provider EF4 e fornire al provider una visualizzazione olistica della query.</span><span class="sxs-lookup"><span data-stu-id="32005-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="32005-409">Una seconda opzione consiste nel&lt;&gt;restituire IEnumerable T , il che significa che il provider LINQ EF4 vedrà solo le espressioni compilate all'interno del repository.</span><span class="sxs-lookup"><span data-stu-id="32005-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="32005-410">Qualsiasi raggruppamento, ordinamento e proiezione eseguiti all'esterno del repository non verranno composti nel comando SQL inviato al database, il che può compromettere le prestazioni.</span><span class="sxs-lookup"><span data-stu-id="32005-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="32005-411">D'altra parte, un repository che&lt;&gt; restituisce solo IEnumerable T risultati non vi sorprenderà mai con un nuovo comando SQL.</span><span class="sxs-lookup"><span data-stu-id="32005-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="32005-412">Entrambi gli approcci funzioneranno e entrambi rimangono testati.</span><span class="sxs-lookup"><span data-stu-id="32005-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="32005-413">È semplice fornire una singola implementazione&lt;dell'interfaccia IRepository T&gt; utilizzando i generics e l'API ObjectContext di EF4.</span><span class="sxs-lookup"><span data-stu-id="32005-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="32005-414">L'approccio IRepository&lt;T&gt; offre un controllo aggiuntivo sulle query perché un client deve richiamare un metodo per accedere a un'entità.</span><span class="sxs-lookup"><span data-stu-id="32005-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="32005-415">All'interno del metodo è possibile fornire ulteriori controlli e operatori LINQ per applicare i vincoli dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="32005-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="32005-416">Si noti che l'interfaccia ha due vincoli sul parametro di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="32005-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="32005-417">Il primo vincolo è la classe cons&lt;taint richiesta dall'oggetto ObjectSet T&gt;e il secondo vincolo impone alle entità di implementare IEntity, ovvero un'astrazione creata per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="32005-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="32005-418">L'interfaccia IEntity impone alle entità di avere una proprietà Id leggibile e possiamo quindi usare questa proprietà nel metodo FindById.The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span><span class="sxs-lookup"><span data-stu-id="32005-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="32005-419">IEntity è definito con il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="32005-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="32005-420">IEntity potrebbe essere considerata una piccola violazione dell'ignoranza della persistenza poiché le entità sono necessarie per implementare questa interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32005-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="32005-421">Tenere presente che l'ignoranza della persistenza riguarda i compromessi e per molti la funzionalità FindById supererà il vincolo imposto dall'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="32005-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="32005-422">L'interfaccia non ha alcun impatto sulla testabilità.</span><span class="sxs-lookup"><span data-stu-id="32005-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="32005-423">La creazione di un'istanza di un IRepository&lt;T&gt; live richiede un oggetto EF4 ObjectContext, pertanto un'unità di lavoro concreta di implementazione deve gestire la creazione di istanze.</span><span class="sxs-lookup"><span data-stu-id="32005-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="32005-424">Utilizzo del repository personalizzato</span><span class="sxs-lookup"><span data-stu-id="32005-424">Using the Custom Repository</span></span>

<span data-ttu-id="32005-425">L'utilizzo del repository personalizzato non è significativamente diverso&lt;&gt;dall'utilizzo del repository basato su IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="32005-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="32005-426">Invece di applicare gli operatori LINQ direttamente a una proprietà, è necessario innanzitutto richiamare uno dei metodi del repository per acquisire un riferimento IQueryable&lt;T.&gt;</span><span class="sxs-lookup"><span data-stu-id="32005-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="32005-427">Si noti che l'operatore include personalizzato implementato in precedenza funzionerà senza modifiche.</span><span class="sxs-lookup"><span data-stu-id="32005-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="32005-428">Il metodo FindById del repository rimuove la logica duplicata dalle azioni che tentano di recuperare una singola entità.</span><span class="sxs-lookup"><span data-stu-id="32005-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="32005-429">Non vi è alcuna differenza significativa nella testabilità dei due approcci che abbiamo esaminato.</span><span class="sxs-lookup"><span data-stu-id="32005-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="32005-430">Potremmo fornire implementazioni false&lt;di&gt; IRepository T creando&lt;classi&gt; concrete supportate da HashSet Employee - proprio come quello che abbiamo fatto nell'ultima sezione.</span><span class="sxs-lookup"><span data-stu-id="32005-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="32005-431">Tuttavia, alcuni sviluppatori preferiscono utilizzare oggetti fittizi e framework di oggetti fittizi anziché creare falsi.</span><span class="sxs-lookup"><span data-stu-id="32005-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="32005-432">Esamineremo l'utilizzo di simulazioni per testare la nostra implementazione e discutere le differenze tra simulazioni e falsi nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="32005-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="32005-433">Test con Mocks</span><span class="sxs-lookup"><span data-stu-id="32005-433">Testing with Mocks</span></span>

<span data-ttu-id="32005-434">Ci sono diversi approcci per costruire quello che Martin Fowler chiama un "test double".</span><span class="sxs-lookup"><span data-stu-id="32005-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="32005-435">Un test double (come un film stunt double) è un oggetto che si costruisce per "stare in" per oggetti reali di produzione durante i test.</span><span class="sxs-lookup"><span data-stu-id="32005-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="32005-436">I repository in memoria che abbiamo creato sono test doppi per i repository che comunicano con SQL Server.</span><span class="sxs-lookup"><span data-stu-id="32005-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="32005-437">Abbiamo visto come usare questi test-double durante gli unit test per isolare il codice e mantenere i test in esecuzione veloce.</span><span class="sxs-lookup"><span data-stu-id="32005-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="32005-438">Il test raddoppia ha implementazioni reali e funzionanti.</span><span class="sxs-lookup"><span data-stu-id="32005-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="32005-439">Dietro le quinte ognuno memorizza una raccolta concreta di oggetti, e aggiungerà e rimuoverà oggetti da questa raccolta come si manipola il repository durante un test.</span><span class="sxs-lookup"><span data-stu-id="32005-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="32005-440">Alcuni sviluppatori amano costruire il loro test raddoppia in questo modo - con codice reale e implementazioni di lavoro.</span><span class="sxs-lookup"><span data-stu-id="32005-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="32005-441">Questi doppi test sono ciò che chiamiamo *falsi*.</span><span class="sxs-lookup"><span data-stu-id="32005-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="32005-442">Hanno implementazioni funzionanti, ma non sono abbastanza reali per l'uso in produzione.</span><span class="sxs-lookup"><span data-stu-id="32005-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="32005-443">Il repository falso in realtà non scrive al database.</span><span class="sxs-lookup"><span data-stu-id="32005-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="32005-444">Il server SMTP falso in realtà non invia un messaggio di posta elettronica sulla rete.</span><span class="sxs-lookup"><span data-stu-id="32005-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="32005-445">Mocks versus Fakes</span><span class="sxs-lookup"><span data-stu-id="32005-445">Mocks versus Fakes</span></span>

<span data-ttu-id="32005-446">C'è un altro tipo di test double noto come un *mock*.</span><span class="sxs-lookup"><span data-stu-id="32005-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="32005-447">Mentre i falsi hanno implementazioni di lavoro, le simulazioni sono senza implementazione.</span><span class="sxs-lookup"><span data-stu-id="32005-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="32005-448">Con l'aiuto di un framework di oggetti fittizi costruiamo questi oggetti fittizi in fase di esecuzione e li usiamo come test double.</span><span class="sxs-lookup"><span data-stu-id="32005-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="32005-449">In questa sezione useremo il framework di simulazione open source Moq.</span><span class="sxs-lookup"><span data-stu-id="32005-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="32005-450">Ecco un semplice esempio di utilizzo di Moq per creare dinamicamente un test double per un repository di dipendenti.</span><span class="sxs-lookup"><span data-stu-id="32005-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="32005-451">Chiediamo a Moq un'implementazione di IRepository&lt;Employee&gt; e ne crea uno in modo dinamico.</span><span class="sxs-lookup"><span data-stu-id="32005-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="32005-452">È possibile ottenere l'oggetto&lt;&gt; che implementa IRepository Employee&lt;accedendo alla proprietà Object dell'oggetto Mock T.&gt;</span><span class="sxs-lookup"><span data-stu-id="32005-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="32005-453">È questo oggetto interno che possiamo passare nei nostri controller, e non sapranno se questo è un test double o il repository reale.</span><span class="sxs-lookup"><span data-stu-id="32005-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="32005-454">È possibile richiamare metodi sull'oggetto proprio come si richiamano i metodi su un oggetto con un'implementazione reale.</span><span class="sxs-lookup"><span data-stu-id="32005-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="32005-455">È necessario chiedersi cosa farà il repository fittizio quando si richiama il Add metodo.</span><span class="sxs-lookup"><span data-stu-id="32005-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="32005-456">Poiché non vi è alcuna implementazione dietro l'oggetto fittizio, Add non esegue alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="32005-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="32005-457">Non c'è collezione concreta dietro le quinte come abbiamo avuto con i falsi che abbiamo scritto, quindi il dipendente viene scartato.</span><span class="sxs-lookup"><span data-stu-id="32005-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="32005-458">Che dire del valore restituito di FindById?</span><span class="sxs-lookup"><span data-stu-id="32005-458">What about the return value of FindById?</span></span> <span data-ttu-id="32005-459">In questo caso l'oggetto fittizio esegue l'unica operazione che può eseguire, ovvero restituire un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="32005-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="32005-460">Poiché viene restituito un tipo di riferimento (un Employee), il valore restituito è un valore null.</span><span class="sxs-lookup"><span data-stu-id="32005-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="32005-461">Mocks potrebbe sembrare inutile; tuttavia, ci sono altre due caratteristiche di scherni di cui non abbiamo parlato.</span><span class="sxs-lookup"><span data-stu-id="32005-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="32005-462">In primo luogo, il framework Moq registra tutte le chiamate effettuate sull'oggetto fittizio.</span><span class="sxs-lookup"><span data-stu-id="32005-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="32005-463">Più avanti nel codice possiamo chiedere a Moq se qualcuno ha richiamato il add metodo, o se qualcuno ha richiamato il FindById metodo.</span><span class="sxs-lookup"><span data-stu-id="32005-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="32005-464">Vedremo più tardi come possiamo usare questa funzione di registrazione "scatola nera" nei test.</span><span class="sxs-lookup"><span data-stu-id="32005-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="32005-465">La seconda grande caratteristica è come possiamo usare Moq per programmare un oggetto fittizio con *aspettative*.</span><span class="sxs-lookup"><span data-stu-id="32005-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="32005-466">Un'aspettativa indica all'oggetto fittizio come rispondere a qualsiasi interazione specificata.</span><span class="sxs-lookup"><span data-stu-id="32005-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="32005-467">Ad esempio, è possibile programmare un'aspettativa nel nostro mock e dire di restituire un oggetto dipendente quando qualcuno richiama FindById.For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span><span class="sxs-lookup"><span data-stu-id="32005-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="32005-468">Il framework Moq utilizza un'API di installazione ed espressioni lambda per programmare queste aspettative.</span><span class="sxs-lookup"><span data-stu-id="32005-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="32005-469">In questo esempio chiediamo a Moq di creare dinamicamente un repository e quindi programmiamo il repository con un'aspettativa.</span><span class="sxs-lookup"><span data-stu-id="32005-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="32005-470">L'aspettativa indica all'oggetto fittizio di restituire un nuovo oggetto dipendente con un valore Id pari a 5 quando un utente richiama il metodo FindById passando un valore pari a 5.</span><span class="sxs-lookup"><span data-stu-id="32005-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="32005-471">Questo test viene superato e non è necessario creare&lt;un'implementazione completa per falsificare IRepository T&gt;.</span><span class="sxs-lookup"><span data-stu-id="32005-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="32005-472">Rivediamo i test che abbiamo scritto in precedenza e li riproviamo per usare i beffe invece dei falsi.</span><span class="sxs-lookup"><span data-stu-id="32005-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="32005-473">Proprio come prima, useremo una classe di base per configurare le parti comuni dell'infrastruttura necessarie per tutti i test del controller.</span><span class="sxs-lookup"><span data-stu-id="32005-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="32005-474">Il codice di installazione rimane per lo più lo stesso.</span><span class="sxs-lookup"><span data-stu-id="32005-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="32005-475">Invece di usare i falsi, useremo Moq per costruire oggetti fittizi.</span><span class="sxs-lookup"><span data-stu-id="32005-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="32005-476">La classe base organizza per l'unità di lavoro fittizia per restituire un repository fittizio quando il codice richiama il Employees proprietà.</span><span class="sxs-lookup"><span data-stu-id="32005-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="32005-477">Il resto della configurazione fittizia avrà luogo all'interno dei dispositivi di test dedicati a ogni scenario specifico.</span><span class="sxs-lookup"><span data-stu-id="32005-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="32005-478">Ad esempio, il dispositivo di test per l'azione Indice imposta il repository fittizio per restituire un elenco di dipendenti quando l'azione richiama il metodo FindAll del repository fittizio.</span><span class="sxs-lookup"><span data-stu-id="32005-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="32005-479">Fatta eccezione per le aspettative, i nostri test sembrano simili ai test che avevamo prima.</span><span class="sxs-lookup"><span data-stu-id="32005-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="32005-480">Tuttavia, con la capacità di registrazione di un framework fittizio possiamo affrontare il test da un'angolazione diversa.</span><span class="sxs-lookup"><span data-stu-id="32005-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="32005-481">Esamineremo questa nuova prospettiva nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="32005-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="32005-482">Test di stato e interazione</span><span class="sxs-lookup"><span data-stu-id="32005-482">State versus Interaction Testing</span></span>

<span data-ttu-id="32005-483">Esistono diverse tecniche che è possibile utilizzare per testare il software con oggetti fittizi.</span><span class="sxs-lookup"><span data-stu-id="32005-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="32005-484">Un approccio consiste nell'utilizzare test basati sullo stato, che è quello che abbiamo fatto finora in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="32005-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="32005-485">I test basati sullo stato fanno asserzioni sullo stato del software.</span><span class="sxs-lookup"><span data-stu-id="32005-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="32005-486">Nell'ultimo test abbiamo richiamato un metodo di azione sul controller e fatto un'asserzione sul modello che deve compilare.</span><span class="sxs-lookup"><span data-stu-id="32005-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="32005-487">Di seguito sono riportati alcuni altri esempi di stato di test:Here are some other examples of testing state:</span><span class="sxs-lookup"><span data-stu-id="32005-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="32005-488">Verificare che il repository contenga il nuovo oggetto dipendente dopo l'esecuzione di Create.</span><span class="sxs-lookup"><span data-stu-id="32005-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="32005-489">Verificare che il modello contenga un elenco di tutti i dipendenti dopo l'esecuzione dell'indice.</span><span class="sxs-lookup"><span data-stu-id="32005-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="32005-490">Verificare che il repository non contenga un determinato dipendente dopo l'esecuzione di Delete.</span><span class="sxs-lookup"><span data-stu-id="32005-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="32005-491">Un altro approccio che si vedrà con gli oggetti fittizi consiste nel verificare *le interazioni*.</span><span class="sxs-lookup"><span data-stu-id="32005-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="32005-492">Mentre il test basato sullo stato crea asserzioni sullo stato degli oggetti, il test basato sull'interazione fa sapere come gli oggetti interagiscono.</span><span class="sxs-lookup"><span data-stu-id="32005-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="32005-493">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="32005-493">For example:</span></span>

-   <span data-ttu-id="32005-494">Verificare che il controller richiami il metodo Add del repository durante l'esecuzione di Create.</span><span class="sxs-lookup"><span data-stu-id="32005-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="32005-495">Verificare che il controller richiami il metodo FindAll del repository durante l'esecuzione di Index.</span><span class="sxs-lookup"><span data-stu-id="32005-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="32005-496">Verificare che il controller richiami l'unità di lavoro Commit metodo per salvare le modifiche durante l'esecuzione di Edit.</span><span class="sxs-lookup"><span data-stu-id="32005-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="32005-497">I test di interazione spesso richiedono meno dati di test, perché non stiamo frugando all'interno delle raccolte e la verifica dei conteggi.</span><span class="sxs-lookup"><span data-stu-id="32005-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="32005-498">Ad esempio, se si conosce l'azione Dettagli richiama il metodo FindById di un repository con il valore corretto, è probabile che l'azione si comporti correttamente.</span><span class="sxs-lookup"><span data-stu-id="32005-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="32005-499">È possibile verificare questo comportamento senza impostare i dati di test da restituire da FindById.We can verify this behavior without setting up any test data to return from FindById.</span><span class="sxs-lookup"><span data-stu-id="32005-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="32005-500">L'unica configurazione richiesta nel dispositivo di test precedente è l'impostazione fornita dalla classe base.</span><span class="sxs-lookup"><span data-stu-id="32005-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="32005-501">Quando richiamiamo l'azione del controller, Moq registrerà le interazioni con il repository fittizio.</span><span class="sxs-lookup"><span data-stu-id="32005-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="32005-502">Usando l'API di verifica di Moq, possiamo chiedere a Moq se il controller ha richiamato FindById con il valore dell'ID corretto.</span><span class="sxs-lookup"><span data-stu-id="32005-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="32005-503">Se il controller non ha richiamato il metodo o ha richiamato il metodo con un valore di parametro imprevisto, il metodo Verify genererà un'eccezione e il test avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="32005-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="32005-504">Ecco un altro esempio per verificare che l'azione Create richiami Commit sull'unità di lavoro corrente.</span><span class="sxs-lookup"><span data-stu-id="32005-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="32005-505">Un pericolo con il test di interazione è la tendenza a specificare eccessivamente le interazioni.</span><span class="sxs-lookup"><span data-stu-id="32005-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="32005-506">La capacità dell'oggetto fittizio di registrare e verificare ogni interazione con l'oggetto fittizio non significa che il test debba tentare di verificare ogni interazione.</span><span class="sxs-lookup"><span data-stu-id="32005-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="32005-507">Alcune interazioni sono dettagli di implementazione ed è necessario verificare solo le interazioni *necessarie* per soddisfare il test corrente.</span><span class="sxs-lookup"><span data-stu-id="32005-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="32005-508">La scelta tra simulazioni o falsi dipende in gran parte dal sistema che stai testando e dalle tue preferenze personali (o di squadra).</span><span class="sxs-lookup"><span data-stu-id="32005-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="32005-509">Gli oggetti fittizi possono ridurre drasticamente la quantità di codice necessaria per implementare il doppio dei test, ma non tutti sono a proprio agio nelle aspettative di programmazione e nella verifica delle interazioni.</span><span class="sxs-lookup"><span data-stu-id="32005-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="32005-510">Conclusioni</span><span class="sxs-lookup"><span data-stu-id="32005-510">Conclusions</span></span>

<span data-ttu-id="32005-511">In questo articolo sono stati illustrati diversi approcci alla creazione di codice tepossibile durante l'utilizzo del ADO.NET Entity Framework per la persistenza dei dati.</span><span class="sxs-lookup"><span data-stu-id="32005-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="32005-512">Possiamo sfruttare le astrazioni incorporate&lt;come&gt;IObjectSet T , o creare&lt;&gt;le proprie astrazioni come IRepository T .</span><span class="sxs-lookup"><span data-stu-id="32005-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="32005-513">In entrambi i casi, il supporto POCO nell'ADO.NET Entity Framework 4.0 consente ai consumer di queste astrazioni di rimanere persistenti ignoranti e altamente teabili.</span><span class="sxs-lookup"><span data-stu-id="32005-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="32005-514">Funzionalità aggiuntive di EF4, ad esempio il caricamento lazy implicito, il codice del servizio business e dell'applicazione funziona senza preoccuparsi dei dettagli di un Data Store relazionale.</span><span class="sxs-lookup"><span data-stu-id="32005-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="32005-515">Infine, le astrazioni che creiamo sono facili da simulare o falsificare all'interno degli unit test e possiamo usare questi test doppi per ottenere test veloci, altamente isolati e affidabili.</span><span class="sxs-lookup"><span data-stu-id="32005-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="32005-516">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="32005-516">Additional Resources</span></span>

-   <span data-ttu-id="32005-517">Robert C. Martin, " Il principio di [responsabilità unica](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="32005-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="32005-518">Martin Fowler, [catalogo di pattern](https://www.martinfowler.com/eaaCatalog/index.html) da modelli di architettura di applicazioni *aziendali*</span><span class="sxs-lookup"><span data-stu-id="32005-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="32005-519">Griffin Caprio, " [Iniezione](https://msdn.microsoft.com/magazine/cc163739.aspx)di dipendenza "</span><span class="sxs-lookup"><span data-stu-id="32005-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="32005-520">Blog sulla programmabilità dei dati, " [Procedura dettagliata: test driveed development con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".</span><span class="sxs-lookup"><span data-stu-id="32005-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="32005-521">Blog sulla programmabilità dei dati, " [Utilizzo dei modelli di repository e unità di lavoro con Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="32005-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="32005-522">Aaron Jensen, " [Introduzione alle specifiche della macchina](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="32005-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="32005-523">Eric Lee, " [BDD con MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"</span><span class="sxs-lookup"><span data-stu-id="32005-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="32005-524">Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="32005-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="32005-525">Martin Fowler, " [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="32005-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="32005-526">Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="32005-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="32005-527">Moq</span><span class="sxs-lookup"><span data-stu-id="32005-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="32005-528">Biografia</span><span class="sxs-lookup"><span data-stu-id="32005-528">Biography</span></span>

<span data-ttu-id="32005-529">Scott Allen è un membro dello staff tecnico di Pluralsight e il fondatore di OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="32005-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="32005-530">In 15 anni di sviluppo software commerciale, Scott ha lavorato su soluzioni per tutto, dai dispositivi embedded a 8 bit alle applicazioni web ASP.NET altamente scalabili.</span><span class="sxs-lookup"><span data-stu-id="32005-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="32005-531">È possibile raggiungere Scott sul suo blog a OdeToCode, o su Twitter a [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span><span class="sxs-lookup"><span data-stu-id="32005-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
