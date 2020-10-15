---
title: Stringhe di connessione e modelli-EF6
description: Stringhe di connessione e modelli in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: c60acb965b7062f3cd35dab94ee8dfe48394969a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063387"
---
# <a name="connection-strings-and-models"></a>Stringhe di connessione e modelli
Questo argomento descrive il modo in cui Entity Framework individua la connessione al database da usare e come è possibile modificarla. I modelli creati con Code First e la finestra di progettazione EF sono trattati in questo argomento.  

In genere, un'applicazione Entity Framework usa una classe derivata da DbContext. Questa classe derivata chiamerà uno dei costruttori nella classe DbContext di base per controllare:  

- Il modo in cui il contesto si connetterà a un database, ovvero il modo in cui viene trovata o utilizzata una stringa di connessione  
- Indica se il contesto utilizzerà il calcolo di un modello usando Code First o caricherà un modello creato con la finestra di progettazione EF  
- Opzioni avanzate aggiuntive  

Nei frammenti seguenti vengono illustrati alcuni dei modi in cui è possibile utilizzare i costruttori DbContext.  

## <a name="use-code-first-with-connection-by-convention"></a>USA Code First con connessione per convenzione  

Se nell'applicazione non è stata eseguita alcuna altra configurazione, la chiamata al costruttore senza parametri in DbContext provocherà l'esecuzione di DbContext in modalità Code First con una connessione di database creata per convenzione. Ad esempio:  

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

In questo esempio DbContext usa il nome completo dello spazio dei nomi della classe del contesto derivato, demo. EF. BloggingContext, come nome del database e crea una stringa di connessione per il database usando SQL Express o local DB. Se entrambi sono installati, verrà utilizzato SQL Express.  

Visual Studio 2010 include SQL Express per impostazione predefinita e Visual Studio 2012 e versioni successive includono il database locale. Durante l'installazione, il pacchetto NuGet EntityFramework controlla il server di database disponibile. Il pacchetto NuGet aggiornerà quindi il file di configurazione impostando il server di database predefinito usato da Code First per la creazione di una connessione per convenzione. Se SQL Express è in esecuzione, verrà usato. Se SQL Express non è disponibile, il database locale verrà registrato come predefinito. Non viene apportata alcuna modifica al file di configurazione se è già presente un'impostazione per la factory di connessione predefinita.  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>USA Code First con connessione per convenzione e nome database specificato  

Se nell'applicazione non è stata eseguita alcuna altra configurazione, chiamare il costruttore di stringa in DbContext con il nome del database che si desidera utilizzare provocherà l'esecuzione di DbContext in modalità Code First con una connessione di database creata per convenzione al database con tale nome. Ad esempio:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

In questo esempio DbContext utilizza "BloggingDatabase" come nome del database e crea una stringa di connessione per il database utilizzando SQL Express (installato con Visual Studio 2010) o il database locale (installato con Visual Studio 2012). Se entrambi sono installati, verrà utilizzato SQL Express.  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>Usare Code First con la stringa di connessione nel file app.config/web.config  

È possibile scegliere di inserire una stringa di connessione nel file di app.config o web.config. Ad esempio:  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

Si tratta di un modo semplice per indicare a DbContext di utilizzare un server di database diverso da SQL Express o local DB, nell'esempio precedente viene specificato un database SQL Server Compact Edition.  

Se il nome della stringa di connessione corrisponde al nome del contesto (con o senza qualificazione dello spazio dei nomi), sarà trovato da DbContext quando viene usato il costruttore senza parametri. Se il nome della stringa di connessione è diverso dal nome del contesto, è possibile indicare a DbContext di usare questa connessione in modalità Code First passando il nome della stringa di connessione al costruttore DbContext. Ad esempio:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

In alternativa, è possibile usare il formato "Name = \<connection string name\> " per la stringa passata al costruttore DbContext. Ad esempio:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

Questo modulo rende esplicito che si prevede che la stringa di connessione venga trovata nel file di configurazione. Se non viene trovata una stringa di connessione con il nome specificato, verrà generata un'eccezione.  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>Database/Model First con stringa di connessione nel file app.config/web.config  

I modelli creati con la finestra di progettazione EF sono diversi da Code First in quanto il modello esiste già e non viene generato dal codice durante l'esecuzione dell'applicazione. Il modello è in genere presente come file EDMX nel progetto.  

Nella finestra di progettazione verrà aggiunta una stringa di connessione EF al file di app.config o web.config. Questa stringa di connessione è speciale in quanto contiene informazioni su come trovare le informazioni nel file EDMX. Ad esempio:  

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

EF Designer genera anche codice che indica a DbContext di usare questa connessione passando il nome della stringa di connessione al costruttore DbContext. Ad esempio:  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbContext sa di caricare il modello esistente (invece di usare Code First per calcolarlo dal codice) perché la stringa di connessione è una stringa di connessione EF che contiene i dettagli del modello da usare.  

## <a name="other-dbcontext-constructor-options"></a>Altre opzioni del costruttore DbContext  

La classe DbContext contiene altri costruttori e modelli di utilizzo che consentono scenari più avanzati. Di seguito sono riportate alcune di queste:  

- È possibile usare la classe DbModelBuilder per compilare un modello di Code First senza creare un'istanza di DbContext. Il risultato è un oggetto DbModel. È quindi possibile passare questo oggetto DbModel a uno dei costruttori DbContext quando si è pronti per creare l'istanza di DbContext.  
- È possibile passare una stringa di connessione completa a DbContext anziché solo il nome del database o della stringa di connessione. Per impostazione predefinita, questa stringa di connessione viene utilizzata con il provider System. Data. SqlClient. Questo può essere modificato impostando un'implementazione diversa di IConnectionFactory nel contesto. Database. DefaultConnectionFactory.  
- È possibile usare un oggetto DbConnection esistente passandolo a un costruttore DbContext. Se l'oggetto Connection è un'istanza di EntityConnection, verrà utilizzato il modello specificato nella connessione anziché calcolare un modello utilizzando Code First. Se l'oggetto è un'istanza di un altro tipo, ad esempio SqlConnection, il contesto lo utilizzerà per la modalità Code First.  
- È possibile passare un oggetto ObjectContext esistente a un costruttore DbContext per creare un DbContext che esegue il wrapping del contesto esistente. Questo può essere usato per le applicazioni esistenti che usano ObjectContext, ma che vogliono sfruttare i vantaggi di DbContext in alcune parti dell'applicazione.  
