---
title: Conversioni di valori-EF Core
description: Configurazione di convertitori di valori in un modello di Entity Framework Core
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: d9d3753c7f0b257a2109e4af1f587df913c15b44
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983443"
---
# <a name="value-conversions"></a>Conversioni di valori

I convertitori di valori consentono di convertire i valori delle proprietà durante la lettura o la scrittura nel database. Questa conversione può provenire da un valore a un altro dello stesso tipo (ad esempio, la crittografia di stringhe) o da un valore di un tipo a un valore di un altro tipo, ad esempio la conversione di valori enum da e verso stringhe nel database.

> [!TIP]
> È possibile eseguire ed eseguire il debug in tutto il codice di questo documento [scaricando il codice di esempio da GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="overview"></a>Panoramica

I convertitori di valori vengono specificati in termini di `ModelClrType` e `ProviderClrType` . Il tipo di modello è il tipo .NET della proprietà nel tipo di entità. Il tipo di provider è il tipo .NET riconosciuto dal provider di database. Ad esempio, per salvare le enumerazioni come stringhe nel database, il tipo di modello è il tipo dell'enumerazione e il tipo di provider è `String` . Questi due tipi possono essere gli stessi.

Le conversioni vengono definite utilizzando due `Func` alberi delle espressioni: uno da `ModelClrType` a `ProviderClrType` e l'altro da `ProviderClrType` a `ModelClrType` . Gli alberi delle espressioni vengono utilizzati in modo che possano essere compilati nel delegato di accesso al database per conversioni efficienti. L'albero delle espressioni può contenere una semplice chiamata a un metodo di conversione per le conversioni complesse.

> [!NOTE]
> Una proprietà configurata per la conversione di valori può anche dover specificare un oggetto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Per ulteriori informazioni, vedere gli esempi riportati di seguito e la documentazione sugli [operatori di confronto dei valori](xref:core/modeling/value-comparers) .

## <a name="configuring-a-value-converter"></a>Configurazione di un convertitore di valori

Le conversioni di valori sono configurate in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> . Si consideri, ad esempio, un'enumerazione e un tipo di entità definiti come segue:

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

Le conversioni possono essere configurate in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> per archiviare i valori enum come stringhe, ad esempio "Donkey", "Mule" e così via nel database. è sufficiente fornire una funzione che converte da `ModelClrType` a `ProviderClrType` e un'altra per la conversione opposta:

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
> Un `null` valore non verrà mai passato a un convertitore di valori. Un valore null in una colonna del database è sempre un valore null nell'istanza dell'entità e viceversa. In questo modo l'implementazione delle conversioni risulta più semplice e consente di condividerle tra proprietà Nullable e non nullable. Per ulteriori informazioni, vedere il [problema GitHub #13850](https://github.com/dotnet/efcore/issues/13850) .

## <a name="pre-defined-conversions"></a>Conversioni predefinite

EF Core contiene molte conversioni predefinite che evitano la necessità di scrivere manualmente funzioni di conversione. Al contrario, EF Core selezionerà la conversione da utilizzare in base al tipo di proprietà nel modello e al tipo di provider di database richiesto.

Ad esempio, l'enumerazione per le conversioni di stringa viene utilizzata come esempio precedente, ma EF Core eseguirà questa operazione automaticamente quando il tipo di provider viene configurato come `string` utilizzando il tipo generico di <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :

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

La stessa operazione può essere eseguita specificando in modo esplicito il tipo di colonna del database. Ad esempio, se il tipo di entità è definito come segue:

### <a name="data-annotations"></a>[Annotazioni dei dati](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[API Fluent](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

I valori enum verranno quindi salvati come stringhe nel database senza ulteriori operazioni di configurazione in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .

## <a name="the-valueconverter-class"></a>Classe ValueConverter

La chiamata a <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> come illustrato in precedenza creerà un' <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> istanza e la imposterà sulla proprietà. L'oggetto `ValueConverter` può invece essere creato in modo esplicito. Ad esempio:

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

Questa operazione può essere utile quando più proprietà utilizzano la stessa conversione.

## <a name="built-in-converters"></a>Convertitori predefiniti

Come indicato in precedenza, EF Core viene fornito con un set di <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classi predefinite, disponibile nello <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> spazio dei nomi. In molti casi EF sceglierà il convertitore predefinito appropriato in base al tipo di proprietà nel modello e al tipo richiesto nel database, come illustrato in precedenza per le enumerazioni. Se, ad esempio, `.HasConversion<int>()` si utilizza su una `bool` proprietà, EF Core convertirà i valori bool in numerici zero e un valore:

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

Questa operazione è analoga a quella della creazione di un'istanza di predefinita <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> e della relativa impostazione in modo esplicito:

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

Nella tabella seguente sono riepilogate le conversioni predefinite comunemente utilizzate dai tipi di modello/proprietà ai tipi di provider di database. Nella tabella `any_numeric_type` significa uno tra,,, `int` `short` `long` `byte` , `uint` , `ushort` , `ulong` , `sbyte` , `char` , `decimal` , `float` o `double` .

| Tipo di proprietà/modello | Tipo di provider/database | Conversione                                                | Utilizzo
|:--------------------|------------------------|-----------------------------------------------------------|------
| bool                | any_numeric_type       | False/true a 0/1                                         | `.HasConversion<any_numeric_type>()`
|                     | any_numeric_type       | False/true per due numeri qualsiasi                             | Utilizzare <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601>.
|                     | string                 | False/true in "Y"/"N"                                     | `.HasConversion<string>()`
|                     | string                 | False/true per due stringhe qualsiasi                             | Utilizzare <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter>.
| any_numeric_type    | bool                   | da 0/1 a false/true                                         | `.HasConversion<bool>()`
|                     | any_numeric_type       | Cast semplice                                               | `.HasConversion<any_numeric_type>()`
|                     | string                 | Numero sotto forma di stringa                                    | `.HasConversion<string>()`
| Enumerazione                | any_numeric_type       | Valore numerico dell'enumerazione                             | `.HasConversion<any_numeric_type>()`
|                     | string                 | Rappresentazione di stringa del valore enum               | `.HasConversion<string>()`
| string              | bool                   | Analizza la stringa come bool                               | `.HasConversion<bool>()`
|                     | any_numeric_type       | Analizza la stringa come tipo numerico specificato               | `.HasConversion<any_numeric_type>()`
|                     | char                   | Primo carattere della stringa                         | `.HasConversion<char>()`
|                     | Datetime               | Analizza la stringa come DateTime                           | `.HasConversion<DateTime>()`
|                     | DateTimeOffset         | Analizza la stringa come DateTimeOffset                     | `.HasConversion<DateTimeOffset>()`
|                     | TimeSpan               | Analizza la stringa come TimeSpan                           | `.HasConversion<TimeSpan>()`
|                     | Guid                   | Analizza la stringa come GUID                               | `.HasConversion<Guid>()`
|                     | byte[]                 | Stringa come byte UTF8                                  | `.HasConversion<byte[]>()`
| char                | string                 | Una singola stringa di caratteri                                 | `.HasConversion<string>()`
| Datetime            | long                   | Data/ora codificata che conserva DateTime. Kind                | `.HasConversion<long>()`
|                     | long                   | Tick                                                     | Utilizzare <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter>.
|                     | string                 | Stringa di data/ora della lingua inglese                        | `.HasConversion<string>()`
| DateTimeOffset      | long                   | Data/ora codificata con offset                             | `.HasConversion<long>()`
|                     | string                 | Stringa di data/ora della lingua inglese con offset            | `.HasConversion<string>()`
| TimeSpan            | long                   | Tick                                                     | `.HasConversion<long>()`
|                     | string                 | Stringa dell'intervallo di tempo della lingua inglese                        | `.HasConversion<string>()`
| Uri                 | string                 | URI sotto forma di stringa                                       | `.HasConversion<string>()`
| PhysicalAddress     | string                 | Indirizzo come stringa                                   | `.HasConversion<string>()`
|                     | byte[]                 | Byte in ordine di rete big-endian                         | `.HasConversion<byte[]>()`
| IPAddress           | string                 | Indirizzo come stringa                                   | `.HasConversion<string>()`
|                     | byte[]                 | Byte in ordine di rete big-endian                         | `.HasConversion<byte[]>()`
| Guid                | string                 | Il GUID nel formato ' dddddddd-dddd-dddd-dddd-dddddddddddd ' | `.HasConversion<string>()`
|                     | byte[]                 | Byte nell'ordine di serializzazione binaria .NET                  | `.HasConversion<byte[]>()`

Si noti che queste conversioni presuppongono che il formato del valore sia appropriato per la conversione. La conversione di stringhe in numeri, ad esempio, avrà esito negativo se i valori della stringa non possono essere analizzati come numeri.

L'elenco completo dei convertitori predefiniti è:

* Conversione delle proprietà bool:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool in stringhe quali "Y" e "N"
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool per due valori qualsiasi
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> Da-bool a zero e uno
* Conversione delle proprietà della matrice di byte:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Matrice di byte in stringa con codifica Base64
* Qualsiasi conversione che richiede solo un cast di tipo
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Conversioni che richiedono solo un cast di tipo
* Conversione di proprietà Char:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char a una stringa di caratteri singolo
* Conversione delle <xref:System.DateTimeOffset> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> a valore a 64 bit con codifica binaria
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> alla matrice di byte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> in stringa
* Conversione delle <xref:System.DateTime> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> a un valore a 64 bit incluso DateTimeKind
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> in stringa
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> per eseguire il ciclo
* Conversione delle proprietà enum:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enum al numero sottostante
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum in stringa
* Conversione delle <xref:System.Guid> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> alla matrice di byte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> in stringa
* Conversione delle <xref:System.Net.IPAddress> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> alla matrice di byte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> in stringa
* Conversione delle proprietà numeriche (int, Double, Decimal e così via):
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Qualsiasi valore numerico in una matrice di byte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Qualsiasi valore numerico da stringa
* Conversione delle <xref:System.Net.NetworkInformation.PhysicalAddress> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> alla matrice di byte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> in stringa
* Conversione delle proprietà di stringa:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Stringhe quali "Y" e "N" a bool
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Da stringa a UTF8 byte
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Da stringa a carattere
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> -Stringa a <xref:System.DateTime>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> -Stringa a <xref:System.DateTimeOffset>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Stringa da enumerare
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> -Stringa a <xref:System.Guid>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Stringa in tipo numerico
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> -Stringa a <xref:System.TimeSpan>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> -Stringa a <xref:System.Uri>
* Conversione delle <xref:System.TimeSpan> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> in stringa
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> per eseguire il ciclo
* Conversione delle <xref:System.Uri> proprietà:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> in stringa

Si noti che tutti i convertitori incorporati sono senza stato e pertanto una singola istanza può essere condivisa in modo sicuro da più proprietà.

## <a name="column-facets-and-mapping-hints"></a>Facet di colonna e hint di mapping

Alcuni tipi di database hanno facet che modificano il modo in cui vengono archiviati i dati. Tra queste sono incluse:

* Precisione e scala per i numeri decimali e le colonne di data/ora
* Dimensioni/lunghezza per le colonne binarie e stringa
* Unicode per le colonne di tipo stringa

Questi facet possono essere configurati in modo normale per una proprietà che utilizza un convertitore di valori e verranno applicati al tipo di database convertito. Ad esempio, quando si esegue la conversione da enum a Strings, è possibile specificare che la colonna di database non deve essere Unicode e archiviare un massimo di 20 caratteri:

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

In alternativa, quando si crea il convertitore in modo esplicito:

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

In questo modo si ottiene una `varchar(20)` colonna quando si utilizzano migrazioni di EF core rispetto SQL Server:

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

Tuttavia, se per impostazione predefinita tutte le `EquineBeast` colonne devono essere `varchar(20)` , queste informazioni possono essere assegnate al convertitore di valori come <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> . Ad esempio:

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

A questo punto, ogni volta che si utilizza questo convertitore, la colonna del database sarà non Unicode con una lunghezza massima di 20. Tuttavia, si tratta solo di hint perché vengono sottoposti a override da qualsiasi facet impostato in modo esplicito sulla proprietà mappata.

## <a name="examples"></a>Esempio

### <a name="simple-value-objects"></a>Oggetti valore semplice

In questo esempio viene usato un tipo semplice per eseguire il wrapping di un tipo primitivo. Questa operazione può essere utile quando si desidera che il tipo nel modello sia più specifico (e pertanto più indipendente dai tipi) rispetto a un tipo primitivo. In questo esempio, il tipo è `Dollars` , che esegue il wrapping della primitiva decimale:

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

Può essere usato in un tipo di entità:

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

E vengono convertiti nell'oggetto sottostante `decimal` quando vengono archiviati nel database:

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> Questo oggetto valore viene implementato come struct di sola [lettura](/dotnet/csharp/language-reference/builtin-types/struct). Ciò significa che EF Core possibile creare snapshot e confrontare i valori senza problemi. Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .

### <a name="composite-value-objects"></a>Oggetti valore composito

Nell'esempio precedente, il tipo di oggetto valore conteneva solo una singola proprietà. È più comune per un tipo di oggetto valore comporre più proprietà che insieme formano un concetto di dominio. Ad esempio, un `Money` tipo generale che contiene sia la quantità che la valuta:

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

Questo oggetto valore può essere utilizzato in un tipo di entità come prima:

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

I convertitori di valori possono attualmente convertire solo i valori da e verso una singola colonna di database. Questa limitazione indica che tutti i valori delle proprietà dell'oggetto devono essere codificati in un unico valore di colonna. Questa operazione viene in genere gestita serializzando l'oggetto mentre viene inserito nel database, quindi deserializzarlo di nuovo in uscita. Ad esempio, utilizzando <xref:System.Text.Json> :

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> Si prevede di consentire il mapping di un oggetto a più colonne nel EF Core 6,0, eliminando la necessità di usare la serializzazione qui. Questa operazione viene rilevata dal [problema GitHub #13947](https://github.com/dotnet/efcore/issues/13947).

> [!NOTE]
> Come nell'esempio precedente, questo oggetto valore viene implementato come struct di sola [lettura](/dotnet/csharp/language-reference/builtin-types/struct). Ciò significa che EF Core possibile creare snapshot e confrontare i valori senza problemi. Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .

### <a name="collections-of-primitives"></a>Raccolte di primitive

La serializzazione può essere usata anche per archiviare una raccolta di valori primitivi. Ad esempio:

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

Utilizzando di <xref:System.Text.Json> nuovo:

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

`ICollection<string>` rappresenta un tipo di riferimento modificabile. Ciò significa che <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> è necessario un oggetto affinché EF core possa rilevare e rilevare correttamente le modifiche. Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .

### <a name="collections-of-value-objects"></a>Raccolte di oggetti valore

Combinando i due esempi precedenti è possibile creare una raccolta di oggetti valore. Si consideri, ad esempio, un `AnnualFinance` tipo che modella le finanze del Blog per un singolo anno:

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

Questo tipo compone diversi `Money` tipi creati in precedenza:

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

È quindi possibile aggiungere una raccolta di `AnnualFinance` al tipo di entità:

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

E usare di nuovo la serializzazione per archiviare questo:

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
> Come in precedenza, questa conversione richiede un oggetto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Per ulteriori informazioni, vedere [operatori di confronto dei valori](xref:core/modeling/value-comparers) .

### <a name="value-objects-as-keys"></a>Oggetti valore come chiavi

A volte le proprietà di chiave primitive possono essere incapsulate in oggetti valore per aggiungere un livello aggiuntivo di indipendenza dai tipi nell'assegnazione di valori. Ad esempio, è possibile implementare un tipo di chiave per i Blog e un tipo di chiave per i post:

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

Questi possono quindi essere utilizzati nel modello di dominio:

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

Si noti che `Blog.Id` non è possibile assegnare accidentalmente un oggetto `PostKey` e `Post.Id` non è possibile assegnargli un errore `BlogKey` . Analogamente, la `Post.BlogId` proprietà della chiave esterna deve essere assegnata a `BlogKey` .

> [!NOTE]
> La visualizzazione di questo modello non significa che sia consigliabile. Valutare attentamente se questo livello di astrazione sta aiutando o ostacolare l'esperienza di sviluppo. Si consiglia inoltre di utilizzare le operazioni di navigazione e le chiavi generate anziché gestire direttamente i valori di chiave.

È quindi possibile eseguire il mapping di queste proprietà chiave utilizzando convertitori di valori:

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
> Attualmente le proprietà chiave con conversioni non possono utilizzare valori di chiave generati. Votare per il [problema GitHub #11597](https://github.com/dotnet/efcore/issues/11597) per rimuovere questa limitazione.

### <a name="use-ulong-for-timestamprowversion"></a>Usare ULONG per timestamp/rowversion

SQL Server supporta la [concorrenza ottimistica](xref:core/saving/concurrency) automatica con [ `rowversion` / `timestamp` colonne binarie a 8 byte](/sql/t-sql/data-types/rowversion-transact-sql). Questi vengono sempre letti e scritti nel database usando una matrice a 8 byte. Tuttavia, le matrici di byte sono un tipo di riferimento modificabile, che li rende piuttosto penosi da gestire. I convertitori di valori consentono invece di eseguire il `rowversion` mapping a una `ulong` proprietà, che è molto più appropriata e facile da utilizzare rispetto alla matrice di byte. Si consideri ad esempio un' `Blog` entità con un token di concorrenza ULONG:

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

È possibile eseguirne il mapping a una colonna di SQL Server `rowversion` usando un convertitore di valori:

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a>Specificare DateTime. Kind durante la lettura delle date

SQL Server Elimina il <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag quando si archivia un oggetto <xref:System.DateTime> come [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) o [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) . Ciò significa che i valori DateTime restituiti dal database hanno sempre un <xref:System.DateTimeKind> di `Unspecified` .

I convertitori di valori possono essere utilizzati in due modi per gestire questo problema. In primo luogo, EF Core dispone di un convertitore di valori che consente di creare un valore opaco a 8 byte che conserva il `Kind` flag. Ad esempio:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

In questo modo è possibile combinare i valori DateTime con `Kind` flag diversi nel database.

Il problema di questo approccio è che il database non dispone più di colonne riconoscibili `datetime` o `datetime2` . Quindi, invece, è normale archiviare sempre l'ora UTC (o, meno comunemente, sempre l'ora locale) e quindi ignorare il `Kind` flag o impostarlo sul valore appropriato usando un convertitore di valori. Il convertitore seguente, ad esempio, assicura che il `DateTime` valore letto dal database disponga di <xref:System.DateTimeKind> `UTC` :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

Se viene impostata una combinazione di valori locali e UTC nelle istanze di entità, il convertitore può essere usato per eseguire la conversione in modo appropriato prima dell'inserimento. Ad esempio:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> Prendere in considerazione la possibilità di unificare tutto il codice di accesso al database in modo da utilizzare l'ora UTC per sempre, occupandosi dell'ora locale quando si presentano dati agli utenti.

### <a name="use-case-insensitive-string-keys"></a>Usare chiavi di stringa senza distinzione tra maiuscole e minuscole

Per impostazione predefinita, alcuni database, tra cui SQL Server, eseguono confronti di stringhe senza distinzione tra maiuscole e minuscole. .NET, d'altra parte, esegue confronti tra stringhe con distinzione tra maiuscole e minuscole per impostazione predefinita. Ciò significa che un valore di chiave esterna, ad esempio "DotNet", corrisponderà al valore di chiave primaria "DotNet" in SQL Server, ma non corrisponderà al EF Core. Un operatore di confronto dei valori per le chiavi può essere utilizzato per forzare EF Core nei confronti di stringhe senza distinzione tra maiuscole e minuscole come nel database. Si consideri, ad esempio, un modello di Blog/post con chiavi di stringa:

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

Questa operazione non funzionerà come previsto se alcuni `Post.BlogId` valori hanno maiuscole e minuscole diverse. Gli errori causati da questa funzione dipendono da ciò che l'applicazione sta eseguendo, ma in genere coinvolgono grafici di oggetti che non sono [corretti](xref:core/change-tracking/relationship-changes) correttamente e/o aggiornamenti che non riescono perché il valore FK non è corretto. Per risolvere il problema, è possibile usare un operatore di confronto del valore:

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
> I confronti tra stringhe .NET e i confronti di stringhe di database possono variare in modo diverso rispetto a maiuscole Questo modello funziona per semplici chiavi ASCII, ma potrebbe non riuscire a usare chiavi con qualsiasi tipo di caratteri specifici delle impostazioni cultura. Per ulteriori informazioni, vedere [regole di confronto e distinzione tra maiuscole e minuscole](xref:core/miscellaneous/collations-and-case-sensitivity) .

### <a name="handle-fixed-length-database-strings"></a>Gestire le stringhe di database a lunghezza fissa

Nell'esempio precedente non è necessario un convertitore di valori. Un convertitore può tuttavia essere utile per i tipi di stringa di database a lunghezza fissa, ad esempio `char(20)` o `nchar(20)` . Le stringhe a lunghezza fissa vengono riempite fino alla loro lunghezza intera ogni volta che viene inserito un valore nel database. Ciò significa che un valore chiave di " `dotnet` " verrà letto dal database come " `dotnet..............` ", dove `.` rappresenta uno spazio. Questa operazione non verrà quindi confrontata correttamente con i valori di chiave non riempiti.

Un convertitore di valori può essere usato per tagliare la spaziatura interna durante la lettura dei valori di chiave. Questo può essere combinato con l'operatore di confronto del valore nell'esempio precedente per confrontare correttamente le chiavi ASCII senza distinzione tra maiuscole e minuscole a lunghezza fissa. Ad esempio:

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

### <a name="encrypt-property-values"></a>Crittografa valori proprietà

I convertitori di valori possono essere usati per crittografare i valori delle proprietà prima di inviarli al database e quindi decrittografarli nel modo in cui vengono decrittografati. Ad esempio, usando l'inversione di stringa come sostituto di un algoritmo di crittografia reale:

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> Attualmente non è possibile ottenere un riferimento al DbContext corrente o a un altro stato della sessione dall'interno di un convertitore di valori. Questo limita i tipi di crittografia che è possibile usare. Votare per il [problema GitHub #11597](https://github.com/dotnet/efcore/issues/12205) per rimuovere questa limitazione.

> [!WARNING]
> Assicurarsi di comprendere tutte le implicazioni se si esegue il rollup della propria crittografia per proteggere i dati sensibili. Si consiglia invece di utilizzare meccanismi di crittografia predefiniti, ad esempio [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) SQL Server.

## <a name="limitations"></a>Limitazioni

Esistono alcune limitazioni note correnti del sistema di conversione dei valori:

* Attualmente non è possibile specificare in un'unica posizione che ogni proprietà di un determinato tipo deve usare lo stesso convertitore di valori. Votare ( 👍 ) per il [problema di GitHub #10784](https://github.com/dotnet/efcore/issues/10784) se si tratta di un elemento necessario.
* Come indicato in precedenza, `null` non è possibile convertire. Votare ( 👍 ) per il [problema di GitHub #13850](https://github.com/dotnet/efcore/issues/13850) se si tratta di un elemento necessario.
* Attualmente non è possibile distribuire una conversione di una proprietà in più colonne o viceversa. Votare ( 👍 ) per il [problema di GitHub #13947](https://github.com/dotnet/efcore/issues/13947) se si tratta di un elemento necessario.
* La generazione di valori non è supportata per la maggior parte delle chiavi mappate tramite convertitori di valori. Votare ( 👍 ) per il [problema di GitHub #11597](https://github.com/dotnet/efcore/issues/11597) se si tratta di un elemento necessario.
* Le conversioni di valori non possono fare riferimento all'istanza corrente di DbContext. Votare ( 👍 ) per il [problema di GitHub #11597](https://github.com/dotnet/efcore/issues/12205) se si tratta di un elemento necessario.

La rimozione di queste limitazioni verrà presa in considerazione per le versioni future.
