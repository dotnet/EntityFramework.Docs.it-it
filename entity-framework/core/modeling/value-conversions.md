---
title: Conversioni di valori-EF Core
description: Configurazione di convertitori di valori in un modello di Entity Framework Core
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: 114a18e41804c85c97791047aa915c2ec38dea15
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023887"
---
# <a name="value-conversions"></a><span data-ttu-id="ae3cd-103">Conversioni di valori</span><span class="sxs-lookup"><span data-stu-id="ae3cd-103">Value Conversions</span></span>

<span data-ttu-id="ae3cd-104">I convertitori di valori consentono di convertire i valori delle proprietà durante la lettura o la scrittura nel database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="ae3cd-105">Questa conversione può provenire da un valore a un altro dello stesso tipo (ad esempio, la crittografia di stringhe) o da un valore di un tipo a un valore di un altro tipo, ad esempio la conversione di valori enum da e verso stringhe nel database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

> [!TIP]
> <span data-ttu-id="ae3cd-106">È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="ae3cd-106">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="overview"></a><span data-ttu-id="ae3cd-107">Panoramica</span><span class="sxs-lookup"><span data-stu-id="ae3cd-107">Overview</span></span>

<span data-ttu-id="ae3cd-108">I convertitori di valori vengono specificati in termini di `ModelClrType` e `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="ae3cd-109">Il tipo di modello è il tipo .NET della proprietà nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="ae3cd-110">Il tipo di provider è il tipo .NET riconosciuto dal provider di database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="ae3cd-111">Ad esempio, per salvare le enumerazioni come stringhe nel database, il tipo di modello è il tipo dell'enumerazione e il tipo di provider è `String` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="ae3cd-112">Questi due tipi possono essere gli stessi.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-112">These two types can be the same.</span></span>

<span data-ttu-id="ae3cd-113">Le conversioni vengono definite utilizzando due `Func` alberi delle espressioni: uno da `ModelClrType` a `ProviderClrType` e l'altro da `ProviderClrType` a `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="ae3cd-114">Gli alberi delle espressioni vengono utilizzati in modo che possano essere compilati nel delegato di accesso al database per conversioni efficienti.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-114">Expression trees are used so that they can be compiled into the database access delegate for efficient conversions.</span></span> <span data-ttu-id="ae3cd-115">L'albero delle espressioni può contenere una semplice chiamata a un metodo di conversione per le conversioni complesse.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-115">The expression tree may contain a simple call to a conversion method for complex conversions.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3cd-116">Una proprietà configurata per la conversione di valori può anche dover specificare un oggetto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-116">A property that has been configured for value conversion may also need to specify a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="ae3cd-117">Per ulteriori informazioni, vedere gli esempi riportati di seguito e la documentazione sugli [operatori di confronto dei valori](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-117">See the examples below, and the [Value Comparers](xref:core/modeling/value-comparers) documentation for more information.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="ae3cd-118">Configurazione di un convertitore di valori</span><span class="sxs-lookup"><span data-stu-id="ae3cd-118">Configuring a value converter</span></span>

<span data-ttu-id="ae3cd-119">Le conversioni di valori sono configurate in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-119">Value conversions are configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="ae3cd-120">Si consideri, ad esempio, un'enumerazione e un tipo di entità definiti come segue:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-120">For example, consider an enum and entity type defined as:</span></span>

<!--
        public class Rider
        {
            public int Id { get; set; }
            public EquineBeast Mount { get; set; }
        }

        public enum EquineBeast
        {
            Donkey,
            Mule,
            Horse,
            Unicorn
        }
-->
[!code-csharp[BeastAndRider](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=BeastAndRider)]

<span data-ttu-id="ae3cd-121">Le conversioni possono essere configurate in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> per archiviare i valori enum come stringhe, ad esempio "Donkey", "Mule" e così via nel database. è sufficiente fornire una funzione che converte da `ModelClrType` a `ProviderClrType` e un'altra per la conversione opposta:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-121">Conversions can be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> to store the enum values as strings such as "Donkey", "Mule", etc. in the database; you simply need to provide one function which converts from the `ModelClrType` to the `ProviderClrType`, and another for the opposite conversion:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(
                        v => v.ToString(),
                        v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
            }
-->
[!code-csharp[ExplicitConversion](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ExplicitConversion)]

> [!NOTE]
> <span data-ttu-id="ae3cd-122">Un `null` valore non verrà mai passato a un convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-122">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="ae3cd-123">Un valore null in una colonna del database è sempre un valore null nell'istanza dell'entità e viceversa.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-123">A null in a database column is always a null in the entity instance, and vice-versa.</span></span> <span data-ttu-id="ae3cd-124">In questo modo l'implementazione delle conversioni risulta più semplice e consente di condividerle tra proprietà Nullable e non nullable.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-124">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span> <span data-ttu-id="ae3cd-125">Per ulteriori informazioni, vedere il [problema GitHub #13850](https://github.com/dotnet/efcore/issues/13850) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-125">See [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) for more information.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="ae3cd-126">Conversioni predefinite</span><span class="sxs-lookup"><span data-stu-id="ae3cd-126">Pre-defined conversions</span></span>

<span data-ttu-id="ae3cd-127">EF Core contiene molte conversioni predefinite che evitano la necessità di scrivere manualmente funzioni di conversione.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-127">EF Core contains many pre-defined conversions that avoid the need to write conversion functions manually.</span></span> <span data-ttu-id="ae3cd-128">Al contrario, EF Core selezionerà la conversione da utilizzare in base al tipo di proprietà nel modello e al tipo di provider di database richiesto.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-128">Instead, EF Core will pick the conversion to use based on the property type in the model and the requested database provider type.</span></span>

<span data-ttu-id="ae3cd-129">Ad esempio, l'enumerazione per le conversioni di stringa viene utilizzata come esempio precedente, ma EF Core eseguirà questa operazione automaticamente quando il tipo di provider viene configurato come `string` utilizzando il tipo generico di <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :</span><span class="sxs-lookup"><span data-stu-id="ae3cd-129">For example, enum to string conversions are used as an example above, but EF Core will actually do this automatically when the provider type is configured as `string` using the generic type of <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>();
            }
-->
[!code-csharp[ConversionByClrType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrType)]

<span data-ttu-id="ae3cd-130">La stessa operazione può essere eseguita specificando in modo esplicito il tipo di colonna del database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-130">The same thing can be achieved by explicitly specifying the database column type.</span></span> <span data-ttu-id="ae3cd-131">Ad esempio, se il tipo di entità è definito come segue:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-131">For example, if the entity type is defined like so:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="ae3cd-132">Annotazioni dei dati</span><span class="sxs-lookup"><span data-stu-id="ae3cd-132">Data Annotations</span></span>](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[<span data-ttu-id="ae3cd-133">API Fluent</span><span class="sxs-lookup"><span data-stu-id="ae3cd-133">Fluent API</span></span>](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

<span data-ttu-id="ae3cd-134">I valori enum verranno quindi salvati come stringhe nel database senza ulteriori operazioni di configurazione in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-134">Then the enum values will be saved as strings in the database without any further configuration in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="ae3cd-135">Classe ValueConverter</span><span class="sxs-lookup"><span data-stu-id="ae3cd-135">The ValueConverter class</span></span>

<span data-ttu-id="ae3cd-136">La chiamata a <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> come illustrato in precedenza creerà un' <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> istanza e la imposterà sulla proprietà.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-136">Calling <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> as shown above will create a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instance and set it on the property.</span></span> <span data-ttu-id="ae3cd-137">L'oggetto `ValueConverter` può invece essere creato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-137">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="ae3cd-138">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-138">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstance](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstance)]

<span data-ttu-id="ae3cd-139">Questa operazione può essere utile quando più proprietà utilizzano la stessa conversione.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-139">This can be useful when multiple properties use the same conversion.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="ae3cd-140">Convertitori predefiniti</span><span class="sxs-lookup"><span data-stu-id="ae3cd-140">Built-in converters</span></span>

<span data-ttu-id="ae3cd-141">Come indicato in precedenza, EF Core viene fornito con un set di <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classi predefinite, disponibile nello <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-141">As mentioned above, EF Core ships with a set of pre-defined <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classes, found in the <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> namespace.</span></span> <span data-ttu-id="ae3cd-142">In molti casi EF sceglierà il convertitore predefinito appropriato in base al tipo di proprietà nel modello e al tipo richiesto nel database, come illustrato in precedenza per le enumerazioni.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-142">In many cases EF will choose the appropriate built-in converter based on the type of the property in the model and the type requested in the database, as shown above for enums.</span></span> <span data-ttu-id="ae3cd-143">Se, ad esempio, `.HasConversion<int>()` si utilizza su una `bool` proprietà, EF Core convertirà i valori bool in numerici zero e un valore:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-143">For example, using `.HasConversion<int>()` on a `bool` property will cause EF Core to convert bool values to numerical zero and one values:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion<int>();
            }
-->
[!code-csharp[ConversionByBuiltInBoolToInt](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToInt)]

<span data-ttu-id="ae3cd-144">Questa operazione è analoga a quella della creazione di un'istanza di predefinita <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> e della relativa impostazione in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-144">This is functionally the same as creating an instance of the built-in <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> and setting it explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new BoolToZeroOneConverter<int>();

                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByBuiltInBoolToIntExplicit](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToIntExplicit)]

<span data-ttu-id="ae3cd-145">Nella tabella seguente sono riepilogate le conversioni predefinite comunemente utilizzate dai tipi di modello/proprietà ai tipi di provider di database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-145">The following table summarizes commonly-used pre-defined conversions from model/property types to database provider types.</span></span> <span data-ttu-id="ae3cd-146">Nella tabella `any_numeric_type` significa uno tra,,, `int` `short` `long` `byte` , `uint` , `ushort` , `ulong` , `sbyte` , `char` , `decimal` , `float` o `double` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-146">In the table `any_numeric_type` means one of `int`, `short`, `long`, `byte`, `uint`, `ushort`, `ulong`, `sbyte`, `char`, `decimal`, `float`, or `double`.</span></span>

| <span data-ttu-id="ae3cd-147">Tipo di proprietà/modello</span><span class="sxs-lookup"><span data-stu-id="ae3cd-147">Model/property type</span></span> | <span data-ttu-id="ae3cd-148">Tipo di provider/database</span><span class="sxs-lookup"><span data-stu-id="ae3cd-148">Provider/database type</span></span> | <span data-ttu-id="ae3cd-149">Conversione</span><span class="sxs-lookup"><span data-stu-id="ae3cd-149">Conversion</span></span>                                                | <span data-ttu-id="ae3cd-150">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="ae3cd-150">Usage</span></span>
|:--------------------|------------------------|-----------------------------------------------------------|------
| <span data-ttu-id="ae3cd-151">bool</span><span class="sxs-lookup"><span data-stu-id="ae3cd-151">bool</span></span>                | <span data-ttu-id="ae3cd-152">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="ae3cd-152">any_numeric_type</span></span>       | <span data-ttu-id="ae3cd-153">False/true a 0/1</span><span class="sxs-lookup"><span data-stu-id="ae3cd-153">False/true to 0/1</span></span>                                         | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="ae3cd-154">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="ae3cd-154">any_numeric_type</span></span>       | <span data-ttu-id="ae3cd-155">False/true per due numeri qualsiasi</span><span class="sxs-lookup"><span data-stu-id="ae3cd-155">False/true to any two numbers</span></span>                             | <span data-ttu-id="ae3cd-156">Utilizzare <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601>.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-156">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span></span>
|                     | <span data-ttu-id="ae3cd-157">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-157">string</span></span>                 | <span data-ttu-id="ae3cd-158">False/true in "Y"/"N"</span><span class="sxs-lookup"><span data-stu-id="ae3cd-158">False/true to "Y"/"N"</span></span>                                     | `.HasConversion<string>()`
|                     | <span data-ttu-id="ae3cd-159">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-159">string</span></span>                 | <span data-ttu-id="ae3cd-160">False/true per due stringhe qualsiasi</span><span class="sxs-lookup"><span data-stu-id="ae3cd-160">False/true to any two strings</span></span>                             | <span data-ttu-id="ae3cd-161">Utilizzare <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter>.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-161">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span></span>
| <span data-ttu-id="ae3cd-162">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="ae3cd-162">any_numeric_type</span></span>    | <span data-ttu-id="ae3cd-163">bool</span><span class="sxs-lookup"><span data-stu-id="ae3cd-163">bool</span></span>                   | <span data-ttu-id="ae3cd-164">da 0/1 a false/true</span><span class="sxs-lookup"><span data-stu-id="ae3cd-164">0/1 to false/true</span></span>                                         | `.HasConversion<bool>()`
|                     | <span data-ttu-id="ae3cd-165">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="ae3cd-165">any_numeric_type</span></span>       | <span data-ttu-id="ae3cd-166">Cast semplice</span><span class="sxs-lookup"><span data-stu-id="ae3cd-166">Simple cast</span></span>                                               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="ae3cd-167">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-167">string</span></span>                 | <span data-ttu-id="ae3cd-168">Numero sotto forma di stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-168">The number as a string</span></span>                                    | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-169">Enumerazione</span><span class="sxs-lookup"><span data-stu-id="ae3cd-169">Enum</span></span>                | <span data-ttu-id="ae3cd-170">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="ae3cd-170">any_numeric_type</span></span>       | <span data-ttu-id="ae3cd-171">Valore numerico dell'enumerazione</span><span class="sxs-lookup"><span data-stu-id="ae3cd-171">The numeric value of the enum</span></span>                             | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="ae3cd-172">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-172">string</span></span>                 | <span data-ttu-id="ae3cd-173">Rappresentazione di stringa del valore enum</span><span class="sxs-lookup"><span data-stu-id="ae3cd-173">The string representation of the enum value</span></span>               | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-174">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-174">string</span></span>              | <span data-ttu-id="ae3cd-175">bool</span><span class="sxs-lookup"><span data-stu-id="ae3cd-175">bool</span></span>                   | <span data-ttu-id="ae3cd-176">Analizza la stringa come bool</span><span class="sxs-lookup"><span data-stu-id="ae3cd-176">Parses the string as a bool</span></span>                               | `.HasConversion<bool>()`
|                     | <span data-ttu-id="ae3cd-177">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="ae3cd-177">any_numeric_type</span></span>       | <span data-ttu-id="ae3cd-178">Analizza la stringa come tipo numerico specificato</span><span class="sxs-lookup"><span data-stu-id="ae3cd-178">Parses the string as the given numeric type</span></span>               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="ae3cd-179">char</span><span class="sxs-lookup"><span data-stu-id="ae3cd-179">char</span></span>                   | <span data-ttu-id="ae3cd-180">Primo carattere della stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-180">The first character of the string</span></span>                         | `.HasConversion<char>()`
|                     | <span data-ttu-id="ae3cd-181">Datetime</span><span class="sxs-lookup"><span data-stu-id="ae3cd-181">DateTime</span></span>               | <span data-ttu-id="ae3cd-182">Analizza la stringa come DateTime</span><span class="sxs-lookup"><span data-stu-id="ae3cd-182">Parses the string as a DateTime</span></span>                           | `.HasConversion<DateTime>()`
|                     | <span data-ttu-id="ae3cd-183">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ae3cd-183">DateTimeOffset</span></span>         | <span data-ttu-id="ae3cd-184">Analizza la stringa come DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ae3cd-184">Parses the string as a DateTimeOffset</span></span>                     | `.HasConversion<DateTimeOffset>()`
|                     | <span data-ttu-id="ae3cd-185">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ae3cd-185">TimeSpan</span></span>               | <span data-ttu-id="ae3cd-186">Analizza la stringa come TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ae3cd-186">Parses the string as a TimeSpan</span></span>                           | `.HasConversion<TimeSpan>()`
|                     | <span data-ttu-id="ae3cd-187">Guid</span><span class="sxs-lookup"><span data-stu-id="ae3cd-187">Guid</span></span>                   | <span data-ttu-id="ae3cd-188">Analizza la stringa come GUID</span><span class="sxs-lookup"><span data-stu-id="ae3cd-188">Parses the string as a Guid</span></span>                               | `.HasConversion<Guid>()`
|                     | <span data-ttu-id="ae3cd-189">byte[]</span><span class="sxs-lookup"><span data-stu-id="ae3cd-189">byte[]</span></span>                 | <span data-ttu-id="ae3cd-190">Stringa come byte UTF8</span><span class="sxs-lookup"><span data-stu-id="ae3cd-190">The string as UTF8 bytes</span></span>                                  | `.HasConversion<byte[]>()`
| <span data-ttu-id="ae3cd-191">char</span><span class="sxs-lookup"><span data-stu-id="ae3cd-191">char</span></span>                | <span data-ttu-id="ae3cd-192">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-192">string</span></span>                 | <span data-ttu-id="ae3cd-193">Una singola stringa di caratteri</span><span class="sxs-lookup"><span data-stu-id="ae3cd-193">A single character string</span></span>                                 | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-194">Datetime</span><span class="sxs-lookup"><span data-stu-id="ae3cd-194">DateTime</span></span>            | <span data-ttu-id="ae3cd-195">long</span><span class="sxs-lookup"><span data-stu-id="ae3cd-195">long</span></span>                   | <span data-ttu-id="ae3cd-196">Data/ora codificata che conserva DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="ae3cd-196">Encoded date/time preserving DateTime.Kind</span></span>                | `.HasConversion<long>()`
|                     | <span data-ttu-id="ae3cd-197">long</span><span class="sxs-lookup"><span data-stu-id="ae3cd-197">long</span></span>                   | <span data-ttu-id="ae3cd-198">Tick</span><span class="sxs-lookup"><span data-stu-id="ae3cd-198">Ticks</span></span>                                                     | <span data-ttu-id="ae3cd-199">Utilizzare <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter>.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-199">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span></span>
|                     | <span data-ttu-id="ae3cd-200">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-200">string</span></span>                 | <span data-ttu-id="ae3cd-201">Stringa di data/ora della lingua inglese</span><span class="sxs-lookup"><span data-stu-id="ae3cd-201">Invariant culture date/time string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-202">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ae3cd-202">DateTimeOffset</span></span>      | <span data-ttu-id="ae3cd-203">long</span><span class="sxs-lookup"><span data-stu-id="ae3cd-203">long</span></span>                   | <span data-ttu-id="ae3cd-204">Data/ora codificata con offset</span><span class="sxs-lookup"><span data-stu-id="ae3cd-204">Encoded date/time with offset</span></span>                             | `.HasConversion<long>()`
|                     | <span data-ttu-id="ae3cd-205">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-205">string</span></span>                 | <span data-ttu-id="ae3cd-206">Stringa di data/ora della lingua inglese con offset</span><span class="sxs-lookup"><span data-stu-id="ae3cd-206">Invariant culture date/time string with offset</span></span>            | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-207">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ae3cd-207">TimeSpan</span></span>            | <span data-ttu-id="ae3cd-208">long</span><span class="sxs-lookup"><span data-stu-id="ae3cd-208">long</span></span>                   | <span data-ttu-id="ae3cd-209">Tick</span><span class="sxs-lookup"><span data-stu-id="ae3cd-209">Ticks</span></span>                                                     | `.HasConversion<long>()`
|                     | <span data-ttu-id="ae3cd-210">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-210">string</span></span>                 | <span data-ttu-id="ae3cd-211">Stringa dell'intervallo di tempo della lingua inglese</span><span class="sxs-lookup"><span data-stu-id="ae3cd-211">Invariant culture time span string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-212">Uri</span><span class="sxs-lookup"><span data-stu-id="ae3cd-212">Uri</span></span>                 | <span data-ttu-id="ae3cd-213">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-213">string</span></span>                 | <span data-ttu-id="ae3cd-214">URI sotto forma di stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-214">The URI as a string</span></span>                                       | `.HasConversion<string>()`
| <span data-ttu-id="ae3cd-215">PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="ae3cd-215">PhysicalAddress</span></span>     | <span data-ttu-id="ae3cd-216">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-216">string</span></span>                 | <span data-ttu-id="ae3cd-217">Indirizzo come stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-217">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="ae3cd-218">byte[]</span><span class="sxs-lookup"><span data-stu-id="ae3cd-218">byte[]</span></span>                 | <span data-ttu-id="ae3cd-219">Byte in ordine di rete big-endian</span><span class="sxs-lookup"><span data-stu-id="ae3cd-219">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="ae3cd-220">IPAddress</span><span class="sxs-lookup"><span data-stu-id="ae3cd-220">IPAddress</span></span>           | <span data-ttu-id="ae3cd-221">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-221">string</span></span>                 | <span data-ttu-id="ae3cd-222">Indirizzo come stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-222">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="ae3cd-223">byte[]</span><span class="sxs-lookup"><span data-stu-id="ae3cd-223">byte[]</span></span>                 | <span data-ttu-id="ae3cd-224">Byte in ordine di rete big-endian</span><span class="sxs-lookup"><span data-stu-id="ae3cd-224">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="ae3cd-225">Guid</span><span class="sxs-lookup"><span data-stu-id="ae3cd-225">Guid</span></span>                | <span data-ttu-id="ae3cd-226">string</span><span class="sxs-lookup"><span data-stu-id="ae3cd-226">string</span></span>                 | <span data-ttu-id="ae3cd-227">Il GUID nel formato ' dddddddd-dddd-dddd-dddd-dddddddddddd '</span><span class="sxs-lookup"><span data-stu-id="ae3cd-227">The GUID in 'dddddddd-dddd-dddd-dddd-dddddddddddd' format</span></span> | `.HasConversion<string>()`
|                     | <span data-ttu-id="ae3cd-228">byte[]</span><span class="sxs-lookup"><span data-stu-id="ae3cd-228">byte[]</span></span>                 | <span data-ttu-id="ae3cd-229">Byte nell'ordine di serializzazione binaria .NET</span><span class="sxs-lookup"><span data-stu-id="ae3cd-229">Bytes in .NET binary serialization order</span></span>                  | `.HasConversion<byte[]>()`

<span data-ttu-id="ae3cd-230">Si noti che queste conversioni presuppongono che il formato del valore sia appropriato per la conversione.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-230">Note that these conversions assume that the format of the value is appropriate for the conversion.</span></span> <span data-ttu-id="ae3cd-231">La conversione di stringhe in numeri, ad esempio, avrà esito negativo se i valori della stringa non possono essere analizzati come numeri.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-231">For example, converting strings to numbers will fail if the string values cannot be parsed as numbers.</span></span>

<span data-ttu-id="ae3cd-232">L'elenco completo dei convertitori predefiniti è:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-232">The full list of built-in converters is:</span></span>

* <span data-ttu-id="ae3cd-233">Conversione delle proprietà bool:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-233">Converting bool properties:</span></span>
  * <span data-ttu-id="ae3cd-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool in stringhe quali "Y" e "N"</span><span class="sxs-lookup"><span data-stu-id="ae3cd-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> - Bool to strings such as "Y" and "N"</span></span>
  * <span data-ttu-id="ae3cd-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool per due valori qualsiasi</span><span class="sxs-lookup"><span data-stu-id="ae3cd-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> - Bool to any two values</span></span>
  * <span data-ttu-id="ae3cd-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> Da-bool a zero e uno</span><span class="sxs-lookup"><span data-stu-id="ae3cd-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> - Bool to zero and one</span></span>
* <span data-ttu-id="ae3cd-237">Conversione delle proprietà della matrice di byte:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-237">Converting byte array properties:</span></span>
  * <span data-ttu-id="ae3cd-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Matrice di byte in stringa con codifica Base64</span><span class="sxs-lookup"><span data-stu-id="ae3cd-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="ae3cd-239">Qualsiasi conversione che richiede solo un cast di tipo</span><span class="sxs-lookup"><span data-stu-id="ae3cd-239">Any conversion that requires only a type-cast</span></span>
  * <span data-ttu-id="ae3cd-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Conversioni che richiedono solo un cast di tipo</span><span class="sxs-lookup"><span data-stu-id="ae3cd-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> - Conversions that require only a type cast</span></span>
* <span data-ttu-id="ae3cd-241">Conversione di proprietà Char:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-241">Converting char properties:</span></span>
  * <span data-ttu-id="ae3cd-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char a una stringa di caratteri singolo</span><span class="sxs-lookup"><span data-stu-id="ae3cd-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> - Char to single character string</span></span>
* <span data-ttu-id="ae3cd-243">Conversione delle <xref:System.DateTimeOffset> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-243">Converting <xref:System.DateTimeOffset> properties:</span></span>
  * <span data-ttu-id="ae3cd-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> a valore a 64 bit con codifica binaria</span><span class="sxs-lookup"><span data-stu-id="ae3cd-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> to binary-encoded 64-bit value</span></span>
  * <span data-ttu-id="ae3cd-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> alla matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ae3cd-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> to byte array</span></span>
  * <span data-ttu-id="ae3cd-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> to string</span></span>
* <span data-ttu-id="ae3cd-247">Conversione delle <xref:System.DateTime> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-247">Converting <xref:System.DateTime> properties:</span></span>
  * <span data-ttu-id="ae3cd-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> a un valore a 64 bit incluso DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="ae3cd-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> to 64-bit value including DateTimeKind</span></span>
  * <span data-ttu-id="ae3cd-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> to string</span></span>
  * <span data-ttu-id="ae3cd-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> per eseguire il ciclo</span><span class="sxs-lookup"><span data-stu-id="ae3cd-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> to ticks</span></span>
* <span data-ttu-id="ae3cd-251">Conversione delle proprietà enum:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-251">Converting enum properties:</span></span>
  * <span data-ttu-id="ae3cd-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enum al numero sottostante</span><span class="sxs-lookup"><span data-stu-id="ae3cd-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> - Enum to underlying number</span></span>
  * <span data-ttu-id="ae3cd-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> - Enum to string</span></span>
* <span data-ttu-id="ae3cd-254">Conversione delle <xref:System.Guid> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-254">Converting <xref:System.Guid> properties:</span></span>
  * <span data-ttu-id="ae3cd-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> alla matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ae3cd-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> to byte array</span></span>
  * <span data-ttu-id="ae3cd-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> to string</span></span>
* <span data-ttu-id="ae3cd-257">Conversione delle <xref:System.Net.IPAddress> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-257">Converting <xref:System.Net.IPAddress> properties:</span></span>
  * <span data-ttu-id="ae3cd-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> alla matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ae3cd-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> to byte array</span></span>
  * <span data-ttu-id="ae3cd-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> to string</span></span>
* <span data-ttu-id="ae3cd-260">Conversione delle proprietà numeriche (int, Double, Decimal e così via):</span><span class="sxs-lookup"><span data-stu-id="ae3cd-260">Converting numeric (int, double, decimal, etc.) properties:</span></span>
  * <span data-ttu-id="ae3cd-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Qualsiasi valore numerico in una matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ae3cd-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> - Any numerical value to byte array</span></span>
  * <span data-ttu-id="ae3cd-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Qualsiasi valore numerico da stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> - Any numerical value to string</span></span>
* <span data-ttu-id="ae3cd-263">Conversione delle <xref:System.Net.NetworkInformation.PhysicalAddress> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-263">Converting <xref:System.Net.NetworkInformation.PhysicalAddress> properties:</span></span>
  * <span data-ttu-id="ae3cd-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> alla matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ae3cd-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to byte array</span></span>
  * <span data-ttu-id="ae3cd-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to string</span></span>
* <span data-ttu-id="ae3cd-266">Conversione delle proprietà di stringa:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-266">Converting string properties:</span></span>
  * <span data-ttu-id="ae3cd-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Stringhe quali "Y" e "N" a bool</span><span class="sxs-lookup"><span data-stu-id="ae3cd-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> - Strings such as "Y" and "N" to bool</span></span>
  * <span data-ttu-id="ae3cd-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Da stringa a UTF8 byte</span><span class="sxs-lookup"><span data-stu-id="ae3cd-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> - String to UTF8 bytes</span></span>
  * <span data-ttu-id="ae3cd-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Da stringa a carattere</span><span class="sxs-lookup"><span data-stu-id="ae3cd-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> - String to character</span></span>
  * <span data-ttu-id="ae3cd-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -Stringa a <xref:System.DateTime></span><span class="sxs-lookup"><span data-stu-id="ae3cd-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> - String to <xref:System.DateTime></span></span>
  * <span data-ttu-id="ae3cd-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -Stringa a <xref:System.DateTimeOffset></span><span class="sxs-lookup"><span data-stu-id="ae3cd-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> - String to <xref:System.DateTimeOffset></span></span>
  * <span data-ttu-id="ae3cd-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Stringa da enumerare</span><span class="sxs-lookup"><span data-stu-id="ae3cd-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> - String to enum</span></span>
  * <span data-ttu-id="ae3cd-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -Stringa a <xref:System.Guid></span><span class="sxs-lookup"><span data-stu-id="ae3cd-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> - String to <xref:System.Guid></span></span>
  * <span data-ttu-id="ae3cd-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Stringa in tipo numerico</span><span class="sxs-lookup"><span data-stu-id="ae3cd-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> - String to numeric type</span></span>
  * <span data-ttu-id="ae3cd-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -Stringa a <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="ae3cd-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> - String to <xref:System.TimeSpan></span></span>
  * <span data-ttu-id="ae3cd-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -Stringa a <xref:System.Uri></span><span class="sxs-lookup"><span data-stu-id="ae3cd-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> - String to <xref:System.Uri></span></span>
* <span data-ttu-id="ae3cd-277">Conversione delle <xref:System.TimeSpan> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-277">Converting <xref:System.TimeSpan> properties:</span></span>
  * <span data-ttu-id="ae3cd-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> to string</span></span>
  * <span data-ttu-id="ae3cd-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> per eseguire il ciclo</span><span class="sxs-lookup"><span data-stu-id="ae3cd-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> to ticks</span></span>
* <span data-ttu-id="ae3cd-280">Conversione delle <xref:System.Uri> proprietà:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-280">Converting <xref:System.Uri> properties:</span></span>
  * <span data-ttu-id="ae3cd-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> in stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> to string</span></span>

<span data-ttu-id="ae3cd-282">Si noti che tutti i convertitori incorporati sono senza stato e pertanto una singola istanza può essere condivisa in modo sicuro da più proprietà.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-282">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="column-facets-and-mapping-hints"></a><span data-ttu-id="ae3cd-283">Facet di colonna e hint di mapping</span><span class="sxs-lookup"><span data-stu-id="ae3cd-283">Column facets and mapping hints</span></span>

<span data-ttu-id="ae3cd-284">Alcuni tipi di database hanno facet che modificano il modo in cui vengono archiviati i dati.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-284">Some database types have facets that modify how the data is stored.</span></span> <span data-ttu-id="ae3cd-285">Tra queste sono incluse:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-285">These include:</span></span>

* <span data-ttu-id="ae3cd-286">Precisione e scala per i numeri decimali e le colonne di data/ora</span><span class="sxs-lookup"><span data-stu-id="ae3cd-286">Precision and scale for decimals and date/time columns</span></span>
* <span data-ttu-id="ae3cd-287">Dimensioni/lunghezza per le colonne binarie e stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-287">Size/length for binary and string columns</span></span>
* <span data-ttu-id="ae3cd-288">Unicode per le colonne di tipo stringa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-288">Unicode for string columns</span></span>

<span data-ttu-id="ae3cd-289">Questi facet possono essere configurati in modo normale per una proprietà che utilizza un convertitore di valori e verranno applicati al tipo di database convertito.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-289">These facets can be configured in the normal way for a property that uses a value converter, and will apply to the converted database type.</span></span> <span data-ttu-id="ae3cd-290">Ad esempio, quando si esegue la conversione da enum a Strings, è possibile specificare che la colonna di database non deve essere Unicode e archiviare un massimo di 20 caratteri:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-290">For example, when converting from an enum to strings, we can specify that the database column should be non-Unicode and store up to 20 characters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>()
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByClrTypeWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrTypeWithFacets)]

<span data-ttu-id="ae3cd-291">In alternativa, quando si crea il convertitore in modo esplicito:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-291">Or, when creating the converter explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter)
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithFacets)]

<span data-ttu-id="ae3cd-292">In questo modo si ottiene una `varchar(20)` colonna quando si utilizzano migrazioni di EF core rispetto SQL Server:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-292">This results in a `varchar(20)` column when using EF Core migrations against SQL Server:</span></span>

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

<span data-ttu-id="ae3cd-293">Tuttavia, se per impostazione predefinita tutte le `EquineBeast` colonne devono essere `varchar(20)` , queste informazioni possono essere assegnate al convertitore di valori come <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-293">However, if by default all `EquineBeast` columns should be `varchar(20)`, then this information can be given to the value converter as a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints>.</span></span> <span data-ttu-id="ae3cd-294">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-294">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v),
                    new ConverterMappingHints(size: 20, unicode: false));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithMappingHints](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithMappingHints)]

<span data-ttu-id="ae3cd-295">A questo punto, ogni volta che si utilizza questo convertitore, la colonna del database sarà non Unicode con una lunghezza massima di 20.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-295">Now any time this converter is used, the database column will be non-unicode with a max length of 20.</span></span> <span data-ttu-id="ae3cd-296">Tuttavia, si tratta solo di hint perché vengono sottoposti a override da qualsiasi facet impostato in modo esplicito sulla proprietà mappata.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-296">However, these are only hints since they are be overridden by any facets explicitly set on the mapped property.</span></span>

## <a name="examples"></a><span data-ttu-id="ae3cd-297">Esempio</span><span class="sxs-lookup"><span data-stu-id="ae3cd-297">Examples</span></span>

### <a name="simple-value-objects"></a><span data-ttu-id="ae3cd-298">Oggetti valore semplice</span><span class="sxs-lookup"><span data-stu-id="ae3cd-298">Simple value objects</span></span>

<span data-ttu-id="ae3cd-299">In questo esempio viene usato un tipo semplice per eseguire il wrapping di un tipo primitivo.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-299">This example uses a simple type to wrap a primitive type.</span></span> <span data-ttu-id="ae3cd-300">Questa operazione può essere utile quando si desidera che il tipo nel modello sia più specifico (e pertanto più indipendente dai tipi) rispetto a un tipo primitivo.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-300">This can be useful when you want the type in your model to be more specific (and hence more type-safe) than a primitive type.</span></span> <span data-ttu-id="ae3cd-301">In questo esempio, il tipo è `Dollars` , che esegue il wrapping della primitiva decimale:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-301">In this example, that type is `Dollars`, which wraps the decimal primitive:</span></span>

<!--
        public readonly struct Dollars
        {
            public Dollars(decimal amount) 
                => Amount = amount;
            
            public decimal Amount { get; }

            public override string ToString() 
                => $"${Amount}";
        }
-->
[!code-csharp[SimpleValueObject](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObject)]

<span data-ttu-id="ae3cd-302">Può essere usato in un tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-302">This can be used in an entity type:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

<span data-ttu-id="ae3cd-303">E vengono convertiti nell'oggetto sottostante `decimal` quando vengono archiviati nel database:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-303">And converted to the underlying `decimal` when stored in the database:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> <span data-ttu-id="ae3cd-304">Questo oggetto valore viene implementato come struct di sola [lettura](/dotnet/csharp/language-reference/builtin-types/struct).</span><span class="sxs-lookup"><span data-stu-id="ae3cd-304">This value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="ae3cd-305">Ciò significa che EF Core possibile creare snapshot e confrontare i valori senza problemi.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-305">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="ae3cd-306">Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-306">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="composite-value-objects"></a><span data-ttu-id="ae3cd-307">Oggetti valore composito</span><span class="sxs-lookup"><span data-stu-id="ae3cd-307">Composite value objects</span></span>

<span data-ttu-id="ae3cd-308">Nell'esempio precedente, il tipo di oggetto valore conteneva solo una singola proprietà.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-308">In the previous example, the value object type contained only a single property.</span></span> <span data-ttu-id="ae3cd-309">È più comune per un tipo di oggetto valore comporre più proprietà che insieme formano un concetto di dominio.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-309">It is more common for a value object type to compose multiple properties that together form a domain concept.</span></span> <span data-ttu-id="ae3cd-310">Ad esempio, un `Money` tipo generale che contiene sia la quantità che la valuta:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-310">For example, a general `Money` type that contains both the amount and the currency:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[CompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObject)]

<span data-ttu-id="ae3cd-311">Questo oggetto valore può essere utilizzato in un tipo di entità come prima:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-311">This value object can be used in an entity type as before:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

<span data-ttu-id="ae3cd-312">I convertitori di valori possono attualmente convertire solo i valori da e verso una singola colonna di database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-312">Value converters can currently only convert values to and from a single database column.</span></span> <span data-ttu-id="ae3cd-313">Questa limitazione indica che tutti i valori delle proprietà dell'oggetto devono essere codificati in un unico valore di colonna.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-313">This limitation means that all property values from the object must be encoded into a single column value.</span></span> <span data-ttu-id="ae3cd-314">Questa operazione viene in genere gestita serializzando l'oggetto mentre viene inserito nel database, quindi deserializzarlo di nuovo in uscita. Ad esempio, utilizzando <xref:System.Text.Json> :</span><span class="sxs-lookup"><span data-stu-id="ae3cd-314">This is typically handled by serializing the object as it goes into the database, and then deserializing it again on the way out. For example, using <xref:System.Text.Json>:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> <span data-ttu-id="ae3cd-315">Si prevede di consentire il mapping di un oggetto a più colonne nel EF Core 6,0, eliminando la necessità di usare la serializzazione qui.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-315">We plan to allow mapping an object to multiple columns in EF Core 6.0, removing the need to use serialization here.</span></span> <span data-ttu-id="ae3cd-316">Questa operazione viene rilevata dal [problema GitHub #13947](https://github.com/dotnet/efcore/issues/13947).</span><span class="sxs-lookup"><span data-stu-id="ae3cd-316">This is tracked by [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947).</span></span>

> [!NOTE]
> <span data-ttu-id="ae3cd-317">Come nell'esempio precedente, questo oggetto valore viene implementato come struct di sola [lettura](/dotnet/csharp/language-reference/builtin-types/struct).</span><span class="sxs-lookup"><span data-stu-id="ae3cd-317">As with the previous example, this value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="ae3cd-318">Ciò significa che EF Core possibile creare snapshot e confrontare i valori senza problemi.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-318">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="ae3cd-319">Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-319">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-primitives"></a><span data-ttu-id="ae3cd-320">Raccolte di primitive</span><span class="sxs-lookup"><span data-stu-id="ae3cd-320">Collections of primitives</span></span>

<span data-ttu-id="ae3cd-321">La serializzazione può essere usata anche per archiviare una raccolta di valori primitivi.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-321">Serialization can also be used to store a collection of primitive values.</span></span> <span data-ttu-id="ae3cd-322">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-322">For example:</span></span>

<!--
        public class Post
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Contents { get; set; }

            public ICollection<string> Tags { get; set; }
        }
-->
[!code-csharp[PrimitiveCollectionModel](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=PrimitiveCollectionModel)]

<span data-ttu-id="ae3cd-323">Utilizzando di <xref:System.Text.Json> nuovo:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-323">Using <xref:System.Text.Json> again:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.Tags)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<string>>(v, null),
                        new ValueComparer<ICollection<string>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (ICollection<string>)c.ToList()));
-->
[!code-csharp[ConfigurePrimitiveCollection](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=ConfigurePrimitiveCollection)]

<span data-ttu-id="ae3cd-324">`ICollection<string>` rappresenta un tipo di riferimento modificabile.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-324">`ICollection<string>` represents a mutable reference type.</span></span> <span data-ttu-id="ae3cd-325">Ciò significa che <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> è necessario un oggetto affinché EF core possa rilevare e rilevare correttamente le modifiche.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-325">This means that a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> is needed so that EF Core can track and detect changes correctly.</span></span> <span data-ttu-id="ae3cd-326">Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-326">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-value-objects"></a><span data-ttu-id="ae3cd-327">Raccolte di oggetti valore</span><span class="sxs-lookup"><span data-stu-id="ae3cd-327">Collections of value objects</span></span>

<span data-ttu-id="ae3cd-328">Combinando i due esempi precedenti è possibile creare una raccolta di oggetti valore.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-328">Combining the previous two examples together we can create a collection of value objects.</span></span> <span data-ttu-id="ae3cd-329">Si consideri, ad esempio, un `AnnualFinance` tipo che modella le finanze del Blog per un singolo anno:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-329">For example, consider an `AnnualFinance` type that models blog finances for a single year:</span></span>

<!--
        public readonly struct AnnualFinance
        {
            [JsonConstructor]
            public AnnualFinance(int year, Money income, Money expenses)
            {
                Year = year;
                Income = income;
                Expenses = expenses;
            }

            public int Year { get; }
            public Money Income { get; }
            public Money Expenses { get; }
            public Money Revenue => new Money(Income.Amount - Expenses.Amount, Income.Currency);
        }
-->
[!code-csharp[ValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollection)]

<span data-ttu-id="ae3cd-330">Questo tipo compone diversi `Money` tipi creati in precedenza:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-330">This type composes several of the `Money` types we created previously:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[ValueObjectCollectionMoney](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionMoney)]

<span data-ttu-id="ae3cd-331">È quindi possibile aggiungere una raccolta di `AnnualFinance` al tipo di entità:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-331">We can then add a collection of `AnnualFinance` to our entity type:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

<span data-ttu-id="ae3cd-332">E usare di nuovo la serializzazione per archiviare questo:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-332">And again use serialization to store this:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Finances)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<AnnualFinance>>(v, null),
                        new ValueComparer<IList<AnnualFinance>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (IList<AnnualFinance>)c.ToList()));
-->
[!code-csharp[ConfigureValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ConfigureValueObjectCollection)]

> [!NOTE]
> <span data-ttu-id="ae3cd-333">Come in precedenza, questa conversione richiede un oggetto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-333">As before, this conversion requires a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="ae3cd-334">Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-334">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="value-objects-as-keys"></a><span data-ttu-id="ae3cd-335">Oggetti valore come chiavi</span><span class="sxs-lookup"><span data-stu-id="ae3cd-335">Value objects as keys</span></span>

<span data-ttu-id="ae3cd-336">A volte le proprietà di chiave primitive possono essere incapsulate in oggetti valore per aggiungere un livello aggiuntivo di indipendenza dai tipi nell'assegnazione di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-336">Sometimes primitive key properties may be wrapped in value objects to add an additional level of type-safety in assigning values.</span></span> <span data-ttu-id="ae3cd-337">Ad esempio, è possibile implementare un tipo di chiave per i Blog e un tipo di chiave per i post:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-337">For example, we could implement a key type for blogs, and a key type for posts:</span></span>

<!--
        public readonly struct BlogKey
        {
            public BlogKey(int id) => Id = id;
            public int Id { get; }
        }

        public readonly struct PostKey
        {
            public PostKey(int id) => Id = id;
            public int Id { get; }
        }
-->
[!code-csharp[KeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjects)]

<span data-ttu-id="ae3cd-338">Questi possono quindi essere utilizzati nel modello di dominio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-338">These can then be used in the domain model:</span></span>

<!--
        public class Blog
        {
            public BlogKey Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public PostKey Id { get; set; }

            public string Title { get; set; }
            public string Content { get; set; }

            public BlogKey? BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[KeyValueObjectsModel](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjectsModel)]

<span data-ttu-id="ae3cd-339">Si noti che `Blog.Id` non è possibile assegnare accidentalmente un oggetto `PostKey` e `Post.Id` non è possibile assegnargli un errore `BlogKey` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-339">Notice that `Blog.Id` cannot accidentally be assigned a `PostKey`, and `Post.Id` cannot accidentally be assigned a `BlogKey`.</span></span> <span data-ttu-id="ae3cd-340">Analogamente, la `Post.BlogId` proprietà della chiave esterna deve essere assegnata a `BlogKey` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-340">Similarly, the `Post.BlogId` foreign key property must be assigned a `BlogKey`.</span></span>

> [!NOTE]
> <span data-ttu-id="ae3cd-341">La visualizzazione di questo modello non significa che sia consigliabile.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-341">Showing this pattern does not mean we recommend it.</span></span> <span data-ttu-id="ae3cd-342">Valutare attentamente se questo livello di astrazione sta aiutando o ostacolare l'esperienza di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-342">Carefully consider whether this level of abstraction is helping or hampering your development experience.</span></span> <span data-ttu-id="ae3cd-343">Si consiglia inoltre di utilizzare le operazioni di navigazione e le chiavi generate anziché gestire direttamente i valori di chiave.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-343">Also, consider using navigations and generated keys instead of dealing with key values directly.</span></span>

<span data-ttu-id="ae3cd-344">È quindi possibile eseguire il mapping di queste proprietà chiave utilizzando convertitori di valori:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-344">These key properties can then be mapped using value converters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var blogKeyConverter = new ValueConverter<BlogKey, int>(
                    v => v.Id,
                    v => new BlogKey(v));

                modelBuilder.Entity<Blog>().Property(e => e.Id).HasConversion(blogKeyConverter);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasConversion(v => v.Id, v => new PostKey(v));
                            b.Property(e => e.BlogId).HasConversion(blogKeyConverter);
                        });
            }
-->
[!code-csharp[ConfigureKeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=ConfigureKeyValueObjects)]

> [!NOTE]
> <span data-ttu-id="ae3cd-345">Attualmente le proprietà chiave con conversioni non possono utilizzare valori di chiave generati.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-345">Currently key properties with conversions cannot use generated key values.</span></span> <span data-ttu-id="ae3cd-346">Votare per il [problema GitHub #11597](https://github.com/dotnet/efcore/issues/11597) per rimuovere questa limitazione.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-346">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) to have this limitation removed.</span></span>

### <a name="use-ulong-for-timestamprowversion"></a><span data-ttu-id="ae3cd-347">Usare ULONG per timestamp/rowversion</span><span class="sxs-lookup"><span data-stu-id="ae3cd-347">Use ulong for timestamp/rowversion</span></span>

<span data-ttu-id="ae3cd-348">SQL Server supporta la [concorrenza ottimistica](xref:core/saving/concurrency) automatica con [ `rowversion` / `timestamp` colonne binarie a 8 byte](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="ae3cd-348">SQL Server supports automatic [optimistic concurrency](xref:core/saving/concurrency) using [8-byte binary `rowversion`/`timestamp` columns](/sql/t-sql/data-types/rowversion-transact-sql).</span></span> <span data-ttu-id="ae3cd-349">Questi vengono sempre letti e scritti nel database usando una matrice a 8 byte.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-349">These are always read from and written to the database using an 8-byte array.</span></span> <span data-ttu-id="ae3cd-350">Tuttavia, le matrici di byte sono un tipo di riferimento modificabile, che li rende piuttosto penosi da gestire.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-350">However, byte arrays are a mutable reference type, which makes them somewhat painful to deal with.</span></span> <span data-ttu-id="ae3cd-351">I convertitori di valori consentono invece di eseguire il `rowversion` mapping a una `ulong` proprietà, che è molto più appropriata e facile da utilizzare rispetto alla matrice di byte.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-351">Value converters allow the `rowversion` to instead be mapped to a `ulong` property, which is much more appropriate and easy to use than the byte array.</span></span> <span data-ttu-id="ae3cd-352">Si consideri ad esempio un' `Blog` entità con un token di concorrenza ULONG:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-352">For example, consider a `Blog` entity with a ulong concurrency token:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

<span data-ttu-id="ae3cd-353">È possibile eseguirne il mapping a una colonna di SQL Server `rowversion` usando un convertitore di valori:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-353">This can be mapped to a SQL server `rowversion` column using a value converter:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a><span data-ttu-id="ae3cd-354">Specificare DateTime. Kind durante la lettura delle date</span><span class="sxs-lookup"><span data-stu-id="ae3cd-354">Specify the DateTime.Kind when reading dates</span></span>

<span data-ttu-id="ae3cd-355">SQL Server Elimina il <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag quando si archivia un oggetto <xref:System.DateTime> come [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) o [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-355">SQL Server discards the <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag when storing a <xref:System.DateTime> as a [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) or [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql).</span></span> <span data-ttu-id="ae3cd-356">Ciò significa che i valori DateTime restituiti dal database hanno sempre un <xref:System.DateTimeKind> di `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-356">This means that DateTime values coming back from the database always have a <xref:System.DateTimeKind> of `Unspecified`.</span></span>

<span data-ttu-id="ae3cd-357">I convertitori di valori possono essere utilizzati in due modi per gestire questo problema.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-357">Value converters can be used in two ways to deal with this.</span></span> <span data-ttu-id="ae3cd-358">In primo luogo, EF Core dispone di un convertitore di valori che consente di creare un valore opaco a 8 byte che conserva il `Kind` flag.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-358">First, EF Core has a value converter that creates an 8-byte opaque value which preserves the `Kind` flag.</span></span> <span data-ttu-id="ae3cd-359">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-359">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

<span data-ttu-id="ae3cd-360">In questo modo è possibile combinare i valori DateTime con `Kind` flag diversi nel database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-360">This allows DateTime values with different `Kind` flags to be mixed in the database.</span></span>

<span data-ttu-id="ae3cd-361">Il problema di questo approccio è che il database non dispone più di colonne riconoscibili `datetime` o `datetime2` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-361">The problem with this approach is that the database no longer has recognizable `datetime` or `datetime2` columns.</span></span> <span data-ttu-id="ae3cd-362">Quindi, invece, è normale archiviare sempre l'ora UTC (o, meno comunemente, sempre l'ora locale) e quindi ignorare il `Kind` flag o impostarlo sul valore appropriato usando un convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-362">So instead it is common to always store UTC time (or, less commonly, always local time) and then either ignore the `Kind` flag or set it to the appropriate value using a value converter.</span></span> <span data-ttu-id="ae3cd-363">Il convertitore seguente, ad esempio, assicura che il `DateTime` valore letto dal database disponga di <xref:System.DateTimeKind> `UTC` :</span><span class="sxs-lookup"><span data-stu-id="ae3cd-363">For example, the converter below ensures that the `DateTime` value read from the database will have the <xref:System.DateTimeKind> `UTC`:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

<span data-ttu-id="ae3cd-364">Se viene impostata una combinazione di valori locali e UTC nelle istanze di entità, il convertitore può essere usato per eseguire la conversione in modo appropriato prima dell'inserimento.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-364">If a mix of local and UTC values are being set in entity instances, then the converter can be used to convert appropriately before inserting.</span></span> <span data-ttu-id="ae3cd-365">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-365">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> <span data-ttu-id="ae3cd-366">Prendere in considerazione la possibilità di unificare tutto il codice di accesso al database in modo da utilizzare l'ora UTC per sempre, occupandosi dell'ora locale quando si presentano dati agli utenti.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-366">Carefully consider unifying all database access code to use UTC time all the time, only dealing with local time when presenting data to users.</span></span>

### <a name="use-case-insensitive-string-keys"></a><span data-ttu-id="ae3cd-367">Usare chiavi di stringa senza distinzione tra maiuscole e minuscole</span><span class="sxs-lookup"><span data-stu-id="ae3cd-367">Use case-insensitive string keys</span></span>

<span data-ttu-id="ae3cd-368">Per impostazione predefinita, alcuni database, tra cui SQL Server, eseguono confronti di stringhe senza distinzione tra maiuscole e minuscole.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-368">Some databases, including SQL Server, perform case-insensitive string comparisons by default.</span></span> <span data-ttu-id="ae3cd-369">.NET, d'altra parte, esegue confronti tra stringhe con distinzione tra maiuscole e minuscole per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-369">.NET, on the other hand, performs case-sensitive string comparisons by default.</span></span> <span data-ttu-id="ae3cd-370">Ciò significa che un valore di chiave esterna, ad esempio "DotNet", corrisponderà al valore di chiave primaria "DotNet" in SQL Server, ma non corrisponderà al EF Core.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-370">This means that a foreign key value like "DotNet" will match the primary key value "dotnet" on SQL Server, but will not match it in EF Core.</span></span> <span data-ttu-id="ae3cd-371">Un operatore di confronto dei valori per le chiavi può essere utilizzato per forzare EF Core nei confronti di stringhe senza distinzione tra maiuscole e minuscole come nel database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-371">A value comparer for keys can be used to force EF Core into case-insensitive string comparisons like in the database.</span></span> <span data-ttu-id="ae3cd-372">Si consideri, ad esempio, un modello di Blog/post con chiavi di stringa:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-372">For example, consider a blog/posts model with string keys:</span></span>

<!--
        public class Blog
        {
            public string Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public string Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public string BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[CaseInsensitiveStringsModel](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=CaseInsensitiveStringsModel)]

<span data-ttu-id="ae3cd-373">Questa operazione non funzionerà come previsto se alcuni `Post.BlogId` valori hanno maiuscole e minuscole diverse.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-373">This will not work as expected if some of the `Post.BlogId` values have different casing.</span></span> <span data-ttu-id="ae3cd-374">Gli errori causati da questa funzione dipendono da ciò che l'applicazione sta eseguendo, ma in genere coinvolgono grafici di oggetti che non sono [corretti](xref:core/change-tracking/relationship-changes) correttamente e/o aggiornamenti che non riescono perché il valore FK non è corretto.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-374">The errors caused by this will depend on what the application is doing, but typically involve graphs of objects that are not [fixed-up](xref:core/change-tracking/relationship-changes) correctly, and/or updates that fail because the FK value is wrong.</span></span> <span data-ttu-id="ae3cd-375">Per risolvere il problema, è possibile usare un operatore di confronto del valore:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-375">A value comparer can be used to correct this:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .Metadata.SetValueComparer(comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).Metadata.SetValueComparer(comparer);
                            b.Property(e => e.BlogId).Metadata.SetValueComparer(comparer);
                        });
            }
-->
[!code-csharp[ConfigureCaseInsensitiveStrings](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=ConfigureCaseInsensitiveStrings)]

> [!NOTE]
> <span data-ttu-id="ae3cd-376">I confronti tra stringhe .NET e i confronti di stringhe di database possono variare in modo diverso rispetto a maiuscole</span><span class="sxs-lookup"><span data-stu-id="ae3cd-376">.NET string comparisons and database string comparisons can differ in more than just case sensitivity.</span></span> <span data-ttu-id="ae3cd-377">Questo modello funziona per semplici chiavi ASCII, ma potrebbe non riuscire a usare chiavi con qualsiasi tipo di caratteri specifici delle impostazioni cultura.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-377">This pattern works for simple ASCII keys, but may fail for keys with any kind of culture-specific characters.</span></span> <span data-ttu-id="ae3cd-378">Per ulteriori informazioni, vedere [regole di confronto e distinzione tra maiuscole e minuscole](xref:core/miscellaneous/collations-and-case-sensitivity) .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-378">See [Collations and Case Sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity) for more information.</span></span>

### <a name="handle-fixed-length-database-strings"></a><span data-ttu-id="ae3cd-379">Gestire le stringhe di database a lunghezza fissa</span><span class="sxs-lookup"><span data-stu-id="ae3cd-379">Handle fixed-length database strings</span></span>

<span data-ttu-id="ae3cd-380">Nell'esempio precedente non è necessario un convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-380">The previous example did not need a value converter.</span></span> <span data-ttu-id="ae3cd-381">Un convertitore può tuttavia essere utile per i tipi di stringa di database a lunghezza fissa, ad esempio `char(20)` o `nchar(20)` .</span><span class="sxs-lookup"><span data-stu-id="ae3cd-381">However, a converter can be useful for fixed-length database string types like `char(20)` or `nchar(20)`.</span></span> <span data-ttu-id="ae3cd-382">Le stringhe a lunghezza fissa vengono riempite fino alla loro lunghezza intera ogni volta che viene inserito un valore nel database.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-382">Fixed-length strings are padded to their full length whenever a value is inserted into the database.</span></span> <span data-ttu-id="ae3cd-383">Ciò significa che un valore chiave di " `dotnet` " verrà letto dal database come " `dotnet..............` ", dove `.` rappresenta uno spazio.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-383">This means that a key value of "`dotnet`" will be read back from the database as "`dotnet..............`", where `.` represents a space character.</span></span> <span data-ttu-id="ae3cd-384">Questa operazione non verrà quindi confrontata correttamente con i valori di chiave non riempiti.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-384">This will then not compare correctly with key values that are not padded.</span></span>

<span data-ttu-id="ae3cd-385">Un convertitore di valori può essere usato per tagliare la spaziatura interna durante la lettura dei valori di chiave.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-385">A value converter can be used to trim the padding when reading key values.</span></span> <span data-ttu-id="ae3cd-386">Questo può essere combinato con l'operatore di confronto del valore nell'esempio precedente per confrontare correttamente le chiavi ASCII senza distinzione tra maiuscole e minuscole a lunghezza fissa.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-386">This can be combined with the value comparer in the previous example to compare fixed length case-insensitive ASCII keys correctly.</span></span> <span data-ttu-id="ae3cd-387">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-387">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<string, string>(
                    v => v,
                    v => v.Trim());
                
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .HasColumnType("char(20)")
                    .HasConversion(converter, comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasColumnType("char(20)").HasConversion(converter, comparer);
                            b.Property(e => e.BlogId).HasColumnType("char(20)").HasConversion(converter, comparer);
                        });
            }
-->
[!code-csharp[ConfigureFixedLengthStrings](../../../samples/core/Modeling/ValueConversions/FixedLengthStrings.cs?name=ConfigureFixedLengthStrings)]

### <a name="encrypt-property-values"></a><span data-ttu-id="ae3cd-388">Crittografa valori proprietà</span><span class="sxs-lookup"><span data-stu-id="ae3cd-388">Encrypt property values</span></span>

<span data-ttu-id="ae3cd-389">I convertitori di valori possono essere usati per crittografare i valori delle proprietà prima di inviarli al database e quindi decrittografarli nel modo in cui vengono decrittografati. Ad esempio, usando l'inversione di stringa come sostituto di un algoritmo di crittografia reale:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-389">Value converters can be used to encrypt property values before sending them to the database, and then decrypt them on the way out. For example, using string reversal as a substitute for a real encryption algorithm:</span></span>

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> <span data-ttu-id="ae3cd-390">Attualmente non è possibile ottenere un riferimento al DbContext corrente o a un altro stato della sessione dall'interno di un convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-390">There is currently no way to get a reference to the current DbContext, or other session state, from within a value converter.</span></span> <span data-ttu-id="ae3cd-391">Questo limita i tipi di crittografia che è possibile usare.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-391">This limits the kinds of encryption that can be used.</span></span> <span data-ttu-id="ae3cd-392">Votare per il [problema GitHub #11597](https://github.com/dotnet/efcore/issues/12205) per rimuovere questa limitazione.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-392">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) to have this limitation removed.</span></span>

> [!WARNING]
> <span data-ttu-id="ae3cd-393">Assicurarsi di comprendere tutte le implicazioni se si esegue il rollup della propria crittografia per proteggere i dati sensibili.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-393">Make sure to understand all the implications if you roll your own encryption to protect sensitive data.</span></span> <span data-ttu-id="ae3cd-394">Si consiglia invece di utilizzare meccanismi di crittografia predefiniti, ad esempio [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-394">Consider instead using pre-built encryption mechanisms, such as [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) on SQL Server.</span></span>

## <a name="limitations"></a><span data-ttu-id="ae3cd-395">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="ae3cd-395">Limitations</span></span>

<span data-ttu-id="ae3cd-396">Esistono alcune limitazioni note correnti del sistema di conversione dei valori:</span><span class="sxs-lookup"><span data-stu-id="ae3cd-396">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="ae3cd-397">Attualmente non è possibile specificare in un'unica posizione che ogni proprietà di un determinato tipo deve usare lo stesso convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-397">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="ae3cd-398">Votare ( 👍 ) per il [problema di GitHub #10784](https://github.com/dotnet/efcore/issues/10784) se si tratta di un elemento necessario.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-398">Please vote (👍) for [GitHub issue #10784](https://github.com/dotnet/efcore/issues/10784) if this is something you need.</span></span>
* <span data-ttu-id="ae3cd-399">Come indicato in precedenza, `null` non è possibile convertire.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-399">As noted above, `null` cannot be converted.</span></span> <span data-ttu-id="ae3cd-400">Votare ( 👍 ) per il [problema di GitHub #13850](https://github.com/dotnet/efcore/issues/13850) se si tratta di un elemento necessario.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-400">Please vote (👍) for [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) if this is something you need.</span></span>
* <span data-ttu-id="ae3cd-401">Attualmente non è possibile distribuire una conversione di una proprietà in più colonne o viceversa.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-401">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span> <span data-ttu-id="ae3cd-402">Votare ( 👍 ) per il [problema di GitHub #13947](https://github.com/dotnet/efcore/issues/13947) se si tratta di un elemento necessario.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-402">Please vote (👍) for [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947) if this is something you need.</span></span>
* <span data-ttu-id="ae3cd-403">La generazione di valori non è supportata per la maggior parte delle chiavi mappate tramite convertitori di valori.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-403">Value generation is not supported for most keys mapped through value converters.</span></span> <span data-ttu-id="ae3cd-404">Votare ( 👍 ) per il [problema di GitHub #11597](https://github.com/dotnet/efcore/issues/11597) se si tratta di un elemento necessario.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-404">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) if this is something you need.</span></span>
* <span data-ttu-id="ae3cd-405">Le conversioni di valori non possono fare riferimento all'istanza corrente di DbContext.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-405">Value conversions cannot reference the current DbContext instance.</span></span> <span data-ttu-id="ae3cd-406">Votare ( 👍 ) per il [problema di GitHub #11597](https://github.com/dotnet/efcore/issues/12205) se si tratta di un elemento necessario.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-406">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) if this is something you need.</span></span>

<span data-ttu-id="ae3cd-407">La rimozione di queste limitazioni verrà presa in considerazione per le versioni future.</span><span class="sxs-lookup"><span data-stu-id="ae3cd-407">Removal of these limitations is being considered for future releases.</span></span>
