---
title: Stringhe di connessione e modelli-EF6
description: Stringhe di connessione e modelli in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 294bb138-978f-4fe2-8491-fdf3cd3c60c4
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: 2203d7f2168dc9d4ae5a6b1914742c7c2b6fbf77
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618439"
---
# <a name="connection-strings-and-models"></a><span data-ttu-id="0fff1-103">Stringhe di connessione e modelli</span><span class="sxs-lookup"><span data-stu-id="0fff1-103">Connection strings and models</span></span>
<span data-ttu-id="0fff1-104">Questo argomento descrive il modo in cui Entity Framework individua la connessione al database da usare e come è possibile modificarla.</span><span class="sxs-lookup"><span data-stu-id="0fff1-104">This topic covers how Entity Framework discovers which database connection to use, and how you can change it.</span></span> <span data-ttu-id="0fff1-105">I modelli creati con Code First e la finestra di progettazione EF sono trattati in questo argomento.</span><span class="sxs-lookup"><span data-stu-id="0fff1-105">Models created with Code First and the EF Designer are both covered in this topic.</span></span>  

<span data-ttu-id="0fff1-106">In genere, un'applicazione Entity Framework usa una classe derivata da DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-106">Typically an Entity Framework application uses a class derived from DbContext.</span></span> <span data-ttu-id="0fff1-107">Questa classe derivata chiamerà uno dei costruttori nella classe DbContext di base per controllare:</span><span class="sxs-lookup"><span data-stu-id="0fff1-107">This derived class will call one of the constructors on the base DbContext class to control:</span></span>  

- <span data-ttu-id="0fff1-108">Il modo in cui il contesto si connetterà a un database, ovvero il modo in cui viene trovata o utilizzata una stringa di connessione</span><span class="sxs-lookup"><span data-stu-id="0fff1-108">How the context will connect to a database — that is, how a connection string is found/used</span></span>  
- <span data-ttu-id="0fff1-109">Indica se il contesto utilizzerà il calcolo di un modello usando Code First o caricherà un modello creato con la finestra di progettazione EF</span><span class="sxs-lookup"><span data-stu-id="0fff1-109">Whether the context will use calculate a model using Code First or load a model created with the EF Designer</span></span>  
- <span data-ttu-id="0fff1-110">Opzioni avanzate aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0fff1-110">Additional advanced options</span></span>  

<span data-ttu-id="0fff1-111">Nei frammenti seguenti vengono illustrati alcuni dei modi in cui è possibile utilizzare i costruttori DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-111">The following fragments show some of the ways the DbContext constructors can be used.</span></span>  

## <a name="use-code-first-with-connection-by-convention"></a><span data-ttu-id="0fff1-112">USA Code First con connessione per convenzione</span><span class="sxs-lookup"><span data-stu-id="0fff1-112">Use Code First with connection by convention</span></span>  

<span data-ttu-id="0fff1-113">Se nell'applicazione non è stata eseguita alcuna altra configurazione, la chiamata al costruttore senza parametri in DbContext provocherà l'esecuzione di DbContext in modalità Code First con una connessione di database creata per convenzione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-113">If you have not done any other configuration in your application, then calling the parameterless constructor on DbContext will cause DbContext to run in Code First mode with a database connection created by convention.</span></span> <span data-ttu-id="0fff1-114">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-114">For example:</span></span>  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

<span data-ttu-id="0fff1-115">In questo esempio DbContext usa il nome completo dello spazio dei nomi della classe del contesto derivato, demo. EF. BloggingContext, come nome del database e crea una stringa di connessione per il database usando SQL Express o local DB.</span><span class="sxs-lookup"><span data-stu-id="0fff1-115">In this example DbContext uses the namespace qualified name of your derived context class—Demo.EF.BloggingContext—as the database name and creates a connection string for this database using either SQL Express or LocalDB.</span></span> <span data-ttu-id="0fff1-116">Se entrambi sono installati, verrà utilizzato SQL Express.</span><span class="sxs-lookup"><span data-stu-id="0fff1-116">If both are installed, SQL Express will be used.</span></span>  

<span data-ttu-id="0fff1-117">Visual Studio 2010 include SQL Express per impostazione predefinita e Visual Studio 2012 e versioni successive includono il database locale.</span><span class="sxs-lookup"><span data-stu-id="0fff1-117">Visual Studio 2010 includes SQL Express by default and Visual Studio 2012 and later includes LocalDB.</span></span> <span data-ttu-id="0fff1-118">Durante l'installazione, il pacchetto NuGet EntityFramework controlla il server di database disponibile.</span><span class="sxs-lookup"><span data-stu-id="0fff1-118">During installation, the EntityFramework NuGet package checks which database server is available.</span></span> <span data-ttu-id="0fff1-119">Il pacchetto NuGet aggiornerà quindi il file di configurazione impostando il server di database predefinito usato da Code First per la creazione di una connessione per convenzione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-119">The NuGet package will then update the configuration file by setting the default database server that Code First uses when creating a connection by convention.</span></span> <span data-ttu-id="0fff1-120">Se SQL Express è in esecuzione, verrà usato.</span><span class="sxs-lookup"><span data-stu-id="0fff1-120">If SQL Express is running, it will be used.</span></span> <span data-ttu-id="0fff1-121">Se SQL Express non è disponibile, il database locale verrà registrato come predefinito.</span><span class="sxs-lookup"><span data-stu-id="0fff1-121">If SQL Express is not available then LocalDB will be registered as the default instead.</span></span> <span data-ttu-id="0fff1-122">Non viene apportata alcuna modifica al file di configurazione se è già presente un'impostazione per la factory di connessione predefinita.</span><span class="sxs-lookup"><span data-stu-id="0fff1-122">No changes are made to the configuration file if it already contains a setting for the default connection factory.</span></span>  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a><span data-ttu-id="0fff1-123">USA Code First con connessione per convenzione e nome database specificato</span><span class="sxs-lookup"><span data-stu-id="0fff1-123">Use Code First with connection by convention and specified database name</span></span>  

<span data-ttu-id="0fff1-124">Se nell'applicazione non è stata eseguita alcuna altra configurazione, chiamare il costruttore di stringa in DbContext con il nome del database che si desidera utilizzare provocherà l'esecuzione di DbContext in modalità Code First con una connessione di database creata per convenzione al database con tale nome.</span><span class="sxs-lookup"><span data-stu-id="0fff1-124">If you have not done any other configuration in your application, then calling the string constructor on DbContext with the database name you want to use will cause DbContext to run in Code First mode with a database connection created by convention to the database of that name.</span></span> <span data-ttu-id="0fff1-125">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-125">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

<span data-ttu-id="0fff1-126">In questo esempio DbContext utilizza "BloggingDatabase" come nome del database e crea una stringa di connessione per il database utilizzando SQL Express (installato con Visual Studio 2010) o il database locale (installato con Visual Studio 2012).</span><span class="sxs-lookup"><span data-stu-id="0fff1-126">In this example DbContext uses “BloggingDatabase” as the database name and creates a connection string for this database using either SQL Express (installed with Visual Studio 2010) or LocalDB (installed with Visual Studio 2012).</span></span> <span data-ttu-id="0fff1-127">Se entrambi sono installati, verrà utilizzato SQL Express.</span><span class="sxs-lookup"><span data-stu-id="0fff1-127">If both are installed, SQL Express will be used.</span></span>  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="0fff1-128">Usare Code First con la stringa di connessione nel file app.config/web.config</span><span class="sxs-lookup"><span data-stu-id="0fff1-128">Use Code First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="0fff1-129">È possibile scegliere di inserire una stringa di connessione nel file di app.config o web.config.</span><span class="sxs-lookup"><span data-stu-id="0fff1-129">You may choose to put a connection string in your app.config or web.config file.</span></span> <span data-ttu-id="0fff1-130">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-130">For example:</span></span>  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

<span data-ttu-id="0fff1-131">Si tratta di un modo semplice per indicare a DbContext di utilizzare un server di database diverso da SQL Express o local DB, nell'esempio precedente viene specificato un database SQL Server Compact Edition.</span><span class="sxs-lookup"><span data-stu-id="0fff1-131">This is an easy way to tell DbContext to use a database server other than SQL Express or LocalDB — the example above specifies a SQL Server Compact Edition database.</span></span>  

<span data-ttu-id="0fff1-132">Se il nome della stringa di connessione corrisponde al nome del contesto (con o senza qualificazione dello spazio dei nomi), sarà trovato da DbContext quando viene usato il costruttore senza parametri.</span><span class="sxs-lookup"><span data-stu-id="0fff1-132">If the name of the connection string matches the name of your context (either with or without namespace qualification) then it will be found by DbContext when the parameterless constructor is used.</span></span> <span data-ttu-id="0fff1-133">Se il nome della stringa di connessione è diverso dal nome del contesto, è possibile indicare a DbContext di usare questa connessione in modalità Code First passando il nome della stringa di connessione al costruttore DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-133">If the connection string name is different from the name of your context then you can tell DbContext to use this connection in Code First mode by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="0fff1-134">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-134">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="0fff1-135">In alternativa, è possibile usare il formato "Name = \<connection string name\> " per la stringa passata al costruttore DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-135">Alternatively, you can use the form “name=\<connection string name\>” for the string passed to the DbContext constructor.</span></span> <span data-ttu-id="0fff1-136">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-136">For example:</span></span>  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

<span data-ttu-id="0fff1-137">Questo modulo rende esplicito che si prevede che la stringa di connessione venga trovata nel file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-137">This form makes it explicit that you expect the connection string to be found in your config file.</span></span> <span data-ttu-id="0fff1-138">Se non viene trovata una stringa di connessione con il nome specificato, verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-138">An exception will be thrown if a connection string with the given name is not found.</span></span>  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a><span data-ttu-id="0fff1-139">Database/Model First con stringa di connessione nel file app.config/web.config</span><span class="sxs-lookup"><span data-stu-id="0fff1-139">Database/Model First with connection string in app.config/web.config file</span></span>  

<span data-ttu-id="0fff1-140">I modelli creati con la finestra di progettazione EF sono diversi da Code First in quanto il modello esiste già e non viene generato dal codice durante l'esecuzione dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-140">Models created with the EF Designer are different from Code First in that your model already exists and is not generated from code when the application runs.</span></span> <span data-ttu-id="0fff1-141">Il modello è in genere presente come file EDMX nel progetto.</span><span class="sxs-lookup"><span data-stu-id="0fff1-141">The model typically exists as an EDMX file in your project.</span></span>  

<span data-ttu-id="0fff1-142">Nella finestra di progettazione verrà aggiunta una stringa di connessione EF al file di app.config o web.config.</span><span class="sxs-lookup"><span data-stu-id="0fff1-142">The designer will add an EF connection string to your app.config or web.config file.</span></span> <span data-ttu-id="0fff1-143">Questa stringa di connessione è speciale in quanto contiene informazioni su come trovare le informazioni nel file EDMX.</span><span class="sxs-lookup"><span data-stu-id="0fff1-143">This connection string is special in that it contains information about how to find the information in your EDMX file.</span></span> <span data-ttu-id="0fff1-144">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-144">For example:</span></span>  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

<span data-ttu-id="0fff1-145">EF Designer genera anche codice che indica a DbContext di usare questa connessione passando il nome della stringa di connessione al costruttore DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-145">The EF Designer will also generate code that tells DbContext to use this connection by passing the connection string name to the DbContext constructor.</span></span> <span data-ttu-id="0fff1-146">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="0fff1-146">For example:</span></span>  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

<span data-ttu-id="0fff1-147">DbContext sa di caricare il modello esistente (invece di usare Code First per calcolarlo dal codice) perché la stringa di connessione è una stringa di connessione EF che contiene i dettagli del modello da usare.</span><span class="sxs-lookup"><span data-stu-id="0fff1-147">DbContext knows to load the existing model (rather than using Code First to calculate it from code) because the connection string is an EF connection string containing details of the model to use.</span></span>  

## <a name="other-dbcontext-constructor-options"></a><span data-ttu-id="0fff1-148">Altre opzioni del costruttore DbContext</span><span class="sxs-lookup"><span data-stu-id="0fff1-148">Other DbContext constructor options</span></span>  

<span data-ttu-id="0fff1-149">La classe DbContext contiene altri costruttori e modelli di utilizzo che consentono scenari più avanzati.</span><span class="sxs-lookup"><span data-stu-id="0fff1-149">The DbContext class contains other constructors and usage patterns that enable some more advanced scenarios.</span></span> <span data-ttu-id="0fff1-150">Di seguito sono riportate alcune di queste:</span><span class="sxs-lookup"><span data-stu-id="0fff1-150">Some of these are:</span></span>  

- <span data-ttu-id="0fff1-151">È possibile usare la classe DbModelBuilder per compilare un modello di Code First senza creare un'istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-151">You can use the DbModelBuilder class to build a Code First model without instantiating a DbContext instance.</span></span> <span data-ttu-id="0fff1-152">Il risultato è un oggetto DbModel.</span><span class="sxs-lookup"><span data-stu-id="0fff1-152">The result of this is a DbModel object.</span></span> <span data-ttu-id="0fff1-153">È quindi possibile passare questo oggetto DbModel a uno dei costruttori DbContext quando si è pronti per creare l'istanza di DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-153">You can then pass this DbModel object to one of the DbContext constructors when you are ready to create your DbContext instance.</span></span>  
- <span data-ttu-id="0fff1-154">È possibile passare una stringa di connessione completa a DbContext anziché solo il nome del database o della stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-154">You can pass a full connection string to DbContext instead of just the database or connection string name.</span></span> <span data-ttu-id="0fff1-155">Per impostazione predefinita, questa stringa di connessione viene utilizzata con il provider System. Data. SqlClient. Questo può essere modificato impostando un'implementazione diversa di IConnectionFactory nel contesto. Database. DefaultConnectionFactory.</span><span class="sxs-lookup"><span data-stu-id="0fff1-155">By default this connection string is used with the System.Data.SqlClient provider; this can be changed by setting a different implementation of IConnectionFactory onto context.Database.DefaultConnectionFactory.</span></span>  
- <span data-ttu-id="0fff1-156">È possibile usare un oggetto DbConnection esistente passandolo a un costruttore DbContext.</span><span class="sxs-lookup"><span data-stu-id="0fff1-156">You can use an existing DbConnection object by passing it to a DbContext constructor.</span></span> <span data-ttu-id="0fff1-157">Se l'oggetto Connection è un'istanza di EntityConnection, verrà utilizzato il modello specificato nella connessione anziché calcolare un modello utilizzando Code First.</span><span class="sxs-lookup"><span data-stu-id="0fff1-157">If the connection object is an instance of EntityConnection, then the model specified in the connection will be used rather than calculating a model using Code First.</span></span> <span data-ttu-id="0fff1-158">Se l'oggetto è un'istanza di un altro tipo, ad esempio SqlConnection, il contesto lo utilizzerà per la modalità Code First.</span><span class="sxs-lookup"><span data-stu-id="0fff1-158">If the object is an instance of some other type—for example, SqlConnection—then the context will use it for Code First mode.</span></span>  
- <span data-ttu-id="0fff1-159">È possibile passare un oggetto ObjectContext esistente a un costruttore DbContext per creare un DbContext che esegue il wrapping del contesto esistente.</span><span class="sxs-lookup"><span data-stu-id="0fff1-159">You can pass an existing ObjectContext to a DbContext constructor to create a DbContext wrapping the existing context.</span></span> <span data-ttu-id="0fff1-160">Questo può essere usato per le applicazioni esistenti che usano ObjectContext, ma che vogliono sfruttare i vantaggi di DbContext in alcune parti dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="0fff1-160">This can be used for existing applications that use ObjectContext but which want to take advantage of DbContext in some parts of the application.</span></span>  
