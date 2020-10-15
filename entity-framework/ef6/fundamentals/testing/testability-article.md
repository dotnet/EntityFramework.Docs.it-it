---
title: Testabilità e Entity Framework 4,0-EF6
description: Testabilità e Entity Framework 4,0
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: 8a9c8fbf7d6131498bd9b37567fdb90010b808cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062984"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="59dc9-103">Testabilità e Entity Framework 4,0</span><span class="sxs-lookup"><span data-stu-id="59dc9-103">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="59dc9-104">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="59dc9-104">Scott Allen</span></span>

<span data-ttu-id="59dc9-105">Data di pubblicazione: maggio 2010</span><span class="sxs-lookup"><span data-stu-id="59dc9-105">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="59dc9-106">Introduzione</span><span class="sxs-lookup"><span data-stu-id="59dc9-106">Introduction</span></span>

<span data-ttu-id="59dc9-107">In questa white paper viene descritto e illustrato come scrivere codice testabile con ADO.NET Entity Framework 4,0 e Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="59dc9-107">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="59dc9-108">Questo documento non tenta di concentrarsi su una metodologia di test specifica, ad esempio la progettazione basata su test (TDD) o la progettazione basata sul comportamento (BDD).</span><span class="sxs-lookup"><span data-stu-id="59dc9-108">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="59dc9-109">Questo documento è invece incentrato sulla scrittura di codice che usa il Entity Framework ADO.NET, ma rimane facile da isolare e testare in modo automatico.</span><span class="sxs-lookup"><span data-stu-id="59dc9-109">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="59dc9-110">Verranno esaminati i modelli di progettazione comuni che semplificano i test negli scenari di accesso ai dati e viene illustrato come applicare tali modelli quando si usa il Framework.</span><span class="sxs-lookup"><span data-stu-id="59dc9-110">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="59dc9-111">Verranno inoltre esaminate le funzionalità specifiche del Framework per vedere come tali funzionalità possono essere utilizzate nel codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-111">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="59dc9-112">Che cos'è il codice testabile?</span><span class="sxs-lookup"><span data-stu-id="59dc9-112">What Is Testable Code?</span></span>

<span data-ttu-id="59dc9-113">La possibilità di verificare un componente software con unit test automatizzati offre molti vantaggi desiderati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-113">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="59dc9-114">Tutti sanno che i test corretti ridurranno il numero di difetti software in un'applicazione e aumenteranno la qualità dell'applicazione, ma la presenza di unit test non sarà più sufficiente per individuare i bug.</span><span class="sxs-lookup"><span data-stu-id="59dc9-114">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="59dc9-115">Una suite di unit test efficace consente a un team di sviluppo di risparmiare tempo e mantenere il controllo del software creato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-115">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="59dc9-116">Un team può apportare modifiche al codice esistente, effettuare il refactoring, riprogettare e ristrutturare il software per soddisfare i nuovi requisiti e aggiungere nuovi componenti in un'applicazione, pur sapendo che il gruppo di test può verificare il comportamento dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-116">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="59dc9-117">Gli unit test fanno parte di un rapido ciclo di feedback per facilitare le modifiche e mantenere la gestibilità del software Man mano che aumenta la complessità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-117">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="59dc9-118">Il testing unità presenta tuttavia un prezzo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-118">Unit testing comes with a price, however.</span></span> <span data-ttu-id="59dc9-119">Un team deve investire il tempo necessario per creare e gestire gli unit test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-119">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="59dc9-120">La quantità di lavoro richiesto per creare questi test è direttamente correlata alla **testabilità** del software sottostante.</span><span class="sxs-lookup"><span data-stu-id="59dc9-120">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="59dc9-121">Quanto è facile testare il software?</span><span class="sxs-lookup"><span data-stu-id="59dc9-121">How easy is the software to test?</span></span> <span data-ttu-id="59dc9-122">Un team che progetta software con la testabilità può creare test efficaci più velocemente rispetto al team che lavora con software non testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-122">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="59dc9-123">Microsoft ha progettato ADO.NET Entity Framework 4,0 (EF4) con la testabilità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-123">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="59dc9-124">Ciò non significa che gli sviluppatori scriveranno unit test sul codice del Framework stesso.</span><span class="sxs-lookup"><span data-stu-id="59dc9-124">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="59dc9-125">Gli obiettivi di testabilità per EF4 semplificano invece la creazione di codice testabile basato sul Framework.</span><span class="sxs-lookup"><span data-stu-id="59dc9-125">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="59dc9-126">Prima di esaminare esempi specifici, è utile comprendere le qualità del codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-126">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="59dc9-127">Qualità del codice testabile</span><span class="sxs-lookup"><span data-stu-id="59dc9-127">The Qualities of Testable Code</span></span>

<span data-ttu-id="59dc9-128">Il codice di facile testing mostrerà sempre almeno due tratti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-128">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="59dc9-129">Per prima cosa, è facile **osservare**il codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-129">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="59dc9-130">Dato un set di input, dovrebbe essere facile osservare l'output del codice.</span><span class="sxs-lookup"><span data-stu-id="59dc9-130">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="59dc9-131">Il test del metodo seguente, ad esempio, è semplice perché il metodo restituisce direttamente il risultato di un calcolo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-131">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="59dc9-132">Il test di un metodo è difficile se il metodo scrive il valore calcolato in un socket di rete, in una tabella di database o in un file come il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-132">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="59dc9-133">Il test deve eseguire operazioni aggiuntive per recuperare il valore.</span><span class="sxs-lookup"><span data-stu-id="59dc9-133">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="59dc9-134">In secondo luogo, è facile **isolare**il codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-134">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="59dc9-135">Si userà lo pseudo-codice seguente come esempio errato di codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-135">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="59dc9-136">Il metodo è facile da osservare: è possibile passare un criterio assicurativo e verificare che il valore restituito corrisponda a un risultato previsto.</span><span class="sxs-lookup"><span data-stu-id="59dc9-136">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="59dc9-137">Tuttavia, per testare il metodo, è necessario disporre di un database installato con lo schema corretto e configurare il server SMTP se il metodo tenta di inviare un messaggio di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="59dc9-137">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="59dc9-138">Il unit test desidera solo verificare la logica di calcolo all'interno del metodo, ma il test potrebbe non riuscire perché il server di posta elettronica è offline o perché il server di database è stato spostato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-138">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="59dc9-139">Entrambi questi errori non sono correlati al comportamento che il test vuole verificare.</span><span class="sxs-lookup"><span data-stu-id="59dc9-139">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="59dc9-140">Il comportamento è difficile da isolare.</span><span class="sxs-lookup"><span data-stu-id="59dc9-140">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="59dc9-141">Gli sviluppatori di software che tentano di scrivere codice testabile si impegnano spesso a mantenere una separazione delle problematiche nel codice che scrivono.</span><span class="sxs-lookup"><span data-stu-id="59dc9-141">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="59dc9-142">Il metodo precedente dovrebbe concentrarsi sui calcoli aziendali e delegare i dettagli di implementazione del database e della posta elettronica ad altri componenti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-142">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="59dc9-143">Robert C. Martin chiama questo principio di singola responsabilità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-143">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="59dc9-144">Un oggetto deve incapsulare una singola responsabilità limitata, ad esempio il calcolo del valore di un criterio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-144">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="59dc9-145">Tutte le altre operazioni di database e di notifica dovrebbero essere responsabilità di un altro oggetto.</span><span class="sxs-lookup"><span data-stu-id="59dc9-145">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="59dc9-146">Il codice scritto in questo modo è più semplice da isolare perché si concentra su una singola attività.</span><span class="sxs-lookup"><span data-stu-id="59dc9-146">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="59dc9-147">In .NET abbiamo le astrazioni necessarie per seguire il principio di singola responsabilità e ottenere l'isolamento.</span><span class="sxs-lookup"><span data-stu-id="59dc9-147">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="59dc9-148">È possibile usare le definizioni di interfaccia e forzare il codice a usare l'astrazione dell'interfaccia anziché un tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="59dc9-148">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="59dc9-149">Più avanti in questo articolo verrà illustrato come un metodo come l'esempio non valido presentato sopra possa funzionare con le *interfacce che* comunicano con il database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-149">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="59dc9-150">In fase di test, tuttavia, è possibile sostituire un'implementazione fittizia che non comunica con il database ma che contenga i dati in memoria.</span><span class="sxs-lookup"><span data-stu-id="59dc9-150">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="59dc9-151">Questa implementazione fittizia isola il codice da problemi non correlati nel codice di accesso ai dati o nella configurazione del database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-151">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="59dc9-152">L'isolamento presenta vantaggi aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-152">There are additional benefits to isolation.</span></span> <span data-ttu-id="59dc9-153">Il calcolo dell'attività nell'ultimo metodo deve richiedere solo pochi millisecondi, ma è possibile che il test venga eseguito per diversi secondi, in quanto il codice viene hop in rete e comunica con diversi server.</span><span class="sxs-lookup"><span data-stu-id="59dc9-153">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="59dc9-154">Gli unit test devono essere eseguiti rapidamente per facilitare le piccole modifiche.</span><span class="sxs-lookup"><span data-stu-id="59dc9-154">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="59dc9-155">Gli unit test devono essere anche ripetibili e non hanno esito negativo perché un componente non correlato al test presenta un problema.</span><span class="sxs-lookup"><span data-stu-id="59dc9-155">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="59dc9-156">La scrittura di codice facile da osservare e per isolare significa che gli sviluppatori avranno più tempo per scrivere test per il codice, dedicare meno tempo ad attendere l'esecuzione dei test e, più importante, dedicare meno tempo alla verifica dei bug che non esistono.</span><span class="sxs-lookup"><span data-stu-id="59dc9-156">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="59dc9-157">È auspicabile apprezzare i vantaggi dei test e comprendere le qualità esposte dal codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-157">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="59dc9-158">Si sta per risolvere il modo in cui scrivere il codice che funziona con EF4 per salvare i dati in un database, rimanendo osservabili e facili da isolare, ma prima di tutto verrà ristretta la nostra attenzione per discutere delle progettazioni testabili per l'accesso ai dati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-158">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="59dc9-159">Modelli di progettazione per la persistenza dei dati</span><span class="sxs-lookup"><span data-stu-id="59dc9-159">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="59dc9-160">Entrambi gli esempi non validi presentati in precedenza avevano troppe responsabilità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-160">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="59dc9-161">Il primo esempio non valido doveva eseguire un calcolo *e* scrivere in un file.</span><span class="sxs-lookup"><span data-stu-id="59dc9-161">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="59dc9-162">Il secondo esempio non valido doveva leggere i dati da un database *ed* eseguire un calcolo aziendale *e* inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="59dc9-162">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="59dc9-163">Progettando metodi più piccoli che separano le problematiche e delegano la responsabilità ad altri componenti, potrai compiere grandi progressi per la scrittura di codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-163">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="59dc9-164">Lo scopo è quello di compilare le funzionalità componendo azioni da piccole e mirate astrazioni.</span><span class="sxs-lookup"><span data-stu-id="59dc9-164">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="59dc9-165">Per quanto riguarda la persistenza dei dati, le astrazioni piccole e mirate che stiamo cercando sono così comuni che sono state documentate come modelli di progettazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-165">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="59dc9-166">I modelli di libro dell'architettura delle applicazioni aziendali di Martin Fowler sono il primo lavoro per descrivere questi modelli in stampa.</span><span class="sxs-lookup"><span data-stu-id="59dc9-166">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="59dc9-167">Verrà fornita una breve descrizione di questi modelli nelle sezioni seguenti prima di illustrare il modo in cui questi ADO.NET Entity Framework implementano e funzionano con tali modelli.</span><span class="sxs-lookup"><span data-stu-id="59dc9-167">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="59dc9-168">Schema Repository</span><span class="sxs-lookup"><span data-stu-id="59dc9-168">The Repository Pattern</span></span>

<span data-ttu-id="59dc9-169">Fowler afferma un repository "media tra i livelli di dominio e di mapping dei dati usando un'interfaccia simile a una raccolta per accedere agli oggetti di dominio".</span><span class="sxs-lookup"><span data-stu-id="59dc9-169">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="59dc9-170">L'obiettivo del modello di repository è quello di isolare il codice dalle minuzie di accesso ai dati e, come abbiamo visto, l'isolamento precedente è un tratto obbligatorio per la testabilità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-170">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="59dc9-171">La chiave per l'isolamento è il modo in cui il repository espone oggetti usando un'interfaccia simile a una raccolta.</span><span class="sxs-lookup"><span data-stu-id="59dc9-171">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="59dc9-172">La logica scritta per l'uso del repository non ha idea di come il repository materializza gli oggetti richiesti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-172">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="59dc9-173">Il repository potrebbe comunicare con un database o solo restituire oggetti da una raccolta in memoria.</span><span class="sxs-lookup"><span data-stu-id="59dc9-173">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="59dc9-174">Tutto il codice deve essere in grado di verificare che il repository sia in grado di gestire la raccolta e di recuperare, aggiungere ed eliminare oggetti dalla raccolta.</span><span class="sxs-lookup"><span data-stu-id="59dc9-174">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="59dc9-175">Nelle applicazioni .NET esistenti un repository concreto eredita spesso da un'interfaccia generica come la seguente:</span><span class="sxs-lookup"><span data-stu-id="59dc9-175">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="59dc9-176">Verranno apportate alcune modifiche alla definizione dell'interfaccia quando si fornisce un'implementazione per EF4, ma il concetto di base rimane lo stesso.</span><span class="sxs-lookup"><span data-stu-id="59dc9-176">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="59dc9-177">Il codice può usare un repository concreto che implementa questa interfaccia per recuperare un'entità in base al valore di chiave primaria, per recuperare una raccolta di entità in base alla valutazione di un predicato o semplicemente recuperare tutte le entità disponibili.</span><span class="sxs-lookup"><span data-stu-id="59dc9-177">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="59dc9-178">Il codice può anche aggiungere e rimuovere entità tramite l'interfaccia del repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-178">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="59dc9-179">Dato un IRepository di oggetti Employee, il codice può eseguire le operazioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-179">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="59dc9-180">Poiché il codice usa un'interfaccia (IRepository of Employee), è possibile fornire il codice con implementazioni diverse dell'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="59dc9-180">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="59dc9-181">Un'implementazione potrebbe essere un'implementazione supportata da EF4 e salvare in modo permanente gli oggetti in un database Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="59dc9-181">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="59dc9-182">Un'implementazione diversa (uno usato durante il test) potrebbe essere supportata da un elenco in memoria di oggetti Employee.</span><span class="sxs-lookup"><span data-stu-id="59dc9-182">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="59dc9-183">L'interfaccia consente di ottenere l'isolamento nel codice.</span><span class="sxs-lookup"><span data-stu-id="59dc9-183">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="59dc9-184">Si noti che l' &lt; interfaccia IRepository T non &gt; espone un'operazione di salvataggio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-184">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="59dc9-185">Come si aggiornano gli oggetti esistenti?</span><span class="sxs-lookup"><span data-stu-id="59dc9-185">How do we update existing objects?</span></span> <span data-ttu-id="59dc9-186">È possibile che si trovino le definizioni IRepository che includono l'operazione di salvataggio e le implementazioni di questi repository dovranno immediatamente salvare in modo permanente un oggetto nel database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-186">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="59dc9-187">In molte applicazioni, tuttavia, non si desidera salvare in modo permanente gli oggetti singolarmente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-187">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="59dc9-188">Al contrario, si vuole che gli oggetti vengano portati in vita, ad esempio da repository diversi, modificare tali oggetti come parte di un'attività di business e quindi rendere permanente tutti gli oggetti come parte di una singola operazione atomica.</span><span class="sxs-lookup"><span data-stu-id="59dc9-188">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="59dc9-189">Fortunatamente, esiste un modello per consentire questo tipo di comportamento.</span><span class="sxs-lookup"><span data-stu-id="59dc9-189">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="59dc9-190">Modello di unità di lavoro</span><span class="sxs-lookup"><span data-stu-id="59dc9-190">The Unit of Work Pattern</span></span>

<span data-ttu-id="59dc9-191">Fowler afferma che un'unità di lavoro "gestisce un elenco di oggetti interessati da una transazione aziendale e coordina la scrittura delle modifiche e la risoluzione dei problemi di concorrenza".</span><span class="sxs-lookup"><span data-stu-id="59dc9-191">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="59dc9-192">È responsabilità dell'unità di lavoro tenere traccia delle modifiche apportate agli oggetti da un repository e rendere persistenti le modifiche apportate agli oggetti quando si indica all'unità di lavoro di eseguire il commit delle modifiche.</span><span class="sxs-lookup"><span data-stu-id="59dc9-192">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="59dc9-193">È anche responsabilità dell'unità di lavoro prendere i nuovi oggetti aggiunti a tutti i repository e inserire gli oggetti in un database e anche l'eliminazione di Mange.</span><span class="sxs-lookup"><span data-stu-id="59dc9-193">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="59dc9-194">Se hai mai lavorato con i set di impostazioni di ADO.NET, avrai già familiarità con il modello di unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="59dc9-194">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="59dc9-195">I set di dati ADO.NET hanno la possibilità di tenere traccia degli aggiornamenti, delle eliminazioni e dell'inserimento di oggetti DataRow e possono (con l'ausilio di un TableAdapter) riconciliare tutte le modifiche apportate a un database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-195">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="59dc9-196">Tuttavia, gli oggetti DataSet modellano un subset scollegato del database sottostante.</span><span class="sxs-lookup"><span data-stu-id="59dc9-196">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="59dc9-197">Il modello di unità di lavoro presenta lo stesso comportamento, ma funziona con gli oggetti business e gli oggetti di dominio isolati dal codice di accesso ai dati e non è a conoscenza del database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-197">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="59dc9-198">Un'astrazione per modellare l'unità di lavoro nel codice .NET potrebbe essere simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="59dc9-198">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="59dc9-199">Esponendo i riferimenti del repository dall'unità di lavoro, è possibile garantire che una singola unità di lavoro abbia la possibilità di tenere traccia di tutte le entità materializzate durante una transazione di business.</span><span class="sxs-lookup"><span data-stu-id="59dc9-199">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="59dc9-200">L'implementazione del metodo commit per un'unità di lavoro reale è il punto in cui si verificano tutte le magie per riconciliare le modifiche in memoria con il database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-200">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="59dc9-201">Dato un riferimento IUnitOfWork, il codice può apportare modifiche agli oggetti business recuperati da uno o più repository e salvare tutte le modifiche usando l'operazione di commit Atomic.</span><span class="sxs-lookup"><span data-stu-id="59dc9-201">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="59dc9-202">Modello di carico Lazy</span><span class="sxs-lookup"><span data-stu-id="59dc9-202">The Lazy Load Pattern</span></span>

<span data-ttu-id="59dc9-203">Fowler usa il nome Lazy Load per descrivere "un oggetto che non contiene tutti i dati necessari ma che sa come ottenerli".</span><span class="sxs-lookup"><span data-stu-id="59dc9-203">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="59dc9-204">Il caricamento lazy trasparente è una funzionalità importante da usare quando si scrive codice aziendale testabile e si usa un database relazionale.</span><span class="sxs-lookup"><span data-stu-id="59dc9-204">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="59dc9-205">Si consideri, ad esempio, il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-205">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="59dc9-206">Come viene popolata la raccolta cartellini?</span><span class="sxs-lookup"><span data-stu-id="59dc9-206">How is the TimeCards collection populated?</span></span> <span data-ttu-id="59dc9-207">Esistono due possibili risposte.</span><span class="sxs-lookup"><span data-stu-id="59dc9-207">There are two possible answers.</span></span> <span data-ttu-id="59dc9-208">Una risposta è che il repository dei dipendenti, quando viene richiesto di recuperare un dipendente, emette una query per recuperare il dipendente insieme alle informazioni sulla scheda tempo associate del dipendente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-208">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="59dc9-209">Nei database relazionali questa operazione richiede in genere una query con una clausola JOIN e può determinare il recupero di più informazioni rispetto a quelle richieste da un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-209">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="59dc9-210">Che cosa accade se l'applicazione non deve mai toccare la proprietà cartellini?</span><span class="sxs-lookup"><span data-stu-id="59dc9-210">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="59dc9-211">Una seconda risposta consiste nel caricare la proprietà cartellini "on demand".</span><span class="sxs-lookup"><span data-stu-id="59dc9-211">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="59dc9-212">Questo caricamento lazy è implicito e trasparente alla logica di business, perché il codice non richiama API speciali per recuperare le informazioni sulla scheda tempo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-212">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="59dc9-213">Il codice presuppone che le informazioni sulle schede temporali siano presenti quando necessario.</span><span class="sxs-lookup"><span data-stu-id="59dc9-213">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="59dc9-214">Il caricamento lazy presenta alcune magie che in genere comportano l'intercettazione in fase di esecuzione delle chiamate al metodo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-214">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="59dc9-215">Il codice di intercettazione è responsabile della comunicazione con il database e del recupero delle informazioni sulle schede temporali lasciando la logica di business gratuita per la logica di business.</span><span class="sxs-lookup"><span data-stu-id="59dc9-215">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="59dc9-216">Questo Lazy Load Magic consente al codice aziendale di isolarsi da operazioni di recupero dei dati e di ottenere codice più testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-216">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="59dc9-217">Lo svantaggio di un carico lazy è che, quando un' *applicazione necessita* delle informazioni sulle schede temporali, il codice eseguirà una query aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="59dc9-217">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="59dc9-218">Questo non è un problema per molte applicazioni, ma per le applicazioni sensibili alle prestazioni o per le applicazioni che eseguono il ciclo di un numero di oggetti Employee e l'esecuzione di una query per recuperare le schede temporali durante ogni iterazione del ciclo (un problema spesso indicato come problema di query N + 1), il caricamento lazy è un trascinamento.</span><span class="sxs-lookup"><span data-stu-id="59dc9-218">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="59dc9-219">In questi scenari, un'applicazione potrebbe voler caricare avidamente le informazioni sulle schede temporali nel modo più efficiente possibile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-219">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="59dc9-220">Fortunatamente, si vedrà come EF4 supporta sia i carichi Lazy impliciti che i caricamenti eager efficienti quando si passa alla sezione successiva e si implementano questi schemi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-220">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="59dc9-221">Implementazione di modelli con il Entity Framework</span><span class="sxs-lookup"><span data-stu-id="59dc9-221">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="59dc9-222">La novità è che tutti i modelli di progettazione descritti nell'ultima sezione sono semplici da implementare con EF4.</span><span class="sxs-lookup"><span data-stu-id="59dc9-222">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="59dc9-223">Per dimostrare che verrà usata una semplice applicazione MVC ASP.NET per modificare e visualizzare i dipendenti e le informazioni relative alle schede temporali associate.</span><span class="sxs-lookup"><span data-stu-id="59dc9-223">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="59dc9-224">Si inizierà usando i seguenti "Plain Old CLR Objects" (POCO).</span><span class="sxs-lookup"><span data-stu-id="59dc9-224">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="59dc9-225">Queste definizioni di classe cambiano leggermente quando si esplorano approcci e funzionalità diversi di EF4, ma lo scopo è quello di impedire al più possibile la persistenza di queste classi come la persistenza ignorata (PI).</span><span class="sxs-lookup"><span data-stu-id="59dc9-225">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="59dc9-226">Un oggetto PI non sa *come*, o anche *se*, lo stato che possiede si trova all'interno di un database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-226">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="59dc9-227">PI e POCOs passano a mano con il software testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-227">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="59dc9-228">Gli oggetti che usano un approccio POCO sono meno vincolati, più flessibili e più semplici da testare, in quanto possono funzionare senza che sia presente un database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-228">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="59dc9-229">Con i POCO disponibili è possibile creare un Entity Data Model (EDM) in Visual Studio (vedere la figura 1).</span><span class="sxs-lookup"><span data-stu-id="59dc9-229">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="59dc9-230">Non utilizzeremo EDM per generare codice per le entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-230">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="59dc9-231">Al contrario, si vogliono usare le entità in modo amorevole.</span><span class="sxs-lookup"><span data-stu-id="59dc9-231">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="59dc9-232">Il modello EDM viene utilizzato solo per generare lo schema del database e fornire i metadati necessari per eseguire il mapping degli oggetti nel database EF4.</span><span class="sxs-lookup"><span data-stu-id="59dc9-232">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![test_01 EF](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="59dc9-234">**Figura 1**</span><span class="sxs-lookup"><span data-stu-id="59dc9-234">**Figure 1**</span></span>

<span data-ttu-id="59dc9-235">Nota: se si desidera sviluppare prima il modello EDM, è possibile generare codice POCO pulito da EDM.</span><span class="sxs-lookup"><span data-stu-id="59dc9-235">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="59dc9-236">A tale scopo, è possibile usare un'estensione di Visual Studio 2010 fornita dal team di programmabilità dei dati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-236">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="59dc9-237">Per scaricare l'estensione, avviare Gestione estensioni dal menu strumenti in Visual Studio ed eseguire una ricerca nella raccolta online di modelli per "POCO" (vedere la figura 2).</span><span class="sxs-lookup"><span data-stu-id="59dc9-237">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="59dc9-238">Sono disponibili diversi modelli POCO per EF.</span><span class="sxs-lookup"><span data-stu-id="59dc9-238">There are several POCO templates available for EF.</span></span> <span data-ttu-id="59dc9-239">Per ulteriori informazioni sull'utilizzo del modello, vedere " [procedura dettagliata: modello poco per il Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".</span><span class="sxs-lookup"><span data-stu-id="59dc9-239">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![test_02 EF](~/ef6/media/eftest-02.png)

<span data-ttu-id="59dc9-241">**Figura 2**</span><span class="sxs-lookup"><span data-stu-id="59dc9-241">**Figure 2**</span></span>

<span data-ttu-id="59dc9-242">Da questo punto di partenza POCO si analizzeranno due diversi approcci al codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-242">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="59dc9-243">Il primo approccio viene chiamato l'approccio EF perché sfrutta le astrazioni dall'API Entity Framework per implementare unità di lavoro e repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-243">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="59dc9-244">Nel secondo approccio si creeranno le astrazioni del repository personalizzate e quindi si vedranno i vantaggi e gli svantaggi di ogni approccio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-244">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="59dc9-245">Si inizierà con l'esplorazione dell'approccio EF.</span><span class="sxs-lookup"><span data-stu-id="59dc9-245">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="59dc9-246">Implementazione incentrata su EF</span><span class="sxs-lookup"><span data-stu-id="59dc9-246">An EF Centric Implementation</span></span>

<span data-ttu-id="59dc9-247">Si consideri l'azione del controller seguente da un progetto MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="59dc9-247">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="59dc9-248">L'azione recupera un oggetto Employee e restituisce un risultato per visualizzare una visualizzazione dettagliata del dipendente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-248">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="59dc9-249">Il codice è testabile?</span><span class="sxs-lookup"><span data-stu-id="59dc9-249">Is the code testable?</span></span> <span data-ttu-id="59dc9-250">Sono necessari almeno due test per verificare il comportamento dell'azione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-250">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="59dc9-251">Prima di tutto, è necessario verificare che l'azione restituisca la visualizzazione corretta, ovvero un semplice test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-251">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="59dc9-252">Si vuole anche scrivere un test per verificare che l'azione recuperi il dipendente corretto e che si desideri eseguire questa operazione senza eseguire il codice per eseguire query sul database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-252">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="59dc9-253">Tenere presente che si vuole isolare il codice sottoposto a test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-253">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="59dc9-254">L'isolamento assicurerà che il test non riesca a causa di un bug nel codice di accesso ai dati o nella configurazione del database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-254">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="59dc9-255">Se il test ha esito negativo, è possibile che sia presente un bug nella logica del controller e non in un componente di sistema di livello inferiore.</span><span class="sxs-lookup"><span data-stu-id="59dc9-255">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="59dc9-256">Per ottenere l'isolamento, sono necessarie alcune astrazioni, come le interfacce presentate in precedenza per i repository e le unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="59dc9-256">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="59dc9-257">Tenere presente che il modello di repository è progettato per mediare tra gli oggetti di dominio e il livello di mapping dei dati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-257">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="59dc9-258">In questo scenario EF4 *è* il livello di mapping dei dati e fornisce già un'astrazione di tipo repository denominata IObjectSet &lt; T &gt; (dallo spazio dei nomi System. Data. Objects).</span><span class="sxs-lookup"><span data-stu-id="59dc9-258">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="59dc9-259">La definizione dell'interfaccia ha un aspetto simile al seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-259">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="59dc9-260">IObjectSet &lt; t &gt; soddisfa i requisiti per un repository perché è simile a una raccolta di oggetti (tramite IEnumerable &lt; T &gt; ) e fornisce metodi per aggiungere e rimuovere oggetti dalla raccolta simulata.</span><span class="sxs-lookup"><span data-stu-id="59dc9-260">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="59dc9-261">I metodi di collegamento e scollegamento espongono funzionalità aggiuntive dell'API EF4.</span><span class="sxs-lookup"><span data-stu-id="59dc9-261">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="59dc9-262">Per usare IObjectSet &lt; T &gt; come interfaccia per i repository è necessaria un'astrazione di unità di lavoro per associare i repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-262">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="59dc9-263">Un'implementazione concreta di questa interfaccia comunicherà con SQL Server ed è facile da creare usando la classe ObjectContext da EF4.</span><span class="sxs-lookup"><span data-stu-id="59dc9-263">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="59dc9-264">La classe ObjectContext è l'unità di lavoro reale nell'API EF4.</span><span class="sxs-lookup"><span data-stu-id="59dc9-264">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="59dc9-265">L'introduzione di un IObjectSet &lt; T &gt; è semplice come richiamare il Metodo CreateObjectSet dell'oggetto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="59dc9-265">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="59dc9-266">Dietro le quinte il Framework utilizzerà i metadati forniti in EDM per produrre un oggetto concreto &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-266">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="59dc9-267">Si tratterà di restituire l' &lt; interfaccia IObjectSet T &gt; perché consente di mantenere la testabilità nel codice client.</span><span class="sxs-lookup"><span data-stu-id="59dc9-267">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="59dc9-268">Questa implementazione concreta è utile nell'ambiente di produzione, ma è necessario concentrarsi sull'uso dell'astrazione IUnitOfWork per semplificare i test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-268">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="59dc9-269">Il test viene raddoppiato</span><span class="sxs-lookup"><span data-stu-id="59dc9-269">The Test Doubles</span></span>

<span data-ttu-id="59dc9-270">Per isolare l'azione del controller, è necessario avere la possibilità di passare tra l'unità di lavoro reale (supportata da un ObjectContext) e un'unità di lavoro di tipo Double o "Fake" (esecuzione di operazioni in memoria).</span><span class="sxs-lookup"><span data-stu-id="59dc9-270">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="59dc9-271">L'approccio comune per eseguire questo tipo di cambio consiste nel non consentire al controller MVC di creare un'istanza di un'unità di lavoro, ma di passare l'unità di lavoro al controller come parametro del costruttore.</span><span class="sxs-lookup"><span data-stu-id="59dc9-271">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="59dc9-272">Il codice precedente è un esempio di inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="59dc9-272">The above code is an example of dependency injection.</span></span> <span data-ttu-id="59dc9-273">Il controller non consente al controller di creare la dipendenza (unità di lavoro), ma di inserire la dipendenza nel controller.</span><span class="sxs-lookup"><span data-stu-id="59dc9-273">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="59dc9-274">In un progetto MVC è comune usare una factory del controller personalizzata insieme a un contenitore di inversione del controllo (IoC) per automatizzare l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="59dc9-274">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="59dc9-275">Questi argomenti esulano dall'ambito di questo articolo, ma è possibile ottenere altre informazioni seguendo i riferimenti alla fine di questo articolo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-275">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="59dc9-276">Un'implementazione fittizia di un'unità di lavoro che è possibile usare per il test potrebbe essere simile alla seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-276">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="59dc9-277">Si noti che l'unità di lavoro fittizia espone una proprietà sottoposta a commit.</span><span class="sxs-lookup"><span data-stu-id="59dc9-277">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="59dc9-278">A volte è utile aggiungere funzionalità a una classe fittizia che facilita i test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-278">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="59dc9-279">In questo caso è facile osservare se il codice consente di eseguire il commit di un'unità di lavoro controllando la proprietà di cui è stato eseguito il commit.</span><span class="sxs-lookup"><span data-stu-id="59dc9-279">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="59dc9-280">È necessario anche un IObjectSet fittizio &lt; &gt; per mantenere gli oggetti Employee e cartellino in memoria.</span><span class="sxs-lookup"><span data-stu-id="59dc9-280">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="59dc9-281">È possibile fornire una singola implementazione usando i generics.</span><span class="sxs-lookup"><span data-stu-id="59dc9-281">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="59dc9-282">Questo test doppia delega la maggior parte del lavoro a un &lt; oggetto HashSet T sottostante &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-282">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="59dc9-283">Si noti che IObjectSet &lt; t &gt; richiede un vincolo generico che applica t come classe (un tipo di riferimento) e impone anche l'implementazione di IQueryable &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-283">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="59dc9-284">È facile fare in modo che una raccolta in memoria appaia come IQueryable &lt; T &gt; usando l'operatore LINQ standard AsQueryable.</span><span class="sxs-lookup"><span data-stu-id="59dc9-284">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="59dc9-285">Test</span><span class="sxs-lookup"><span data-stu-id="59dc9-285">The Tests</span></span>

<span data-ttu-id="59dc9-286">Gli unit test tradizionali utilizzeranno una singola classe di test per conservare tutti i test per tutte le azioni in un singolo controller MVC.</span><span class="sxs-lookup"><span data-stu-id="59dc9-286">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="59dc9-287">È possibile scrivere questi test, o qualsiasi tipo di unit test, usando i Fakes in memoria compilati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-287">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="59dc9-288">Tuttavia, per questo articolo si eviterà l'approccio della classe di test monolitico e si raggruppano i test per concentrarsi su un componente specifico di funzionalità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-288">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="59dc9-289">Ad esempio, "Crea nuovo dipendente" potrebbe essere la funzionalità che si desidera testare, quindi verrà utilizzata una singola classe di test per verificare l'azione del singolo controller responsabile della creazione di un nuovo dipendente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-289">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="59dc9-290">Per tutte queste classi di test con granularità fine sono necessari alcuni codici di configurazione comuni.</span><span class="sxs-lookup"><span data-stu-id="59dc9-290">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="59dc9-291">Ad esempio, è sempre necessario creare i repository in memoria e l'unità di lavoro falsa.</span><span class="sxs-lookup"><span data-stu-id="59dc9-291">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="59dc9-292">È anche necessaria un'istanza del controller dipendente con l'unità di lavoro fittizia inserita.</span><span class="sxs-lookup"><span data-stu-id="59dc9-292">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="59dc9-293">Questo codice di installazione comune verrà condiviso tra le classi di test usando una classe base.</span><span class="sxs-lookup"><span data-stu-id="59dc9-293">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="59dc9-294">Il "oggetto Mother" usato nella classe base è un modello comune per la creazione di dati di test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-294">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="59dc9-295">Un oggetto Mother contiene metodi factory per creare un'istanza delle entità di test da usare in più fixture di test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-295">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="59dc9-296">È possibile usare EmployeeControllerTestBase come classe di base per un numero di fixture di test (vedere la figura 3).</span><span class="sxs-lookup"><span data-stu-id="59dc9-296">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="59dc9-297">Ogni fixture di test eseguirà il test di un'azione specifica del controller.</span><span class="sxs-lookup"><span data-stu-id="59dc9-297">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="59dc9-298">Una fixture di test, ad esempio, si concentra sul test dell'azione di creazione usata durante una richiesta HTTP GET (per visualizzare la visualizzazione per la creazione di un dipendente) e un'altra fixture si concentrerà sull'azione di creazione usata in una richiesta HTTP POST (per ottenere informazioni inviate dall'utente per creare un dipendente).</span><span class="sxs-lookup"><span data-stu-id="59dc9-298">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="59dc9-299">Ogni classe derivata è responsabile solo per la configurazione necessaria nel contesto specifico e per fornire le asserzioni necessarie per verificare i risultati per il contesto di test specifico.</span><span class="sxs-lookup"><span data-stu-id="59dc9-299">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![test_03 EF](~/ef6/media/eftest-03.png)

<span data-ttu-id="59dc9-301">**Figura 3**</span><span class="sxs-lookup"><span data-stu-id="59dc9-301">**Figure 3**</span></span>

<span data-ttu-id="59dc9-302">La convenzione di denominazione e lo stile di test presentati qui non sono necessari per il codice testabile. si tratta solo di un approccio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-302">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="59dc9-303">La figura 4 illustra i test in esecuzione nel plug-in Test Runner di Jet Brains per Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="59dc9-303">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![test_04 EF](~/ef6/media/eftest-04.png)

<span data-ttu-id="59dc9-305">**Figura 4**</span><span class="sxs-lookup"><span data-stu-id="59dc9-305">**Figure 4**</span></span>

<span data-ttu-id="59dc9-306">Con una classe di base per gestire il codice di installazione condiviso, gli unit test per ogni azione del controller sono di piccole dimensioni e facili da scrivere.</span><span class="sxs-lookup"><span data-stu-id="59dc9-306">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="59dc9-307">I test verranno eseguiti rapidamente (dal momento che si eseguono operazioni in memoria) e non dovrebbero avere esito negativo a causa dell'infrastruttura non correlata o delle preoccupazioni ambientali (perché l'unità sottoposta a test è isolata).</span><span class="sxs-lookup"><span data-stu-id="59dc9-307">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="59dc9-308">In questi test la classe base esegue la maggior parte delle operazioni di installazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-308">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="59dc9-309">Tenere presente che il costruttore della classe base crea il repository in memoria, un'unità di lavoro fittizia e un'istanza della classe EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="59dc9-309">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="59dc9-310">La classe di test deriva da questa classe di base e si concentra sulle specifiche di test del metodo create.</span><span class="sxs-lookup"><span data-stu-id="59dc9-310">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="59dc9-311">In questo caso, le specifiche si riducono ai passaggi "Arrange, Act e Assert" che verranno visualizzati in qualsiasi procedura di unit test:</span><span class="sxs-lookup"><span data-stu-id="59dc9-311">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="59dc9-312">Creare un oggetto newEmployee per simulare i dati in ingresso.</span><span class="sxs-lookup"><span data-stu-id="59dc9-312">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="59dc9-313">Richiamare l'azione di creazione di EmployeeController e passare newEmployee.</span><span class="sxs-lookup"><span data-stu-id="59dc9-313">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="59dc9-314">Verificare che l'azione di creazione produca i risultati previsti (il dipendente viene visualizzato nel repository).</span><span class="sxs-lookup"><span data-stu-id="59dc9-314">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="59dc9-315">Ciò che abbiamo creato ci permette di testare qualsiasi azione EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="59dc9-315">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="59dc9-316">Ad esempio, quando si scrivono test per l'azione index del controller Employee, è possibile ereditare dalla classe base di test per stabilire la stessa configurazione di base per i test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-316">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="59dc9-317">Anche in questo caso la classe base creerà il repository in memoria, l'unità di lavoro falsa e un'istanza di EmployeeController.</span><span class="sxs-lookup"><span data-stu-id="59dc9-317">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="59dc9-318">I test per l'azione di indice devono essere concentrati solo sulla chiamata all'azione dell'indice e sul test delle qualità del modello restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-318">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="59dc9-319">I test creati con Fakes in memoria sono orientati a testare lo *stato* del software.</span><span class="sxs-lookup"><span data-stu-id="59dc9-319">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="59dc9-320">Ad esempio, durante il test dell'azione di creazione si vuole controllare lo stato del repository dopo l'esecuzione dell'azione di creazione: il repository è in possesso del nuovo dipendente?</span><span class="sxs-lookup"><span data-stu-id="59dc9-320">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="59dc9-321">In seguito verrà esaminato il test basato sull'interazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-321">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="59dc9-322">Il test basato sull'interazione chiederà se il codice sottoposto a test ha richiamato i metodi appropriati sugli oggetti e ha passato i parametri corretti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-322">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="59dc9-323">Per il momento si passerà alla copertura di un altro modello di progettazione, ovvero il carico Lazy.</span><span class="sxs-lookup"><span data-stu-id="59dc9-323">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="59dc9-324">Caricamento eager e caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="59dc9-324">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="59dc9-325">A un certo punto dell'applicazione Web MVC ASP.NET, è possibile che si desideri visualizzare le informazioni di un dipendente e includere le schede temporali associate del dipendente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-325">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="59dc9-326">Ad esempio, è possibile che sia presente una visualizzazione Riepilogo della scheda tempo che mostra il nome del dipendente e il numero totale di schede temporali nel sistema.</span><span class="sxs-lookup"><span data-stu-id="59dc9-326">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="59dc9-327">Per implementare questa funzionalità, è possibile adottare diversi approcci.</span><span class="sxs-lookup"><span data-stu-id="59dc9-327">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="59dc9-328">Proiezione</span><span class="sxs-lookup"><span data-stu-id="59dc9-328">Projection</span></span>

<span data-ttu-id="59dc9-329">Un semplice approccio per creare il riepilogo consiste nel costruire un modello dedicato alle informazioni che si desidera visualizzare nella visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-329">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="59dc9-330">In questo scenario il modello potrebbe essere simile al seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-330">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="59dc9-331">Si noti che EmployeeSummaryViewModel non è un'entità, in altre parole non è un elemento che si desidera salvare in modo permanente nel database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-331">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="59dc9-332">Questa classe verrà usata solo per eseguire il shuffling dei dati nella visualizzazione in modo fortemente tipizzato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-332">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="59dc9-333">Il modello di visualizzazione è simile a un oggetto di trasferimento dati (DTO) perché non contiene alcun comportamento (nessun metodo): solo proprietà.</span><span class="sxs-lookup"><span data-stu-id="59dc9-333">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="59dc9-334">Le proprietà conterranno i dati che è necessario spostare.</span><span class="sxs-lookup"><span data-stu-id="59dc9-334">The properties will hold the data we need to move.</span></span> <span data-ttu-id="59dc9-335">È facile creare un'istanza di questo modello di visualizzazione utilizzando l'operatore di proiezione standard di LINQ, ovvero l'operatore Select.</span><span class="sxs-lookup"><span data-stu-id="59dc9-335">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="59dc9-336">Sono disponibili due funzionalità rilevanti per il codice precedente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-336">There are two notable features to the above code.</span></span> <span data-ttu-id="59dc9-337">Primo: il codice è facile da testare perché è ancora facile da osservare e isolare.</span><span class="sxs-lookup"><span data-stu-id="59dc9-337">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="59dc9-338">L'operatore Select funziona anche con i falsi in memoria, come avviene per l'unità di lavoro reale.</span><span class="sxs-lookup"><span data-stu-id="59dc9-338">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="59dc9-339">Il secondo aspetto rilevante è il modo in cui il codice consente a EF4 di generare una singola query efficiente per assemblare le informazioni sui dipendenti e sulle schede temporali insieme.</span><span class="sxs-lookup"><span data-stu-id="59dc9-339">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="59dc9-340">Sono state caricate informazioni sui dipendenti e informazioni sulle schede temporali nello stesso oggetto senza usare API particolari.</span><span class="sxs-lookup"><span data-stu-id="59dc9-340">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="59dc9-341">Il codice ha semplicemente espresso le informazioni necessarie usando gli operatori LINQ standard che funzionano con origini dati in memoria e origini dati remote.</span><span class="sxs-lookup"><span data-stu-id="59dc9-341">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="59dc9-342">EF4 è stato in grado di convertire gli alberi delle espressioni generati dalla query LINQ e dal \# compilatore C in un'unica ed efficiente query T-SQL.</span><span class="sxs-lookup"><span data-stu-id="59dc9-342">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="59dc9-343">In altri casi non si vuole usare un modello di visualizzazione o un oggetto DTO, ma con entità reali.</span><span class="sxs-lookup"><span data-stu-id="59dc9-343">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="59dc9-344">Quando sappiamo che abbiamo bisogno di un dipendente *e* delle schede temporali dei dipendenti, possiamo caricare con entusiasmo i dati correlati in modo non intrusivo ed efficiente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-344">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="59dc9-345">Caricamento eager esplicito</span><span class="sxs-lookup"><span data-stu-id="59dc9-345">Explicit Eager Loading</span></span>

<span data-ttu-id="59dc9-346">Quando si desidera caricare in modo eager le informazioni sulle entità correlate, è necessario un meccanismo per la logica di business (o in questo scenario, la logica dell'azione del controller) per esprimere il proprio desiderio al repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-346">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="59dc9-347">La classe EF4 ObjectQuery &lt; T &gt; definisce un metodo include per specificare gli oggetti correlati da recuperare durante una query.</span><span class="sxs-lookup"><span data-stu-id="59dc9-347">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="59dc9-348">Tenere presente che l'ObjectContext EF4 espone le entità tramite la &lt; classe concrete t &gt; di oggetti che eredita da ObjectQuery &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-348">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="59dc9-349">Se si usano &lt; &gt; i riferimenti a ObjectSet T nell'azione del controller, è possibile scrivere il codice seguente per specificare un carico eager delle informazioni sulle schede temporali per ogni dipendente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-349">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="59dc9-350">Tuttavia, poiché si sta tentando di evitare che il codice sia testabile, non è possibile esporre ObjectSet &lt; T &gt; dall'esterno della classe Real Unit of work.</span><span class="sxs-lookup"><span data-stu-id="59dc9-350">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="59dc9-351">Si basa invece sull' &lt; interfaccia IObjectSet t &gt; , che è più semplice da falsificare, ma IObjectSet t non &lt; &gt; definisce un metodo di inclusione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-351">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="59dc9-352">La bellezza di LINQ è la possibilità di creare un proprio operatore di inclusione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-352">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="59dc9-353">Si noti che l'operatore di inclusione è definito come metodo di estensione per IQueryable &lt; t &gt; anziché IObjectSet &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-353">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="59dc9-354">Questo consente di usare il metodo con una gamma più ampia di tipi possibili, tra cui IQueryable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; e ObjectSet t &lt; &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-354">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="59dc9-355">Nel caso in cui la sequenza sottostante non sia un vero e proprio EF4 ObjectQuery &lt; T &gt; , non si verifica alcun danno e l'operatore di inclusione non è un'operazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-355">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="59dc9-356">Se la sequenza sottostante *è* un oggetto ObjectQuery &lt; t &gt; (o derivato da OBJECTQUERY &lt; t &gt; ), EF4 visualizzerà il requisito per i dati aggiuntivi e formulerà la query SQL corretta.</span><span class="sxs-lookup"><span data-stu-id="59dc9-356">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="59dc9-357">Con questo nuovo operatore, è possibile richiedere in modo esplicito un carico eager delle informazioni sulle schede temporali dal repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-357">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="59dc9-358">Quando viene eseguito su un ObjectContext reale, il codice produce la seguente query singola.</span><span class="sxs-lookup"><span data-stu-id="59dc9-358">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="59dc9-359">La query raccoglie informazioni sufficienti dal database in un unico viaggio per materializzare gli oggetti Employee e popolare completamente la relativa proprietà cartellini.</span><span class="sxs-lookup"><span data-stu-id="59dc9-359">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="59dc9-360">Il bel notizia è che il codice all'interno del metodo di azione rimane completamente testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-360">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="59dc9-361">Non è necessario fornire funzionalità aggiuntive per i Fakes per supportare l'operatore di inclusione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-361">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="59dc9-362">La cattiva notizia è che è stato necessario usare l'operatore di inclusione all'interno del codice che volevamo ignorare la persistenza.</span><span class="sxs-lookup"><span data-stu-id="59dc9-362">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="59dc9-363">Questo è un esempio principale del tipo di compromessi che è necessario valutare quando si compila codice testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-363">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="59dc9-364">In alcuni casi è necessario lasciare che la persistenza riguardi la perdita al di fuori dell'astrazione del repository per soddisfare gli obiettivi delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="59dc9-364">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="59dc9-365">L'alternativa al caricamento eager è il caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="59dc9-365">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="59dc9-366">Il caricamento lazy significa che il codice aziendale *non* è necessario per annunciare in modo esplicito il requisito per i dati associati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-366">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="59dc9-367">Vengono invece utilizzate le entità nell'applicazione e se sono necessari dati aggiuntivi Entity Framework caricherà i dati su richiesta.</span><span class="sxs-lookup"><span data-stu-id="59dc9-367">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="59dc9-368">Caricamento lazy</span><span class="sxs-lookup"><span data-stu-id="59dc9-368">Lazy Loading</span></span>

<span data-ttu-id="59dc9-369">È facile immaginare uno scenario in cui non si conoscono i dati necessari per una logica di business.</span><span class="sxs-lookup"><span data-stu-id="59dc9-369">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="59dc9-370">È possibile che la logica necessiti di un oggetto dipendente, ma è possibile creare branch in percorsi di esecuzione diversi, in cui alcuni di questi percorsi richiedono informazioni sulle schede temporali del dipendente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-370">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="59dc9-371">Scenari come questo sono perfetti per il caricamento lazy implicito perché i dati vengono visualizzati magicamente in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="59dc9-371">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="59dc9-372">Il caricamento lazy, anche noto come caricamento posticipato, comporta alcuni requisiti sugli oggetti entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-372">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="59dc9-373">POCO con la vera ignoranza di persistenza non deve soddisfare i requisiti del livello di persistenza, ma la vera ignoranza persistenza è praticamente impossibile da raggiungere.</span><span class="sxs-lookup"><span data-stu-id="59dc9-373">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="59dc9-374">Viene invece misurata l'ignoranza della persistenza nei gradi relativi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-374">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="59dc9-375">Sarebbe deplorevole se avessimo dovuto ereditare da una classe base orientata alla persistenza o usare una raccolta specializzata per ottenere il caricamento lazy in POCO.</span><span class="sxs-lookup"><span data-stu-id="59dc9-375">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="59dc9-376">Fortunatamente, EF4 dispone di una soluzione meno intrusiva.</span><span class="sxs-lookup"><span data-stu-id="59dc9-376">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="59dc9-377">Praticamente non rilevabile</span><span class="sxs-lookup"><span data-stu-id="59dc9-377">Virtually Undetectable</span></span>

<span data-ttu-id="59dc9-378">Quando si usano oggetti POCO, EF4 può generare dinamicamente proxy di runtime per le entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-378">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="59dc9-379">Questi proxy incapsulano in modo invisibile i POCO materializzati e forniscono servizi aggiuntivi intercettando ogni operazione get e set di proprietà per eseguire operazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="59dc9-379">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="59dc9-380">Un servizio di questo tipo è la funzionalità di caricamento lazy che si sta cercando.</span><span class="sxs-lookup"><span data-stu-id="59dc9-380">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="59dc9-381">Un altro servizio è un meccanismo efficiente di rilevamento delle modifiche che può registrare quando il programma modifica i valori delle proprietà di un'entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-381">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="59dc9-382">L'elenco di modifiche viene utilizzato da ObjectContext durante il metodo SaveChanges per salvare in modo permanente tutte le entità modificate utilizzando i comandi UPDATE.</span><span class="sxs-lookup"><span data-stu-id="59dc9-382">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="59dc9-383">Per il funzionamento di questi proxy, tuttavia, è necessario un modo per associare le operazioni get e set di proprietà a un'entità e i proxy raggiungono questo obiettivo eseguendo l'override dei membri virtuali.</span><span class="sxs-lookup"><span data-stu-id="59dc9-383">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="59dc9-384">Pertanto, se si desidera eseguire il caricamento lazy implicito e il rilevamento delle modifiche efficiente, è necessario tornare alle definizioni delle classi POCO e contrassegnare le proprietà come virtuali.</span><span class="sxs-lookup"><span data-stu-id="59dc9-384">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="59dc9-385">Possiamo comunque affermare che l'entità Employee è per lo più ignorante per la persistenza.</span><span class="sxs-lookup"><span data-stu-id="59dc9-385">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="59dc9-386">L'unico requisito consiste nell'usare i membri virtuali e questa operazione non influisca sulla testabilità del codice.</span><span class="sxs-lookup"><span data-stu-id="59dc9-386">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="59dc9-387">Non è necessario derivare da alcuna classe di base speciale o persino utilizzare una raccolta speciale dedicata al caricamento lazy.</span><span class="sxs-lookup"><span data-stu-id="59dc9-387">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="59dc9-388">Come illustrato nel codice, qualsiasi classe che implementa ICollection &lt; T &gt; è disponibile per l'esecuzione di entità correlate.</span><span class="sxs-lookup"><span data-stu-id="59dc9-388">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="59dc9-389">È anche necessario apportare una piccola modifica all'interno dell'unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="59dc9-389">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="59dc9-390">Il caricamento lazy è *disattivato* per impostazione predefinita quando si utilizza direttamente un oggetto ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="59dc9-390">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="59dc9-391">È possibile impostare una proprietà per la proprietà ContextOptions per abilitare il caricamento posticipato. è possibile impostare questa proprietà all'interno dell'unità di lavoro reale se si vuole abilitare il caricamento lazy ovunque.</span><span class="sxs-lookup"><span data-stu-id="59dc9-391">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="59dc9-392">Con il caricamento lazy implicito abilitato, il codice dell'applicazione può usare un dipendente e le schede temporali associate del dipendente, pur rimanendo a conoscenza del lavoro necessario per EF per caricare i dati aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-392">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="59dc9-393">Il caricamento lazy rende più semplice la scrittura del codice dell'applicazione e, con il proxy magico, il codice rimane completamente testabile.</span><span class="sxs-lookup"><span data-stu-id="59dc9-393">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="59dc9-394">I fake in memoria dell'unità di lavoro possono semplicemente precaricare entità fittizie con dati associati quando necessario durante un test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-394">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="59dc9-395">A questo punto, l'attenzione verrà ricavata dalla creazione di repository con IObjectSet &lt; T &gt; e si osserveranno le astrazioni per nascondere tutti i segni del Framework di persistenza.</span><span class="sxs-lookup"><span data-stu-id="59dc9-395">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="59dc9-396">Repository personalizzati</span><span class="sxs-lookup"><span data-stu-id="59dc9-396">Custom Repositories</span></span>

<span data-ttu-id="59dc9-397">Quando è stato presentato per la prima volta il modello di progettazione unità di lavoro in questo articolo, è stato fornito un codice di esempio per l'aspetto dell'unità di lavoro.</span><span class="sxs-lookup"><span data-stu-id="59dc9-397">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="59dc9-398">Verrà ripresentata questa idea originale usando lo scenario Employee e Employee Time Card che abbiamo lavorato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-398">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="59dc9-399">La differenza principale tra questa unità di lavoro e l'unità di lavoro creata nell'ultima sezione è il modo in cui questa unità di lavoro non usa astrazioni dal Framework EF4 (non esiste alcun IObjectSet &lt; T &gt; ).</span><span class="sxs-lookup"><span data-stu-id="59dc9-399">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="59dc9-400">IObjectSet &lt; T &gt; funziona bene come un'interfaccia del repository, ma l'API che espone potrebbe non essere perfettamente allineata alle esigenze dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-400">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="59dc9-401">In questo prossimo approccio si rappresenteranno i repository usando un' &lt; astrazione IRepository T personalizzata &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-401">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="59dc9-402">Molti sviluppatori che seguono progettazione basata su test, progettazione basata su comportamenti e metodologie basate su dominio preferiscono &lt; l' &gt; approccio IRepository T per diversi motivi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-402">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="59dc9-403">In primo luogo, &lt; l' &gt; interfaccia IRepository T rappresenta un livello "anti-danneggiamento".</span><span class="sxs-lookup"><span data-stu-id="59dc9-403">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="59dc9-404">Come descritto da Eric Evans nel suo libro di progettazione basato su dominio, un livello Anti-danneggiamento mantiene il codice del dominio dalle API dell'infrastruttura, ad esempio un'API di persistenza.</span><span class="sxs-lookup"><span data-stu-id="59dc9-404">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="59dc9-405">In secondo luogo, gli sviluppatori possono compilare metodi nel repository che soddisfano le esigenze esatte di un'applicazione (come rilevato durante la scrittura dei test).</span><span class="sxs-lookup"><span data-stu-id="59dc9-405">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="59dc9-406">Ad esempio, potrebbe essere necessario individuare spesso una singola entità usando un valore ID, quindi è possibile aggiungere un metodo FindById all'interfaccia del repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-406">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="59dc9-407">&lt; &gt; La definizione di IRepository T sarà simile alla seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-407">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="59dc9-408">Si noti che verrà eseguito il rollback dell'utilizzo di un' &lt; interfaccia IQueryable T &gt; per esporre le raccolte di entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-408">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="59dc9-409">IQueryable &lt; T &gt; consente la propagazione degli alberi delle espressioni LINQ nel provider EF4 e fornisce al provider una visualizzazione olistica della query.</span><span class="sxs-lookup"><span data-stu-id="59dc9-409">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="59dc9-410">Una seconda opzione consiste nel restituire IEnumerable &lt; T &gt; , il che significa che il provider LINQ EF4 visualizzerà solo le espressioni compilate all'interno del repository.</span><span class="sxs-lookup"><span data-stu-id="59dc9-410">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="59dc9-411">Eventuali operazioni di raggruppamento, ordinamento e proiezione eseguite all'esterno del repository non verranno composte nel comando SQL inviato al database, il che può influire negativamente sulle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="59dc9-411">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="59dc9-412">D'altra parte, un repository che restituisce solo &lt; &gt; i risultati IEnumerable T non sorprenderà mai con un nuovo comando SQL.</span><span class="sxs-lookup"><span data-stu-id="59dc9-412">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="59dc9-413">Entrambi gli approcci funzioneranno ed entrambi gli approcci rimarranno testabili.</span><span class="sxs-lookup"><span data-stu-id="59dc9-413">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="59dc9-414">È semplice fornire un'unica implementazione dell' &lt; interfaccia IRepository T &gt; usando i generics e l'API EF4 ObjectContext.</span><span class="sxs-lookup"><span data-stu-id="59dc9-414">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="59dc9-415">L' &lt; approccio IRepository T &gt; fornisce un controllo aggiuntivo sulle query perché un client deve richiamare un metodo per ottenere un'entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-415">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="59dc9-416">All'interno del metodo è possibile fornire controlli aggiuntivi e operatori LINQ per applicare i vincoli dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-416">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="59dc9-417">Si noti che l'interfaccia presenta due vincoli per il parametro di tipo generico.</span><span class="sxs-lookup"><span data-stu-id="59dc9-417">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="59dc9-418">Il primo vincolo è la classe const Taint required by ObjectSet &lt; T &gt; e il secondo vincolo impone alle entità di implementare IEntity, ovvero un'astrazione creata per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-418">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="59dc9-419">L'interfaccia IEntity impone alle entità di avere una proprietà ID leggibile ed è quindi possibile usare questa proprietà nel metodo FindById.</span><span class="sxs-lookup"><span data-stu-id="59dc9-419">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="59dc9-420">IEntity è definito con il codice seguente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-420">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="59dc9-421">IEntity può essere considerato una piccola violazione dell'ignoranza di persistenza, poiché le entità sono necessarie per implementare questa interfaccia.</span><span class="sxs-lookup"><span data-stu-id="59dc9-421">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="59dc9-422">Tenere presente che la persistenza dell'ignoranza riguarda i compromessi e per molte delle funzionalità di FindById supererà il vincolo imposto dall'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="59dc9-422">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="59dc9-423">L'interfaccia non ha alcun effetto sulla testabilità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-423">The interface has no impact on testability.</span></span>

<span data-ttu-id="59dc9-424">La creazione di un'istanza di un IRepository Live &lt; &gt; richiede un ObjectContext EF4, pertanto un'implementazione di un'unità di lavoro concreta deve gestire la creazione di istanze.</span><span class="sxs-lookup"><span data-stu-id="59dc9-424">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="59dc9-425">Uso del repository personalizzato</span><span class="sxs-lookup"><span data-stu-id="59dc9-425">Using the Custom Repository</span></span>

<span data-ttu-id="59dc9-426">L'uso del repository personalizzato non è significativamente diverso dall'uso del repository basato su IObjectSet &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-426">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="59dc9-427">Anziché applicare gli operatori LINQ direttamente a una proprietà, è necessario prima richiamare uno dei metodi del repository per ottenere un &lt; riferimento IQueryable T &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-427">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="59dc9-428">Si noti che l'operatore di inclusione personalizzato implementato in precedenza funzionerà senza alcuna modifica.</span><span class="sxs-lookup"><span data-stu-id="59dc9-428">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="59dc9-429">Il metodo FindById del repository rimuove la logica duplicata dalle azioni che tentano di recuperare una singola entità.</span><span class="sxs-lookup"><span data-stu-id="59dc9-429">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="59dc9-430">Non esiste alcuna differenza significativa per la testabilità dei due approcci esaminati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-430">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="59dc9-431">Potremmo fornire implementazioni fittizie di IRepository &lt; T &gt; compilando classi concrete supportate dal &lt; dipendente di HashSet &gt; , proprio come quello che abbiamo fatto nell'ultima sezione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-431">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="59dc9-432">Tuttavia, alcuni sviluppatori preferiscono usare oggetti fittizi e Framework di oggetti fittizi anziché creare Fake.</span><span class="sxs-lookup"><span data-stu-id="59dc9-432">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="59dc9-433">Si esaminerà l'uso di simulazioni per testare l'implementazione e discutere le differenze tra simulazioni e Fakes nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="59dc9-433">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="59dc9-434">Test con simulazioni</span><span class="sxs-lookup"><span data-stu-id="59dc9-434">Testing with Mocks</span></span>

<span data-ttu-id="59dc9-435">Esistono diversi approcci per la creazione di un "Double di test" di Martin Fowler.</span><span class="sxs-lookup"><span data-stu-id="59dc9-435">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="59dc9-436">Un doppio di test (ad esempio una doppia acrobazia del film) è un oggetto compilato in "stand in" per gli oggetti di produzione reali durante i test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-436">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="59dc9-437">I repository in memoria creati sono test doppi per i repository che comunicano con SQL Server.</span><span class="sxs-lookup"><span data-stu-id="59dc9-437">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="59dc9-438">Abbiamo visto come usare questi test-doppi durante gli unit test per isolare il codice e tenere i test eseguiti rapidamente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-438">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="59dc9-439">I doppi test creati hanno implementazioni reali e funzionanti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-439">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="59dc9-440">Dietro le quinte viene archiviata una raccolta concreta di oggetti che aggiungono e rimuovono oggetti da questa raccolta quando si modifica il repository durante un test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-440">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="59dc9-441">Alcuni sviluppatori desiderano creare un test doppio in questo modo, con codice reale e implementazioni di lavoro.</span><span class="sxs-lookup"><span data-stu-id="59dc9-441">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="59dc9-442">Questi doppi test sono quelli che chiamiamo *Fakes*.</span><span class="sxs-lookup"><span data-stu-id="59dc9-442">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="59dc9-443">Hanno implementazioni di lavoro, ma non sono abbastanza reali per l'uso in produzione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-443">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="59dc9-444">Il repository Fake non esegue effettivamente la scrittura nel database.</span><span class="sxs-lookup"><span data-stu-id="59dc9-444">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="59dc9-445">Il fake server SMTP non invia effettivamente un messaggio di posta elettronica tramite la rete.</span><span class="sxs-lookup"><span data-stu-id="59dc9-445">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="59dc9-446">Simulazioni rispetto a Fakes</span><span class="sxs-lookup"><span data-stu-id="59dc9-446">Mocks versus Fakes</span></span>

<span data-ttu-id="59dc9-447">Esiste un altro tipo di test doppio noto come *simulazione*.</span><span class="sxs-lookup"><span data-stu-id="59dc9-447">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="59dc9-448">Sebbene Fakes disponga di implementazioni di lavoro, le simulazioni sono prive di implementazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-448">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="59dc9-449">Con l'ausilio di un Framework di oggetti fittizio è possibile costruire questi oggetti fittizi in fase di esecuzione e usarli come doppi di test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-449">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="59dc9-450">In questa sezione verrà usato il Framework di simulazione open source MOQ.</span><span class="sxs-lookup"><span data-stu-id="59dc9-450">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="59dc9-451">Ecco un semplice esempio di uso di MOQ per creare dinamicamente un doppio di test per un repository Employee.</span><span class="sxs-lookup"><span data-stu-id="59dc9-451">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="59dc9-452">Per l'implementazione di un dipendente IRepository è richiesta la &lt; compilazione dinamica di un dipendente &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-452">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="59dc9-453">È possibile ottenere l'oggetto che implementa il &lt; dipendente IRepository &gt; accedendo alla proprietà dell'oggetto dell'oggetto fittizio &lt; T &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-453">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="59dc9-454">Si tratta di un oggetto interno che è possibile passare ai controller, che non sapranno se si tratta di un doppio test o di un repository reale.</span><span class="sxs-lookup"><span data-stu-id="59dc9-454">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="59dc9-455">È possibile richiamare i metodi sull'oggetto esattamente come si richiamerebbe metodi su un oggetto con un'implementazione reale.</span><span class="sxs-lookup"><span data-stu-id="59dc9-455">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="59dc9-456">Quando viene richiamato il metodo Add, è necessario chiedersi quale sarà il repository fittizio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-456">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="59dc9-457">Poiché non esiste alcuna implementazione dietro l'oggetto fittizio, Add non esegue alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-457">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="59dc9-458">Non esiste alcuna raccolta concreta dietro le quinte, come abbiamo fatto con i Fakes scritti, quindi il dipendente viene eliminato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-458">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="59dc9-459">Per quanto riguarda il valore restituito di FindById?</span><span class="sxs-lookup"><span data-stu-id="59dc9-459">What about the return value of FindById?</span></span> <span data-ttu-id="59dc9-460">In questo caso, l'oggetto fittizio esegue l'unica operazione che può essere eseguita, che restituisce un valore predefinito.</span><span class="sxs-lookup"><span data-stu-id="59dc9-460">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="59dc9-461">Poiché viene restituito un tipo di riferimento (un dipendente), il valore restituito è un valore null.</span><span class="sxs-lookup"><span data-stu-id="59dc9-461">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="59dc9-462">Le simulazioni potrebbero sembrare inutili; Tuttavia, esistono altre due funzionalità di simulazioni di cui non abbiamo parlato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-462">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="59dc9-463">In primo luogo, il Framework MOQ registra tutte le chiamate effettuate sull'oggetto fittizio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-463">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="59dc9-464">Più avanti nel codice è possibile richiedere MOQ se qualcuno ha richiamato il metodo Add o se chiunque ha richiamato il metodo FindById.</span><span class="sxs-lookup"><span data-stu-id="59dc9-464">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="59dc9-465">In seguito verrà illustrato in che modo è possibile utilizzare la funzionalità di registrazione "black box" nei test.</span><span class="sxs-lookup"><span data-stu-id="59dc9-465">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="59dc9-466">La seconda grande funzionalità è il modo in cui è possibile usare MOQ per programmare un oggetto fittizio con le *aspettative*.</span><span class="sxs-lookup"><span data-stu-id="59dc9-466">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="59dc9-467">Una previsione indica all'oggetto fittizio come rispondere a una determinata interazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-467">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="59dc9-468">È possibile, ad esempio, programmare un'attesa nella simulazione e indicare che restituirà un oggetto dipendente quando un utente richiama FindById.</span><span class="sxs-lookup"><span data-stu-id="59dc9-468">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="59dc9-469">Il Framework MOQ usa un'API di installazione e le espressioni lambda per programmare queste aspettative.</span><span class="sxs-lookup"><span data-stu-id="59dc9-469">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="59dc9-470">In questo esempio si chiede a MOQ di compilare dinamicamente un repository, quindi si programma il repository con una previsione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-470">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="59dc9-471">L'aspettativa indica all'oggetto fittizio di restituire un nuovo oggetto dipendente con un valore ID pari a 5 quando un utente richiama il metodo FindById passando un valore di 5.</span><span class="sxs-lookup"><span data-stu-id="59dc9-471">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="59dc9-472">Questo test ha esito positivo e non è stato necessario creare un'implementazione completa di Fake IRepository &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-472">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="59dc9-473">Esaminiamo nuovamente i test scritti in precedenza e li rielaborare per usare le simulazioni invece di Fakes.</span><span class="sxs-lookup"><span data-stu-id="59dc9-473">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="59dc9-474">Proprio come in precedenza, si userà una classe base per configurare le parti comuni dell'infrastruttura necessarie per tutti i test del controller.</span><span class="sxs-lookup"><span data-stu-id="59dc9-474">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="59dc9-475">Il codice di installazione rimane lo stesso.</span><span class="sxs-lookup"><span data-stu-id="59dc9-475">The setup code remains mostly the same.</span></span> <span data-ttu-id="59dc9-476">Invece di usare Fakes, verrà usato MOQ per costruire oggetti fittizi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-476">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="59dc9-477">La classe base dispone che l'unità di lavoro fittizia restituisca un repository fittizio quando il codice richiama la proprietà Employees.</span><span class="sxs-lookup"><span data-stu-id="59dc9-477">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="59dc9-478">Il resto dell'installazione fittizia verrà eseguita all'interno delle fixture di test dedicate a ogni scenario specifico.</span><span class="sxs-lookup"><span data-stu-id="59dc9-478">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="59dc9-479">Ad esempio, la fixture di test per l'azione index configurerà il repository fittizio per restituire un elenco di dipendenti quando l'azione richiama il metodo FindAll del repository fittizio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-479">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="59dc9-480">Ad eccezione delle aspettative, i test hanno un aspetto simile a quello dei test precedenti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-480">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="59dc9-481">Tuttavia, con la capacità di registrazione di un Framework fittizio è possibile affrontare i test da un angolo diverso.</span><span class="sxs-lookup"><span data-stu-id="59dc9-481">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="59dc9-482">Questa nuova prospettiva verrà esaminata nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="59dc9-482">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="59dc9-483">Stato rispetto al test di interazione</span><span class="sxs-lookup"><span data-stu-id="59dc9-483">State versus Interaction Testing</span></span>

<span data-ttu-id="59dc9-484">Esistono diverse tecniche che è possibile utilizzare per testare il software con oggetti fittizi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-484">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="59dc9-485">Un approccio consiste nell'usare il test basato sullo stato, che è quello che abbiamo fatto finora in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-485">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="59dc9-486">Il test basato sullo stato esegue asserzioni sullo stato del software.</span><span class="sxs-lookup"><span data-stu-id="59dc9-486">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="59dc9-487">Nell'ultimo test è stato richiamato un metodo di azione sul controller ed è stata creata un'asserzione sul modello da compilare.</span><span class="sxs-lookup"><span data-stu-id="59dc9-487">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="59dc9-488">Ecco alcuni altri esempi di stato di test:</span><span class="sxs-lookup"><span data-stu-id="59dc9-488">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="59dc9-489">Verificare che il repository contenga il nuovo oggetto Employee dopo l'esecuzione di create.</span><span class="sxs-lookup"><span data-stu-id="59dc9-489">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="59dc9-490">Verificare che il modello contenga un elenco di tutti i dipendenti dopo l'esecuzione dell'indice.</span><span class="sxs-lookup"><span data-stu-id="59dc9-490">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="59dc9-491">Verificare che il repository non contenga un determinato dipendente dopo l'esecuzione dell'operazione Delete.</span><span class="sxs-lookup"><span data-stu-id="59dc9-491">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="59dc9-492">Un altro approccio che verrà visualizzato con oggetti fittizi consiste nel verificare le *interazioni*.</span><span class="sxs-lookup"><span data-stu-id="59dc9-492">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="59dc9-493">Mentre il test basato sullo stato esegue asserzioni sullo stato degli oggetti, il test basato sull'interazione esegue asserzioni sulla modalità di interazione degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="59dc9-493">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="59dc9-494">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="59dc9-494">For example:</span></span>

-   <span data-ttu-id="59dc9-495">Verificare che il controller richiami il metodo Add del repository quando viene eseguita l'istruzione create.</span><span class="sxs-lookup"><span data-stu-id="59dc9-495">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="59dc9-496">Verificare che il controller richiami il metodo FindAll del repository quando l'indice viene eseguito.</span><span class="sxs-lookup"><span data-stu-id="59dc9-496">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="59dc9-497">Verificare che il controller richiami il metodo commit dell'unità di lavoro per salvare le modifiche quando viene eseguita la modifica.</span><span class="sxs-lookup"><span data-stu-id="59dc9-497">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="59dc9-498">I test di interazione spesso richiedono un minor numero di dati di test, perché non viene eseguito il poke all'interno delle raccolte e la verifica dei conteggi.</span><span class="sxs-lookup"><span data-stu-id="59dc9-498">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="59dc9-499">Ad esempio, se si sa che l'azione dettagli richiama il metodo FindById di un repository con il valore corretto, è probabile che l'azione si comportano correttamente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-499">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="59dc9-500">È possibile verificare questo comportamento senza configurare i dati di test da restituire da FindById.</span><span class="sxs-lookup"><span data-stu-id="59dc9-500">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="59dc9-501">L'unica configurazione richiesta nella fixture di test precedente è la configurazione fornita dalla classe base.</span><span class="sxs-lookup"><span data-stu-id="59dc9-501">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="59dc9-502">Quando si richiama l'azione del controller, MOQ registrerà le interazioni con il repository fittizio.</span><span class="sxs-lookup"><span data-stu-id="59dc9-502">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="59dc9-503">Usando l'API verify di MOQ, è possibile richiedere MOQ se il controller ha richiamato FindById con il valore ID appropriato.</span><span class="sxs-lookup"><span data-stu-id="59dc9-503">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="59dc9-504">Se il controller non ha richiamato il metodo o ha richiamato il metodo con un valore di parametro imprevisto, il metodo Verify genererà un'eccezione e il test avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="59dc9-504">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="59dc9-505">Di seguito è riportato un altro esempio per verificare che l'azione di creazione richiami commit sull'unità di lavoro corrente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-505">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="59dc9-506">Un pericolo per i test di interazione è la tendenza a sovraspecificare le interazioni.</span><span class="sxs-lookup"><span data-stu-id="59dc9-506">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="59dc9-507">La capacità dell'oggetto fittizio di registrare e verificare ogni interazione con l'oggetto fittizio non significa che il test deve provare a verificare ogni interazione.</span><span class="sxs-lookup"><span data-stu-id="59dc9-507">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="59dc9-508">Alcune interazioni sono i dettagli di implementazione ed è necessario verificare solo le interazioni *necessarie* per soddisfare il test corrente.</span><span class="sxs-lookup"><span data-stu-id="59dc9-508">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="59dc9-509">La scelta tra simulazioni o fake dipende in gran parte dal sistema che si sta testando e dalle preferenze personali (o del team).</span><span class="sxs-lookup"><span data-stu-id="59dc9-509">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="59dc9-510">Gli oggetti fittizi possono ridurre drasticamente la quantità di codice necessario per implementare i doppi test, ma non tutti sono le aspettative di programmazione e la verifica delle interazioni.</span><span class="sxs-lookup"><span data-stu-id="59dc9-510">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="59dc9-511">Conclusioni</span><span class="sxs-lookup"><span data-stu-id="59dc9-511">Conclusions</span></span>

<span data-ttu-id="59dc9-512">In questo articolo sono stati illustrati diversi approcci alla creazione di codice testabile quando si usa il Entity Framework ADO.NET per la persistenza dei dati.</span><span class="sxs-lookup"><span data-stu-id="59dc9-512">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="59dc9-513">Possiamo sfruttare le astrazioni compilate come IObjectSet &lt; t &gt; o creare astrazioni personalizzate come IRepository &lt; t &gt; .</span><span class="sxs-lookup"><span data-stu-id="59dc9-513">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="59dc9-514">In entrambi i casi, il supporto POCO in ADO.NET Entity Framework 4,0 consente agli utenti di queste astrazioni di rimanere ignoranti ed estremamente testabili.</span><span class="sxs-lookup"><span data-stu-id="59dc9-514">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="59dc9-515">Funzionalità aggiuntive di EF4 come il caricamento lazy implicito consentono al codice del servizio aziendale e dell'applicazione di funzionare senza doversi preoccupare dei dettagli di un archivio dati relazionale.</span><span class="sxs-lookup"><span data-stu-id="59dc9-515">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="59dc9-516">Infine, le astrazioni create sono facili da simulare o falsificare all'interno degli unit test e possono essere usate per ottenere test veloci, altamente isolati e affidabili.</span><span class="sxs-lookup"><span data-stu-id="59dc9-516">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="59dc9-517">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="59dc9-517">Additional Resources</span></span>

-   <span data-ttu-id="59dc9-518">Robert C. Martin, [il principio di singola responsabilità](https://www.objectmentor.com/resources/articles/srp.pdf)</span><span class="sxs-lookup"><span data-stu-id="59dc9-518">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="59dc9-519">Martin Fowler, [Catalogo dei modelli](https://www.martinfowler.com/eaaCatalog/index.html) di *modelli di architettura delle applicazioni aziendali*</span><span class="sxs-lookup"><span data-stu-id="59dc9-519">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="59dc9-520">Griffin Caprio, " [inserimento delle dipendenze](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-520">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="59dc9-521">Blog di programmabilità dei dati, " [procedura dettagliata: sviluppo basato su test con la Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".</span><span class="sxs-lookup"><span data-stu-id="59dc9-521">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="59dc9-522">Blog di programmabilità dei dati, " [uso di repository e modelli di unità di lavoro con Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-522">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="59dc9-523">Aaron Jensen, " [Introduzione alle specifiche del computer](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-523">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="59dc9-524">Eric Lee, " [BDD con MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-524">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="59dc9-525">Eric Evans, " [progettazione basata su dominio](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-525">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="59dc9-526">Martin Fowler, " [Mocks is Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-526">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="59dc9-527">Martin Fowler, " [test doppio](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="59dc9-527">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="59dc9-528">MOQ</span><span class="sxs-lookup"><span data-stu-id="59dc9-528">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="59dc9-529">Biografia</span><span class="sxs-lookup"><span data-stu-id="59dc9-529">Biography</span></span>

<span data-ttu-id="59dc9-530">Scott Allen è membro del personale tecnico di Pluralsight e fondatore di OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="59dc9-530">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="59dc9-531">In 15 anni di sviluppo di software commerciale, Scott ha lavorato alle soluzioni per tutto ciò che dai dispositivi embedded a 8 bit alle applicazioni Web ASP.NET altamente scalabili.</span><span class="sxs-lookup"><span data-stu-id="59dc9-531">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="59dc9-532">È possibile raggiungere Scott sul suo Blog in OdeToCode o su Twitter all'indirizzo [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .</span><span class="sxs-lookup"><span data-stu-id="59dc9-532">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
