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
# <a name="value-conversions"></a>Conversioni di valori

I convertitori di valori consentono di convertire i valori delle proprietà durante la lettura o la scrittura nel database. Questa conversione può provenire da un valore a un altro dello stesso tipo (ad esempio, la crittografia di stringhe) o da un valore di un tipo a un valore di un altro tipo, ad esempio la conversione di valori enum da e verso stringhe nel database.

## <a name="fundamentals"></a>Nozioni di base

I convertitori di valori vengono specificati in termini di `ModelClrType` e `ProviderClrType` . Il tipo di modello è il tipo .NET della proprietà nel tipo di entità. Il tipo di provider è il tipo .NET riconosciuto dal provider di database. Ad esempio, per salvare le enumerazioni come stringhe nel database, il tipo di modello è il tipo dell'enumerazione e il tipo di provider è `String` . Questi due tipi possono essere gli stessi.

Le conversioni vengono definite utilizzando due `Func` alberi delle espressioni: uno da `ModelClrType` a `ProviderClrType` e l'altro da `ProviderClrType` a `ModelClrType` . Gli alberi delle espressioni vengono usati in modo che possano essere compilati nel codice di accesso al database per conversioni efficienti. Per le conversioni complesse, l'albero delle espressioni può essere una semplice chiamata a un metodo che esegue la conversione.

## <a name="configuring-a-value-converter"></a>Configurazione di un convertitore di valori

Le conversioni di valori sono definite nelle proprietà `OnModelCreating` del del `DbContext` . Si consideri, ad esempio, un'enumerazione e un tipo di entità definiti come segue:

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

È quindi possibile definire le conversioni in `OnModelCreating` per archiviare i valori enum come stringhe (ad esempio, "Donkey", "Mule",...) nel database:

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
> Un `null` valore non verrà mai passato a un convertitore di valori. In questo modo l'implementazione delle conversioni risulta più semplice e consente di condividerle tra proprietà Nullable e non nullable.

## <a name="the-valueconverter-class"></a>Classe ValueConverter

La chiamata a `HasConversion` come illustrato in precedenza creerà un' `ValueConverter` istanza e la imposterà sulla proprietà. L'oggetto `ValueConverter` può invece essere creato in modo esplicito. Ad esempio:

```csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Questa operazione può essere utile quando più proprietà utilizzano la stessa conversione.

> [!NOTE]
> Attualmente non è possibile specificare in un'unica posizione che ogni proprietà di un determinato tipo deve usare lo stesso convertitore di valori. Questa funzionalità verrà considerata per una versione futura.

## <a name="built-in-converters"></a>Convertitori predefiniti

EF Core viene fornito con un set di classi predefinite `ValueConverter` , disponibile nello `Microsoft.EntityFrameworkCore.Storage.ValueConversion` spazio dei nomi. Si tratta di:

* `BoolToZeroOneConverter` Da-bool a zero e uno
* `BoolToStringConverter` -Bool in stringhe quali "Y" e "N"
* `BoolToTwoValuesConverter` -Bool per due valori qualsiasi
* `BytesToStringConverter` -Matrice di byte in stringa con codifica Base64
* `CastingConverter` -Conversioni che richiedono solo un cast di tipo
* `CharToStringConverter` -Char a una stringa di caratteri singolo
* `DateTimeOffsetToBinaryConverter` Da-DateTimeOffset a valore a 64 bit con codifica binaria
* `DateTimeOffsetToBytesConverter` Da-DateTimeOffset a matrice di byte
* `DateTimeOffsetToStringConverter` Da-DateTimeOffset a stringa
* `DateTimeToBinaryConverter` Da-DateTime a 64 bit, incluso DateTimeKind
* `DateTimeToStringConverter` Da-DateTime a String
* `DateTimeToTicksConverter` -DateTime per i cicli
* `EnumToNumberConverter` -Enum al numero sottostante
* `EnumToStringConverter` -Enum in stringa
* `GuidToBytesConverter` -GUID a matrice di byte
* `GuidToStringConverter` -GUID a stringa
* `NumberToBytesConverter` -Qualsiasi valore numerico in una matrice di byte
* `NumberToStringConverter` -Qualsiasi valore numerico da stringa
* `StringToBytesConverter` -Da stringa a UTF8 byte
* `TimeSpanToStringConverter` Da-TimeSpan a stringa
* `TimeSpanToTicksConverter` -TimeSpan per i cicli

Si noti che `EnumToStringConverter` è incluso nell'elenco. Ciò significa che non è necessario specificare la conversione in modo esplicito, come illustrato in precedenza. Usare invece il convertitore predefinito:

```csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

Si noti che tutti i convertitori incorporati sono senza stato e pertanto una singola istanza può essere condivisa in modo sicuro da più proprietà.

## <a name="pre-defined-conversions"></a>Conversioni predefinite

Per le conversioni comuni per le quali esiste un convertitore incorporato, non è necessario specificare il convertitore in modo esplicito. Al contrario, è sufficiente configurare il tipo di provider da usare e EF userà automaticamente il convertitore predefinito appropriato. Le conversioni da enum a stringa vengono usate come esempio sopra, ma EF eseguirà questa operazione automaticamente se il tipo di provider è configurato:

```csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

La stessa operazione può essere eseguita specificando in modo esplicito il tipo di colonna. Ad esempio, se il tipo di entità è definito come segue:

```csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

I valori enum verranno quindi salvati come stringhe nel database senza ulteriori operazioni di configurazione in `OnModelCreating` .

## <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni note correnti del sistema di conversione dei valori:

* Come indicato in precedenza, `null` non è possibile convertire.
* Attualmente non è possibile distribuire una conversione di una proprietà in più colonne o viceversa.
* L'utilizzo di conversioni di valori può influisca sulla capacità di EF Core di tradurre espressioni in SQL. Per tali casi verrà registrato un avviso.
La rimozione di queste limitazioni verrà considerata per una versione futura.
