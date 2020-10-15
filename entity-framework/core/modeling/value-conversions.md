---
title: Conversioni di valori-EF Core
description: Configurazione di convertitori di valori in un modello di Entity Framework Core
author: ajcvickers
ms.date: 02/19/2018
uid: core/modeling/value-conversions
ms.openlocfilehash: 221560a145fe25c2b7bf094839dd37791bc25955
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063959"
---
# <a name="value-conversions"></a><span data-ttu-id="ff7c1-103">Conversioni di valori</span><span class="sxs-lookup"><span data-stu-id="ff7c1-103">Value Conversions</span></span>

<span data-ttu-id="ff7c1-104">I convertitori di valori consentono di convertire i valori delle proprietà durante la lettura o la scrittura nel database.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="ff7c1-105">Questa conversione può provenire da un valore a un altro dello stesso tipo (ad esempio, la crittografia di stringhe) o da un valore di un tipo a un valore di un altro tipo, ad esempio la conversione di valori enum da e verso stringhe nel database.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="ff7c1-106">Nozioni di base</span><span class="sxs-lookup"><span data-stu-id="ff7c1-106">Fundamentals</span></span>

<span data-ttu-id="ff7c1-107">I convertitori di valori vengono specificati in termini di `ModelClrType` e `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="ff7c1-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="ff7c1-108">Il tipo di modello è il tipo .NET della proprietà nel tipo di entità.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="ff7c1-109">Il tipo di provider è il tipo .NET riconosciuto dal provider di database.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="ff7c1-110">Ad esempio, per salvare le enumerazioni come stringhe nel database, il tipo di modello è il tipo dell'enumerazione e il tipo di provider è `String` .</span><span class="sxs-lookup"><span data-stu-id="ff7c1-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="ff7c1-111">Questi due tipi possono essere gli stessi.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-111">These two types can be the same.</span></span>

<span data-ttu-id="ff7c1-112">Le conversioni vengono definite utilizzando due `Func` alberi delle espressioni: uno da `ModelClrType` a `ProviderClrType` e l'altro da `ProviderClrType` a `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="ff7c1-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="ff7c1-113">Gli alberi delle espressioni vengono usati in modo che possano essere compilati nel codice di accesso al database per conversioni efficienti.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="ff7c1-114">Per le conversioni complesse, l'albero delle espressioni può essere una semplice chiamata a un metodo che esegue la conversione.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="ff7c1-115">Configurazione di un convertitore di valori</span><span class="sxs-lookup"><span data-stu-id="ff7c1-115">Configuring a value converter</span></span>

<span data-ttu-id="ff7c1-116">Le conversioni di valori sono definite nelle proprietà `OnModelCreating` del del `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="ff7c1-116">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="ff7c1-117">Si consideri, ad esempio, un'enumerazione e un tipo di entità definiti come segue:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-117">For example, consider an enum and entity type defined as:</span></span>

```csharp
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
```

<span data-ttu-id="ff7c1-118">È quindi possibile definire le conversioni in `OnModelCreating` per archiviare i valori enum come stringhe (ad esempio, "Donkey", "Mule",...) nel database:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-118">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]
> <span data-ttu-id="ff7c1-119">Un `null` valore non verrà mai passato a un convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="ff7c1-120">In questo modo l'implementazione delle conversioni risulta più semplice e consente di condividerle tra proprietà Nullable e non nullable.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="ff7c1-121">Classe ValueConverter</span><span class="sxs-lookup"><span data-stu-id="ff7c1-121">The ValueConverter class</span></span>

<span data-ttu-id="ff7c1-122">La chiamata a `HasConversion` come illustrato in precedenza creerà un' `ValueConverter` istanza e la imposterà sulla proprietà.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="ff7c1-123">L'oggetto `ValueConverter` può invece essere creato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="ff7c1-124">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-124">For example:</span></span>

```csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="ff7c1-125">Questa operazione può essere utile quando più proprietà utilizzano la stessa conversione.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]
> <span data-ttu-id="ff7c1-126">Attualmente non è possibile specificare in un'unica posizione che ogni proprietà di un determinato tipo deve usare lo stesso convertitore di valori.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="ff7c1-127">Questa funzionalità verrà considerata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="ff7c1-128">Convertitori predefiniti</span><span class="sxs-lookup"><span data-stu-id="ff7c1-128">Built-in converters</span></span>

<span data-ttu-id="ff7c1-129">EF Core viene fornito con un set di classi predefinite `ValueConverter` , disponibile nello `Microsoft.EntityFrameworkCore.Storage.ValueConversion` spazio dei nomi.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="ff7c1-130">Si tratta di:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-130">These are:</span></span>

* <span data-ttu-id="ff7c1-131">`BoolToZeroOneConverter` Da-bool a zero e uno</span><span class="sxs-lookup"><span data-stu-id="ff7c1-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="ff7c1-132">`BoolToStringConverter` -Bool in stringhe quali "Y" e "N"</span><span class="sxs-lookup"><span data-stu-id="ff7c1-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="ff7c1-133">`BoolToTwoValuesConverter` -Bool per due valori qualsiasi</span><span class="sxs-lookup"><span data-stu-id="ff7c1-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="ff7c1-134">`BytesToStringConverter` -Matrice di byte in stringa con codifica Base64</span><span class="sxs-lookup"><span data-stu-id="ff7c1-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="ff7c1-135">`CastingConverter` -Conversioni che richiedono solo un cast di tipo</span><span class="sxs-lookup"><span data-stu-id="ff7c1-135">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="ff7c1-136">`CharToStringConverter` -Char a una stringa di caratteri singolo</span><span class="sxs-lookup"><span data-stu-id="ff7c1-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="ff7c1-137">`DateTimeOffsetToBinaryConverter` Da-DateTimeOffset a valore a 64 bit con codifica binaria</span><span class="sxs-lookup"><span data-stu-id="ff7c1-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="ff7c1-138">`DateTimeOffsetToBytesConverter` Da-DateTimeOffset a matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ff7c1-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="ff7c1-139">`DateTimeOffsetToStringConverter` Da-DateTimeOffset a stringa</span><span class="sxs-lookup"><span data-stu-id="ff7c1-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="ff7c1-140">`DateTimeToBinaryConverter` Da-DateTime a 64 bit, incluso DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="ff7c1-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="ff7c1-141">`DateTimeToStringConverter` Da-DateTime a String</span><span class="sxs-lookup"><span data-stu-id="ff7c1-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="ff7c1-142">`DateTimeToTicksConverter` -DateTime per i cicli</span><span class="sxs-lookup"><span data-stu-id="ff7c1-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="ff7c1-143">`EnumToNumberConverter` -Enum al numero sottostante</span><span class="sxs-lookup"><span data-stu-id="ff7c1-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="ff7c1-144">`EnumToStringConverter` -Enum in stringa</span><span class="sxs-lookup"><span data-stu-id="ff7c1-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="ff7c1-145">`GuidToBytesConverter` -GUID a matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ff7c1-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="ff7c1-146">`GuidToStringConverter` -GUID a stringa</span><span class="sxs-lookup"><span data-stu-id="ff7c1-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="ff7c1-147">`NumberToBytesConverter` -Qualsiasi valore numerico in una matrice di byte</span><span class="sxs-lookup"><span data-stu-id="ff7c1-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="ff7c1-148">`NumberToStringConverter` -Qualsiasi valore numerico da stringa</span><span class="sxs-lookup"><span data-stu-id="ff7c1-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="ff7c1-149">`StringToBytesConverter` -Da stringa a UTF8 byte</span><span class="sxs-lookup"><span data-stu-id="ff7c1-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="ff7c1-150">`TimeSpanToStringConverter` Da-TimeSpan a stringa</span><span class="sxs-lookup"><span data-stu-id="ff7c1-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="ff7c1-151">`TimeSpanToTicksConverter` -TimeSpan per i cicli</span><span class="sxs-lookup"><span data-stu-id="ff7c1-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="ff7c1-152">Si noti che `EnumToStringConverter` è incluso nell'elenco.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="ff7c1-153">Ciò significa che non è necessario specificare la conversione in modo esplicito, come illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="ff7c1-154">Usare invece il convertitore predefinito:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-154">Instead, just use the built-in converter:</span></span>

```csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="ff7c1-155">Si noti che tutti i convertitori incorporati sono senza stato e pertanto una singola istanza può essere condivisa in modo sicuro da più proprietà.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="ff7c1-156">Conversioni predefinite</span><span class="sxs-lookup"><span data-stu-id="ff7c1-156">Pre-defined conversions</span></span>

<span data-ttu-id="ff7c1-157">Per le conversioni comuni per le quali esiste un convertitore incorporato, non è necessario specificare il convertitore in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="ff7c1-158">Al contrario, è sufficiente configurare il tipo di provider da usare e EF userà automaticamente il convertitore predefinito appropriato.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="ff7c1-159">Le conversioni da enum a stringa vengono usate come esempio sopra, ma EF eseguirà questa operazione automaticamente se il tipo di provider è configurato:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

```csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="ff7c1-160">La stessa operazione può essere eseguita specificando in modo esplicito il tipo di colonna.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="ff7c1-161">Ad esempio, se il tipo di entità è definito come segue:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-161">For example, if the entity type is defined like so:</span></span>

```csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="ff7c1-162">I valori enum verranno quindi salvati come stringhe nel database senza ulteriori operazioni di configurazione in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="ff7c1-162">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="ff7c1-163">Limitazioni</span><span class="sxs-lookup"><span data-stu-id="ff7c1-163">Limitations</span></span>

<span data-ttu-id="ff7c1-164">Esistono alcune limitazioni note correnti del sistema di conversione dei valori:</span><span class="sxs-lookup"><span data-stu-id="ff7c1-164">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="ff7c1-165">Come indicato in precedenza, `null` non è possibile convertire.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="ff7c1-166">Attualmente non è possibile distribuire una conversione di una proprietà in più colonne o viceversa.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="ff7c1-167">L'utilizzo di conversioni di valori può influisca sulla capacità di EF Core di tradurre espressioni in SQL.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="ff7c1-168">Per tali casi verrà registrato un avviso.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="ff7c1-169">La rimozione di queste limitazioni verrà considerata per una versione futura.</span><span class="sxs-lookup"><span data-stu-id="ff7c1-169">Removal of these limitations is being considered for a future release.</span></span>
