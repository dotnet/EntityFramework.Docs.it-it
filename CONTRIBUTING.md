# <a name="contributing-to-the-entity-framework-documentation"></a><span data-ttu-id="21165-101">Contributo alla documentazione di Entity Framework</span><span class="sxs-lookup"><span data-stu-id="21165-101">Contributing to the Entity Framework documentation</span></span>

<span data-ttu-id="21165-102">Il processo per contribuire alla documentazione di Entity Framework con articoli ed esempi di codice è spiegato di seguito.</span><span class="sxs-lookup"><span data-stu-id="21165-102">The process of contributing articles and code samples to the Entity Framework documentation is explained below.</span></span> <span data-ttu-id="21165-103">I contributi possono essere semplici come le correzioni di errori di ortografia o complessi, ad esempio nuovi articoli.</span><span class="sxs-lookup"><span data-stu-id="21165-103">Contributions can be as simple as typo corrections or as complex as new articles.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="21165-104">Come effettuare una semplice correzione o suggerire una modifica</span><span class="sxs-lookup"><span data-stu-id="21165-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="21165-105">Gli articoli sono archiviati come file markdown in questo repository.</span><span class="sxs-lookup"><span data-stu-id="21165-105">Articles are stored as Markdown files in this repository.</span></span> <span data-ttu-id="21165-106">Per apportare modifiche semplici al contenuto di un file markdown, fare clic sul collegamento **Edit** (Modifica) nell'angolo superiore destro della finestra del browser.</span><span class="sxs-lookup"><span data-stu-id="21165-106">To make a simple change to the content of a Markdown file, click the **Edit** link in the upper right corner of the browser window.</span></span> <span data-ttu-id="21165-107">Potrebbe essere necessario espandere la barra delle **opzioni**per visualizzare il collegamento **Edit** (Modifica).</span><span class="sxs-lookup"><span data-stu-id="21165-107">You might need to expand the **options** bar to see the **Edit** link.</span></span> <span data-ttu-id="21165-108">Seguire le istruzioni per creare una richiesta pull.</span><span class="sxs-lookup"><span data-stu-id="21165-108">Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="21165-109">La richiesta pull verrà esaminata dal team EF e accettata oppure verranno suggerite modifiche.</span><span class="sxs-lookup"><span data-stu-id="21165-109">The EF team will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="21165-110">Come effettuare un invio più complesso</span><span class="sxs-lookup"><span data-stu-id="21165-110">How to make a more complex submission</span></span>

<span data-ttu-id="21165-111">Sono necessarie delle conoscenze di base di [Git e GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="21165-111">You'll need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="21165-112">Aprire un [problema](https://github.com/dotnet/EntityFramework.Docs/issues/new) che descriva ciò che si vuole fare, ad esempio modificare un articolo esistente o crearne uno nuovo.</span><span class="sxs-lookup"><span data-stu-id="21165-112">Open an [issue](https://github.com/dotnet/EntityFramework.Docs/issues/new) describing what you want to do, such as change an existing article or create a new one.</span></span> <span data-ttu-id="21165-113">Attendere l'approvazione dal team EF prima di investire molto tempo.</span><span class="sxs-lookup"><span data-stu-id="21165-113">Wait for approval from the EF team before you invest much time.</span></span>
* <span data-ttu-id="21165-114">Creare un fork del repository [DotNet/EntityFramework.Doc](https://github.com/dotnet/EntityFramework.Docs/) e creare un ramo per le modifiche.</span><span class="sxs-lookup"><span data-stu-id="21165-114">Fork the [dotnet/EntityFramework.Docs](https://github.com/dotnet/EntityFramework.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="21165-115">Inviare una richiesta pull (PR) al master con le modifiche.</span><span class="sxs-lookup"><span data-stu-id="21165-115">Submit a pull request (PR) to master with your changes.</span></span>
* <span data-ttu-id="21165-116">Rispondere ai commenti e suggerimenti per la richiesta pull.</span><span class="sxs-lookup"><span data-stu-id="21165-116">Respond to PR feedback.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="21165-117">Sintassi di Markdown</span><span class="sxs-lookup"><span data-stu-id="21165-117">Markdown syntax</span></span>

<span data-ttu-id="21165-118">Gli articoli sono scritti nel linguaggio [DocFx Flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), che è un superset di [GitHub Flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="21165-118">Articles are written in [DocFx-flavored Markdown (DFM)](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="21165-119">Per esempi della sintassi e dei metadati DFM per le funzionalità dell'interfaccia utente comunemente usate nella documentazione di EF, vedere il [modello di metadati e Markdown](https://github.com/dotnet/docs/blob/master/styleguide/template.md) nella guida di stile del repository .NET Core.</span><span class="sxs-lookup"><span data-stu-id="21165-119">For examples of DFM syntax and metadata for UI features commonly used in the EF documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Core repo style guide.</span></span>

## <a name="folder-structure-conventions"></a><span data-ttu-id="21165-120">Convenzioni per la struttura di cartelle</span><span class="sxs-lookup"><span data-stu-id="21165-120">Folder structure conventions</span></span>

<span data-ttu-id="21165-121">Le immagini e altri contenuti statici, vengono archiviati in una cartella `_static` all'interno di ogni cartella area/del sito.</span><span class="sxs-lookup"><span data-stu-id="21165-121">Images and other static content are stored in an `_static` folder within each area/folder of the site.</span></span>

<span data-ttu-id="21165-122">Gli esempi di codice vengono archiviati nella cartella radice `samples`.</span><span class="sxs-lookup"><span data-stu-id="21165-122">Code samples are stored in the `samples` root folder.</span></span> <span data-ttu-id="21165-123">Sono organizzati in una struttura di cartelle che riproduce la struttura della documentazione (disponibile sotto la cartella radice `entity-framework`).</span><span class="sxs-lookup"><span data-stu-id="21165-123">They are organized into a folder structure that mimics the documentation structure (found under the `entity-framework` root folder).</span></span>

## <a name="code-snippets"></a><span data-ttu-id="21165-124">Frammenti di codice</span><span class="sxs-lookup"><span data-stu-id="21165-124">Code snippets</span></span>

<span data-ttu-id="21165-125">Gli articoli contengono spesso frammenti di codice per illustrare gli argomenti discussi.</span><span class="sxs-lookup"><span data-stu-id="21165-125">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="21165-126">DFM consente di copiare codice nel file markdown o di fare riferimento a un file di codice separato.</span><span class="sxs-lookup"><span data-stu-id="21165-126">DFM lets you copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="21165-127">Quando possibile, usare file di codice separati per ridurre al minimo la probabilità di errori nel codice.</span><span class="sxs-lookup"><span data-stu-id="21165-127">Whenever possible, use separate code files to minimize the chance of errors in the code.</span></span> <span data-ttu-id="21165-128">I file di codice vengono archiviati nel repository usando la struttura di cartelle descritta in precedenza per i progetti di esempio.</span><span class="sxs-lookup"><span data-stu-id="21165-128">The code files should be stored in the repo using the folder structure described above for sample projects.</span></span>

<span data-ttu-id="21165-129">Di seguito sono riportati alcuni esempi di [sintassi di frammenti di codice DFM](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span><span class="sxs-lookup"><span data-stu-id="21165-129">Here are some examples of [DFM code snippet syntax](http://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet).</span></span>

<span data-ttu-id="21165-130">Per eseguire il rendering di un intero file di codice come un frammento:</span><span class="sxs-lookup"><span data-stu-id="21165-130">To render an entire code file as a snippet:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs)]
```

<span data-ttu-id="21165-131">Per eseguire il rendering di una parte di un file come un frammento usando i numeri di riga:</span><span class="sxs-lookup"><span data-stu-id="21165-131">To render a portion of a file as a snippet by using line numbers:</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?range=1-10]
```

<span data-ttu-id="21165-132">Per i frammenti di codice C#, è possibile fare riferimento a un'[area C#](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span><span class="sxs-lookup"><span data-stu-id="21165-132">For C# snippets, you can reference a [C# region](https://msdn.microsoft.com/library/9a1ybwek.aspx).</span></span> <span data-ttu-id="21165-133">Usare le aree invece dei numeri di riga.</span><span class="sxs-lookup"><span data-stu-id="21165-133">Use regions rather than line numbers.</span></span> <span data-ttu-id="21165-134">I numeri di riga in un file di codice tendono a cambiare e a perdere la sincronizzazione con i riferimenti ai numeri di riga in Markdown.</span><span class="sxs-lookup"><span data-stu-id="21165-134">Line numbers in a code file tend to change and get out of sync with line number references in Markdown.</span></span> <span data-ttu-id="21165-135">Le aree C# possono essere annidate.</span><span class="sxs-lookup"><span data-stu-id="21165-135">C# regions can be nested.</span></span> <span data-ttu-id="21165-136">Se si fa riferimento all'area esterna, non viene eseguito il rendering delle direttive interne `#region` e `#endregion` in un frammento di codice.</span><span class="sxs-lookup"><span data-stu-id="21165-136">If you reference the outer region, the inner `#region` and `#endregion` directives are not rendered in a snippet.</span></span>

<span data-ttu-id="21165-137">Per eseguire il rendering di un'area C# denominata "snippet_Example":</span><span class="sxs-lookup"><span data-stu-id="21165-137">To render a C# region named "snippet_Example":</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="21165-138">Per evidenziare le righe selezionate in un frammento di codice sottoposto a rendering (in genere il rendering viene eseguito con un colore di sfondo giallo):</span><span class="sxs-lookup"><span data-stu-id="21165-138">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```none
[!code-csharp[Main](../../../samples/core/saving/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
```

## <a name="test-your-changes-with-docfx"></a><span data-ttu-id="21165-139">Eseguire il test delle modifiche con DocFX</span><span class="sxs-lookup"><span data-stu-id="21165-139">Test your changes with DocFX</span></span>

<span data-ttu-id="21165-140">Testare le modifiche con lo [strumento da riga di comando DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), che consente di creare una versione ospitata in locale del sito.</span><span class="sxs-lookup"><span data-stu-id="21165-140">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="21165-141">DocFX non esegue il rendering delle estensioni di stile e del sito create per docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="21165-141">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="21165-142">DocFX richiede .NET Framework in Windows o Mono (per Linux o macOS).</span><span class="sxs-lookup"><span data-stu-id="21165-142">DocFX requires the .NET Framework on Windows, or Mono for Linux or macOS.</span></span>

### <a name="windows-instructions"></a><span data-ttu-id="21165-143">Istruzioni per Windows</span><span class="sxs-lookup"><span data-stu-id="21165-143">Windows instructions</span></span>

* <span data-ttu-id="21165-144">Scaricare e decomprimere *docfx.zip* da [DocFX releases](https://github.com/dotnet/docfx/releases) (Versioni di DocFX).</span><span class="sxs-lookup"><span data-stu-id="21165-144">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="21165-145">Aggiungere DocFX a PATH.</span><span class="sxs-lookup"><span data-stu-id="21165-145">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="21165-146">In una finestra della riga di comando, passare al repository clonato (che contiene il file *docfx.json*) ed eseguire il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="21165-146">In a command-line window, navigate to the cloned repository (which contains the *docfx.json* file) and run the following command:</span></span>

   ```console
   docfx -t default --serve
   ```

* <span data-ttu-id="21165-147">In un browser passare a `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="21165-147">In a browser, navigate to `http://localhost:8080`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="21165-148">Istruzioni per Mono</span><span class="sxs-lookup"><span data-stu-id="21165-148">Mono instructions</span></span>

* <span data-ttu-id="21165-149">Installare Mono tramite Homebrew: `brew install mono`.</span><span class="sxs-lookup"><span data-stu-id="21165-149">Install Mono via Homebrew - `brew install mono`.</span></span>
* <span data-ttu-id="21165-150">Scarica la [versione più recente di DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span><span class="sxs-lookup"><span data-stu-id="21165-150">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases/tag/v2.7.2).</span></span>
* <span data-ttu-id="21165-151">Estrarre in `\bin\docfx`.</span><span class="sxs-lookup"><span data-stu-id="21165-151">Extract to `\bin\docfx`.</span></span>
* <span data-ttu-id="21165-152">Creare un alias per **docfx**:</span><span class="sxs-lookup"><span data-stu-id="21165-152">Create an alias for **docfx**:</span></span>

  ```console
  function docfx {
    mono $HOME/bin/docfx/docfx.exe
  }

  function docfx-serve {
    mono $HOME/bin/docfx/docfx.exe serve _site
  }
  ```

* <span data-ttu-id="21165-153">Eseguire **docfx** nel repository clonato per compilare il sito e **docfx-serve** per visualizzare il sito all'indirizzo `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="21165-153">Run **docfx** in the cloned repository to build the site, and **docfx-serve** to view the site at `http://localhost:8080`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="21165-154">Voce e tono</span><span class="sxs-lookup"><span data-stu-id="21165-154">Voice and tone</span></span>

<span data-ttu-id="21165-155">L'obiettivo è quello di scrivere documentazione facilmente comprensibile per il più ampio pubblico possibile.</span><span class="sxs-lookup"><span data-stu-id="21165-155">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="21165-156">A tale scopo, sono state definite linee guida per lo stile di scrittura che i collaboratori dovranno seguire.</span><span class="sxs-lookup"><span data-stu-id="21165-156">To that end we have established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="21165-157">Per altre informazioni, vedere [Linee guida per la voce e il tono](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) nel repository .NET Core.</span><span class="sxs-lookup"><span data-stu-id="21165-157">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET Core repo.</span></span>
